---
title: 'Omnidirectional Perception System'
subtitle: 'ROS2 Humble, Raspberry Pi, OpenCV, Tesseract OCR, Python, SQLite'
date: 2026-03-31 12:00:00 +0000
description: Built a multi-camera warehouse scanning solution for CEVA Logistics that fuses OCR and barcode data across all six faces of a package to generate a Universal Product Passport in real time — eliminating single-point-of-failure barcode dependency.
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

When a barcode is obscured, damaged, or missing in a warehouse, the item loses its identity — triggering manual intervention and breaking traceability. For CEVA Logistics, we built an autonomous vision system that scans all six faces of any package and fuses OCR text and barcode data into a **Universal Product Passport**, so identification never depends on a single signal being readable.

**My role:** Technical Lead (Software) — system architecture, all five ROS2 nodes, SmartMatcher scoring engine, SQLite integration, and end-to-end testing across 43 scenarios.

| Resilience | Efficiency | Accuracy |
|:----------:|:----------:|:--------:|
| ✅ Works when barcode fails | ✅ ≤ 3s end-to-end | ✅ Up to 98.7% match |

🔗 [View Full Code on GitHub →](https://github.com/YongJiee/Omnidirectional-Perception-System)

<iframe src="https://www.youtube.com/embed/YOUR_VIDEO_ID" width="640" height="360" frameborder="0" allowfullscreen></iframe>

---

## Project Highlights

- Scans all **6 faces** of any package using 3× IMX708 cameras across inbound and sorting modes
- **OCR + barcode fusion** — system identifies items even when barcode is fully missing or damaged
- **43/43 test scenarios PASS** — 10 inbound, 8 sorting, 25 resilience
- End-to-end scan time of **~2.1–2.9s** across all scenarios
- Live **Universal Product Passport** written to SQLite, visible in DB Browser during demo
- Industry partner: **CEVA Logistics**

---

## Hardware Integration

<div style="display: flex; align-items: center; justify-content: space-between;">
  <div style="width: 45%;">
    <h3 style="font-size: 1.6em;">Hardware Used:</h3>
    <ul style="font-size: 1.1em;">
      <li>Raspberry Pi 4 (Bookworm)</li>
      <li>3× IMX708 Cameras</li>
      <li>Arducam Multi Camera Adapter V2.2</li>
      <li>Direct Ethernet (Pi ↔ WSL Ubuntu)</li>
      <li>Robotic Arm with End-Effector Camera</li>
    </ul>
  </div>

  <figure style="width: 45%; text-align: center; font-style: italic;">
    <img src="/images/Projects/ProjectOPS/hardware_setup.jpg" style="width: 85%;" class="img-fluid rounded">
    <p>OPS hardware setup — Pi + Arducam mux + 3 cameras</p>
  </figure>
</div>

<div style="clear: both;"></div>

---

## System Architecture

The system runs across two machines — a Raspberry Pi 4 handling all camera capture, and a WSL Ubuntu host running the ROS2 processing pipeline, connected over direct ethernet with no internet.

<img src="/images/Projects/ProjectOPS/architecture.png" style="width: 90%; margin-bottom: 10px; border-radius: 8px;">

<div style="display: flex; flex-wrap: wrap; gap: 16px; margin-top: 12px;">

  <div style="flex: 1 1 280px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Camera Capture</h4>
    <p><strong>Runs on:</strong> Raspberry Pi 4</p>
    <p><strong>Node:</strong> <code>multi_camera_publisher</code></p>
    <p><strong>Tool:</strong> Picamera2, Arducam mux</p>
  </div>

  <div style="flex: 1 1 280px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>OCR + Barcode</h4>
    <p><strong>Runs on:</strong> WSL Ubuntu</p>
    <p><strong>Node:</strong> <code>ocr_node</code></p>
    <p><strong>Tool:</strong> Tesseract PSM 11, pyzbar, OpenCV</p>
  </div>

  <div style="flex: 1 1 280px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>SmartMatcher</h4>
    <p><strong>Runs on:</strong> WSL Ubuntu</p>
    <p><strong>Node:</strong> <code>database_matcher_node</code></p>
    <p><strong>Tool:</strong> thefuzz, custom scoring engine</p>
  </div>

  <div style="flex: 1 1 280px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Universal Product Passport</h4>
    <p><strong>Runs on:</strong> WSL Ubuntu</p>
    <p><strong>Node:</strong> <code>database_manager</code></p>
    <p><strong>Tool:</strong> SQLite</p>
  </div>

  <div style="flex: 1 1 280px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Robot Command</h4>
    <p><strong>Runs on:</strong> WSL Ubuntu</p>
    <p><strong>Node:</strong> <code>robot_command_node</code></p>
    <p><strong>Tool:</strong> ROS2 topic <code>/robot_command</code></p>
  </div>

  <div style="flex: 1 1 280px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Clock Synchronisation</h4>
    <p><strong>Runs on:</strong> WSL Ubuntu</p>
    <p><strong>Approach:</strong> Autonomous offset measurement per session (no NTP on isolated ethernet)</p>
  </div>

</div>

---

## Camera Coverage — All 6 Faces

<div style="display: flex; gap: 20px; margin-top: 10px;">
  <img src="/images/Projects/ProjectOPS/inbound_demo.gif" style="width: 48%; border-radius: 8px;">
  <img src="/images/Projects/ProjectOPS/sorting_demo.gif" style="width: 48%; border-radius: 8px;">
</div>

<div style="display: flex; gap: 20px; margin-top: 8px; font-style: italic; font-size: 0.9em;">
  <p style="width: 48%; text-align: center;">Inbound mode — top + side faces scanned</p>
  <p style="width: 48%; text-align: center;">Sorting mode — bottom + side + end-effector</p>
</div>

- **Cam0 + Cam2** at 45° → side faces
- **Cam1** top-down → inbound scans TOP face; sorting: arm holds box underneath so Cam1 scans BOTTOM
- **Arm end-effector camera** → scans TOP before pick-up in sorting mode

---

## Technical Challenges & Solutions

**Barcode single point of failure**  
**Solution:** Multi-signal fusion (OCR + barcode) across all six faces. OCR fallback achieves up to 98.7% match accuracy with no barcode at all.

**Clock sync on isolated ethernet**  
**Challenge:** Pi and WSL on direct ethernet — no internet, NTP not viable.  
**Solution:** Autonomous clock offset measurement on the first image of each session (`WSL receive time − Pi cam_start timestamp`). Typical offset: 0.5–0.8s. Passed via JSON to the matcher node for accurate end-to-end timing.

**Sequential capture hardware constraint**  
**Challenge:** Arducam mux forces sequential camera switching via I²C — `ThreadPoolExecutor` ruled out.  
**Solution:** Optimised per-camera latency — focus delay 0.2s → 0.05s, removed local disk save, removed redundant RGB→BGR conversion. Pi cycle time: ~2.1–2.5s for 3 cameras.

**Ambiguous product identification**  
**Challenge:** Products with similar names (e.g. Cream Lip Gloss across two brands) score near-identically without a barcode.  
**Solution:** SmartMatcher `check_tie()` detects ambiguous results and flags them. System correctly rejects low-confidence matches rather than guessing.

---

## SmartMatcher — Scoring Logic

Rather than a simple string match, SmartMatcher dynamically selects a scoring formula based on database contents and available signals:

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

---

## Results & Performance

### 43 / 43 Test Scenarios — PASS

| Test Group | Scenarios | Outcome |
|------------|-----------|---------|
| Inbound scanning | 10 | ✅ All ≤ 3.0s |
| Sorting mode | 8 | ✅ All ≤ 2.7s |
| Resilience (OCR/barcode degradation) | 25 | ✅ All PASS |

### Resilience Findings

- **Brand integrity is critical** — brand text corruption drops score to ~93% (FAIL)
- **Product corruption is tolerated** — if brand is intact, score stays ~95–96% (PASS)
- **OCR-safe products** (no barcode needed): Cream Lip Stain (98.5%), Lip Butter Balm (98.7%)
- **Barcode-required**: Cream Lip Gloss — ambiguous across two brands, correctly fails without barcode
- **Real-world camera noise** — 100% ignored via `OCR_NOISE_WORDS` filter

---

## Demonstration

This video showcases OPS scanning packages in both inbound and sorting modes, with live Universal Product Passport entries appearing in DB Browser as each scan completes.

<iframe src="https://www.youtube.com/embed/YOUR_VIDEO_ID" width="640" height="360" frameborder="0" allowfullscreen></iframe>