---
title: 'Omnidirectional Perception System'
subtitle: 'ROS2, Raspberry Pi, OpenCV, Tesseract OCR, Python, SQLite'
date: 2026-03-31 12:00:00 +0000
slug: omnidirectional-perception-system
description: A multi-camera warehouse scanning system that generates a Universal Product Passport for every package - even when barcodes fail - by fusing OCR and barcode signals across all six faces in under 3 seconds. Built for CEVA Logistics.
featured_image: '/images/Projects/ProjectOPS/OPS_demo.gif'
---

<div class="tag-container">
  <span>ROS2</span>
  <span>Raspberry Pi</span>
  <span>OpenCV</span>
  <span>Tesseract OCR</span>
  <span>Python</span>
  <span>SQLite</span>
</div>

## Warehouse barcode scanning has a single point of failure. We eliminated it.

When a barcode is obscured, damaged, or missing in a warehouse, the item loses its identity — triggering manual intervention and breaking traceability. For CEVA Logistics, we built an autonomous vision system that scans all six faces of any package and fuses OCR text + barcode data into a **Universal Product Passport**, so identification never depends on a single signal being readable.

**My role:** Technical Lead (Software) — system architecture, all five ROS2 nodes, SmartMatcher scoring engine, SQLite integration, and end-to-end testing across 43 scenarios.

| Resilience | Efficiency | Accuracy |
|:----------:|:----------:|:--------:|
| ✅ Works when barcode fails | ✅ ≤ 3s end-to-end | ✅ Up to 98.7% match |

