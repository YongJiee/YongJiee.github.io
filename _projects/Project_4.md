---
title: 'Omnidirectional Perception System'
subtitle: 'ROS2 Humble, Raspberry Pi, OpenCV, Tesseract OCR, Python, SQLite'
date: 2026-05-05 12:00:00 +0000
description: A multi-camera warehouse scanning system that generates a Universal Product Passport for every package - even when barcodes fail - by fusing OCR and barcode signals across all six faces in under 3 seconds.
featured_image: '/images/Projects/Project4/OPS.gif'
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

When a barcode is obscured, damaged, or missing in a warehouse, the item loses its identity — triggering manual intervention and breaking traceability. We built an autonomous vision system that scans all six faces of any package and fuses OCR text and barcode data into a **Universal Product Passport**, so identification never depends on a single signal being readable.

**My role:** Technical Lead (Software) — system architecture, all five ROS2 nodes, OCR and image preprocessing pipeline, SmartMatcher scoring engine, SQLite database integration, and end-to-end testing across 43 scenarios.

| Resilience | Efficiency | Accuracy |
|:----------:|:----------:|:--------:|
| ✅ Works when barcode fails | ✅ ≤ 3s end-to-end | ✅ Up to 98.7% match |

🔗 [View Full Code on GitHub →](https://github.com/YongJiee/Omnidirectional-Perception-System)

<iframe src="https://www.youtube.com/embed/u8oNIFpHx1Q" width="640" height="360" frameborder="0" allowfullscreen></iframe>

---

## Project Highlights

- Scans all **6 faces** of any package using 3× IMX708 cameras across inbound and sorting modes
- **OCR + barcode fusion** — system identifies items even when barcode is fully missing or damaged
- **43/43 test scenarios PASS** — 10 inbound, 8 sorting, 25 resilience
- End-to-end scan time **~2.1–2.9s** across all scenarios
- Live **Universal Product Passport** written to SQLite, visible in DB Browser during demo

---

## Team

<div style="display: flex; flex-wrap: wrap; gap: 16px; margin-top: 12px;">

  <div style="flex: 1 1 200px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Liu Jiacheng</h4>
    <p>Team Lead / System Engineer</p>
  </div>

  <div style="flex: 1 1 200px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Goh Tong Ren Albert</h4>
    <p>Project Manager</p>
  </div>

  <div style="flex: 1 1 200px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Tan Yong Zhuo</h4>
    <p>Mechanical Lead</p>
  </div>

  <div style="flex: 1 1 200px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Tan Yong Jie</h4>
    <p><strong>Technical Lead (Software)</strong></p>
  </div>

</div>

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
    <img src="/images/Projects/Project4/Hardware_setup.jpg" style="width: 85%;" class="img-fluid rounded">
    <p>OPS scanning rig — robotic arm + conveyor system</p>
  </figure>
</div>

<div style="clear: both;"></div>

---

## System Architecture

The system runs across two machines — a Raspberry Pi 4 handling all camera capture, and a WSL Ubuntu host running the ROS2 processing pipeline, connected over direct ethernet.

<img src="/images/Projects/Project4/architecture.svg" style="width: 90%; margin-bottom: 10px; border-radius: 8px;">

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

</div>

---

## Camera Coverage — All 6 Faces

- **Cam0 + Cam2** at 45° → side faces
- **Cam1** top-down → inbound scans TOP; sorting: arm holds box underneath so Cam1 scans BOTTOM
- **Arm end-effector camera** → scans TOP before pick-up in sorting mode only

---

## Problems & How We Solved Them

### Problem 1 — Camera angle distorts text, making OCR unreliable

Side cameras sit at 45° to fit the compact rig. Raw images arrive skewed, causing Tesseract to misread characters or miss words entirely.

**Solution:** An OpenCV preprocessing pipeline runs on every frame before OCR — Canny edge detection finds the package face boundary, then perspective warp corrects the angle so Tesseract receives a flat, front-facing view.

```python
def preprocess_image(self, image):
    gray = cv2.cvtColor(image, cv2.COLOR_RGB2GRAY)

    # Find package face boundary
    edges = cv2.Canny(gray, 50, 150)
    contours, _ = cv2.findContours(edges, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

    # Correct camera angle via perspective warp
    if contours:
        largest = max(contours, key=cv2.contourArea)
        pts = self._get_corner_points(largest)
        return self._perspective_warp(gray, pts)

    return gray
```

---

### Problem 2 — Cardboard texture generates false OCR tokens

Tesseract picks up noise characters from cardboard grain and print artifacts — tokens like `"wt"`, `"fl"`, `"oz"` — that pollute the match and reduce accuracy.

**Solution:** A curated `OCR_NOISE_WORDS` filter strips known false-positive tokens before any matching occurs. Real-world testing confirmed 100% of noise tokens were correctly ignored across all resilience scenarios.

```python
OCR_NOISE_WORDS = {'the', 'and', 'of', 'net', 'wt', 'oz', 'ml', 'fl', 'made', 'in'}

def extract_text(self, image):
    raw = pytesseract.image_to_string(image, config='--psm 11')
    tokens = raw.lower().split()
    clean = [t for t in tokens if t not in OCR_NOISE_WORDS and len(t) > 2]
    return clean
```

---

### Problem 3 — Some products are ambiguous without a barcode

Products like *Cream Lip Gloss* exist under two different brands with near-identical label text. A naive fuzzy match would pick one at random — which is worse than admitting uncertainty.

**Solution:** SmartMatcher scores all candidates and calls `check_tie()` to detect when two products score within margin. Rather than guessing, it flags the result for manual resolution. The system only accepts a match when confidence clears 95%.

```python
def _calculate_accuracy(self, ocr_tokens, candidate):
    brand_score   = self._enhanced_fuzzy_match(ocr_tokens, candidate['brand'])
    product_score = self._enhanced_fuzzy_match(ocr_tokens, candidate['product_name'])
    keyword_bonus = self._keyword_bonus(ocr_tokens, candidate)

    if self._is_unique_product(candidate):
        return product_score * 0.9 + keyword_bonus      # brand not required
    elif brand_score == 0:
        return brand_score * 0.6                        # brand-only fallback
    else:
        return brand_score * 0.4 + product_score * 0.5 + keyword_bonus
```

---

### Problem 4 — Every scan needs a traceable, queryable record

Identification results need to be stored in a way that's auditable — including failed or flagged scans, not just successful ones. Assessors also need to verify results live without running queries.

**Solution:** Every scan writes a complete Universal Product Passport row to SQLite regardless of outcome. `scan_mode` is stored directly in the scans table (no JOIN needed) so DB Browser shows the full picture instantly. Unknown quantities are written as `quantity_source='flagged'` rather than silently omitted.

```python
def save_scan(self, scan_data):
    conn = sqlite3.connect(self.db_path)
    cursor = conn.cursor()
    cursor.execute('''
        INSERT INTO scans (
            scan_id, scan_mode, product_id, brand, product_name,
            quantity, quantity_source, match_score, match_method,
            overall_start, overall_end, cycle_time, timestamp
        ) VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)
    ''', (
        scan_data['scan_id'], scan_data['scan_mode'],
        scan_data['product_id'], scan_data['brand'],
        scan_data['product_name'], scan_data['quantity'],
        scan_data['quantity_source'], scan_data['match_score'],
        scan_data['match_method'], scan_data['overall_start'],
        scan_data['overall_end'], scan_data['cycle_time'],
        scan_data['timestamp']
    ))
    conn.commit()
    conn.close()
```

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

### System Specifications

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

---

## Demo Scenarios

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(320px, 1fr)); gap: 30px; margin: 40px 0;">
  <div style="text-align: center;">
    <video style="width: 100%; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);" controls>
      <source src="/images/Projects/Project4/Inbound Scenario 2.mp4" type="video/mp4">
    </video>
    <p style="margin-top: 15px; font-weight: bold;">Inbound mode</p>
    <p style="margin-top: 5px; color: #666; font-size: 0.9em;">Top + side faces · live database update</p>
  </div>
  <div style="text-align: center;">
    <video style="width: 100%; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);" controls>
      <source src="/images/Projects/Project4/Sorting Scenario 3.mp4" type="video/mp4">
    </video>
    <p style="margin-top: 15px; font-weight: bold;">Sorting mode</p>
    <p style="margin-top: 5px; color: #666; font-size: 0.9em;">Bottom + side + end-effector · pass/fail to arm</p>
  </div>
</div>

---

## What I Learned

The most valuable lesson wasn't a technology — it was learning to make decisions under real constraints rather than ideal ones.

**Fuzzy matching vs ML** came down to a build-vs-maintain framing. A classifier would have been more interesting to build, but fuzzy matching with a well-tuned noise filter was the right answer for an inventory that changes constantly. Choosing the less impressive solution because it fits the problem better is a real engineering call.

**Designing for failure** — flagged quantities, tie detection, noise filtering, brand-integrity gating — the most important software work wasn't in the happy path. It was in deciding what to do when things go wrong, and making those decisions explicit rather than silent.