🔗 [View Full Code on GitHub →](https://github.com/YongJiee/Omnidirectional-Perception-System)

---

## Watch It In Action

<video width="100%" controls>
    <source src="/images/Projects/ProjectOPS/OPS_demo.mp4" type="video/mp4">
</video>

*OPS scanning a package and writing a live Universal Product Passport entry to the SQLite database*

---

## System Architecture

The system runs across two machines — a Raspberry Pi 4 handling all camera capture, and a WSL Ubuntu host running the ROS2 processing pipeline. They communicate over direct ethernet with no internet connection, which ruled out NTP and required a custom clock sync solution.

![OPS Architecture Diagram](/images/Projects/ProjectOPS/architecture.png)

*Pi (192.168.10.2) → ROS2 over ethernet → WSL (192.168.10.1) → OCR + match + database + robot command*

**Node breakdown:**

| Node | Runs on | Responsibility |
|------|---------|----------------|
| `multi_camera_publisher` | Pi | Captures 3× IMX708 via Arducam mux, publishes `/image_raw` |
| `ocr_node` | WSL | Tesseract PSM 11 + pyzbar per face, publishes `/ocr_result` |
| `database_matcher_node` | WSL | SmartMatcher scoring, clock sync, publishes `/match_result` |
| `database_manager` | WSL | Writes Universal Product Passport to SQLite |
| `robot_command_node` | WSL | Sends pass/fail to arm via `/robot_command`, triggers next scan |

---

## What I Built

### Camera coverage — all 6 faces, two modes

- **Cam0 + Cam2** at 45° → side faces
- **Cam1** top-down → inbound: scans TOP face; sorting: arm holds box underneath so Cam1 scans BOTTOM
- **Arm end-effector camera** → scans TOP before pick-up (sorting mode only)

### SmartMatcher — fuzzy scoring engine

Rather than a simple string match, SmartMatcher dynamically selects a scoring formula based on what's in the database and what signals were read:

```python
def _calculate_accuracy(self, ocr_tokens, candidate):
    brand_score   = self._enhanced_fuzzy_match(ocr_tokens, candidate['brand'])
    product_score = self._enhanced_fuzzy_match(ocr_tokens, candidate['product_name'])
    keyword_bonus = self._keyword_bonus(ocr_tokens, candidate)

    if self._is_unique_product(candidate):
        return product_score * 0.9 + keyword_bonus      # brand not needed
    elif brand_score == 0:
        return brand_score * 0.6                        # brand-only fallback
    else:
        return brand_score * 0.4 + product_score * 0.5 + keyword_bonus
```

Barcode exact match bypasses scoring entirely with a sentinel score of `200.0`.

### Autonomous clock synchronisation

Pi and WSL are on an isolated ethernet link — no internet, no NTP. Rather than accept timing inaccuracy, I measured the clock offset autonomously at the start of each session:

```python
if self.clock_offset is None:
    wsl_receive_time  = time.time()
    pi_cam_start      = msg_data['cam_start']
    self.clock_offset = wsl_receive_time - pi_cam_start
    self.get_logger().info(f'Clock offset: {self.clock_offset:.3f}s')
```

This offset is passed via JSON payload to `database_matcher_node`, which applies it to `overall_start` for accurate end-to-end timing. Typical offset: 0.5–0.8s per session.

### Race condition prevention

With multiple camera feeds resolving asynchronously, a `_fuse_triggered` flag prevents the matcher from double-publishing:

```python
if self._fuse_triggered:
    return
self._fuse_triggered = True
self._publish_result(best_match)
```

---

## Key Engineering Decisions

### Why fuzzy matching instead of a trained ML classifier?

A classifier would require labelled training data for every new product added to the warehouse. Fuzzy matching with a curated noise filter (`OCR_NOISE_WORDS`) generalises to new products immediately — the database is the only thing that needs updating. Given CEVA's constantly rotating product inventory, this was the pragmatic choice over retraining overhead.

### Why sequential camera capture?

The Arducam multiplexer switches cameras via I²C — it physically cannot expose more than one sensor at a time. `ThreadPoolExecutor` for parallel capture was tested and ruled out. Instead, I squeezed latency out of each capture: focus delay reduced from 0.2s → 0.05s, local disk save removed, and redundant RGB→BGR conversion eliminated. Net result: Pi cycle time of ~2.1–2.5s for 3 cameras.

---

## Results & Performance

### 43 / 43 test scenarios — PASS

| Test Group | Scenarios | Outcome |
|------------|-----------|---------|
| Inbound scanning | 10 | ✅ All ≤ 3.0s |
| Sorting mode | 8 | ✅ All ≤ 2.7s |
| Resilience (OCR/barcode degradation) | 25 | ✅ All PASS |

### Resilience findings

- **Brand integrity is critical** — brand text corruption drops score to ~93% (FAIL)
- **Product corruption is tolerated** — if brand is intact, score stays ~95–96% (PASS)
- **OCR-safe products** (no barcode needed): Cream Lip Stain (98.5%), Lip Butter Balm (98.7%)
- **Barcode-required products**: Cream Lip Gloss — ambiguous across two brands, correctly fails without barcode
- **Real-world camera noise** — 100% ignored via `OCR_NOISE_WORDS` filter

### System specifications

| Component | Detail |
|-----------|--------|
| Platform | Raspberry Pi 4 (Bookworm) |
| Cameras | 3× IMX708, Arducam Multi Camera Adapter V2.2 |
| Communication | Direct ethernet, no internet |
| Framework | ROS2 Humble |
| OCR | Tesseract PSM 11 |
| Barcode | pyzbar |
| Matching | thefuzz (SmartMatcher) |
| Database | SQLite |
| Language | Python 3.10 |
| Industry partner | CEVA Logistics |

---

## Additional Demo Scenarios

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(320px, 1fr)); gap: 30px; margin: 40px 0;">
    <div style="text-align: center;">
        <img src="/images/Projects/ProjectOPS/inbound_demo.gif" style="width: 100%; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">
        <p style="margin-top: 15px; font-weight: bold;">Inbound mode</p>
        <p style="margin-top: 5px; color: #666; font-size: 0.9em;">Top + side faces · live database update</p>
    </div>
    <div style="text-align: center;">
        <img src="/images/Projects/ProjectOPS/sorting_demo.gif" style="width: 100%; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">
        <p style="margin-top: 15px; font-weight: bold;">Sorting mode</p>
        <p style="margin-top: 5px; color: #666; font-size: 0.9em;">Bottom + side + end-effector · pass/fail to arm</p>
    </div>
</div>

---

## What I Learned

The most valuable lesson wasn't a technology — it was learning to make decisions under real constraints rather than ideal ones.

**Clock sync without NTP** forced me to think about what I actually needed (accurate relative timing per session) versus what I assumed I needed (wall-clock sync). The autonomous offset measurement solved the real problem in ~10 lines.

**Fuzzy matching vs ML** came down to a build-vs-maintain framing. A classifier would have been more interesting to build, but fuzzy matching with a well-tuned noise filter was the right answer for an inventory that changes constantly. Choosing the less impressive solution because it fits the problem better is a real engineering call.

**Designing for failure** — `quantity_source='flagged'`, tie detection, `OCR_NOISE_WORDS`, brand-integrity gating — the most important software work wasn't in the happy path. It was in deciding what to do when things go wrong, and making those decisions explicit rather than silent.