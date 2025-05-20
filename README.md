Bien sûr ! Voici la version du README avec des emojis sympas pour rendre tout ça plus vivant et facile à lire :

---

# 🏭 Industrial Quality Control Project Using Machine Vision — Jam Jars 🍯

---

## 📋 Project Description

This project aims to design an automated industrial quality control system for jam jars on a production line. The system uses an intelligent camera (Keyence type) to check several essential criteria:

1. 🧢 Presence of the lid on the jar.
2. 🔒 Proper closure of the lid.
3. 🏷️ Presence and conformity of the label, including verification of all printed text.
4. 📏 Correct jam level inside the jar.

If any of the above criteria are not met, the system signals a defect by displaying a **red square ❌** on a dedicated screen visible to the line operator for quick intervention.

---

## 🎯 Objectives

* 🤖 Automate defect detection on the production line.
* ✅ Improve product quality and reduce rejects.
* 👁️ Provide a simple and clear visual interface for the operator.
* 🔗 Integrate the system with a Programmable Logic Controller (PLC) for efficient and scalable management.

---

## 🛠️ System Architecture

| Component                                            | Function                                                                        |
| ---------------------------------------------------- | ------------------------------------------------------------------------------- |
| 📷 **Keyence Intelligent Camera (CV-X / IV series)** | Captures and analyzes images, performs real-time visual inspections.            |
| 💡 **Industrial LED Lighting**                       | Provides uniform lighting for clear and usable images.                          |
| 🚦 **Optical Passage Sensor**                        | Detects each jar passing to trigger image capture.                              |
| 🤖 **Programmable Logic Controller (PLC)**           | Centralizes defect signals, controls operator interface and corrective actions. |
| 🖥️ **Industrial HMI Screen**                        | Displays inspection status in real-time, signals defects with a red square.     |
| 🗑️ **Reject System (optional)**                     | Automatically removes defective jars from the line.                             |

---

## 🔍 Inspected Features

1. 🧢 **Lid presence**
   Visual detection of lid presence using shape and contrast analysis.

2. 🔒 **Correct lid closure**
   Verification of alignment and proper closure through contour and light reflection analysis.

3. 🏷️ **Label presence and conformity**
   Detection of label position and verification of printed text (via OCR/OCV).

4. 📏 **Jam level correctness**
   Measurement of jam level from the image (contrast analysis) or a complementary sensor.

---

## 🔧 Image Processing Algorithms for Quality Control

### 1. Lid Presence 🧢

* **Image acquisition**: top or slight angled view.
* **Preprocessing**: grayscale conversion, median filtering to reduce noise.
* **Segmentation**: contour detection, adaptive thresholding to isolate the lid.
* **Shape analysis**: detect a circle (Hough transform) corresponding to the lid.
* **Criterion**: valid circle presence → lid present; else defect.

```
Acquire top view image
Convert image to grayscale
Apply median filter to reduce noise
Define ROI around jar
Apply adaptive threshold to segment lid
Find contours within ROI
For each detected contour:
    Compute inscribed circle using Hough transform
    If circle matches expected size:
        Lid present = TRUE
        Break loop
If Lid present == TRUE:
    Return OK
Else:
    Return "lid missing" defect

```
---

### 2. Proper Lid Closure 🔒

* **Edge analysis**: extract lid contour (e.g. Canny filter), check circular continuity.
* **Reflection analysis**: detect shadows or unusual reflections on the lid via intensity histogram.
* **3D option**: measure lid height to confirm proper closure.

```
Acquire image with lid
Convert to grayscale
Extract lid contour (e.g. Canny)
Analyze contour continuity
If contour is continuous without breaks:
    Closure OK = TRUE
Else:
    Closure OK = FALSE

Analyze intensity histogram within lid area
If abnormal peaks detected (shadows/reflections):
    Closure OK = FALSE

Return Closure OK or "lid not properly closed" defect

```


---

### 3. Label Presence and Conformity 🏷️

* **Localization**: template matching or detection within predefined ROI.
* **Presence check**: texture and contrast analysis to confirm label presence.
* **Text verification**: built-in OCR to extract and validate key information (date, batch, name).
* **Criterion**: text correct → OK; else defect.

```
Acquire side view image of jar
Define ROI for label area
Extract ROI
Analyze texture and contrast to confirm label presence
If label present:
    Apply OCR on ROI
    Extract key fields (date, batch, name)
    For each key field:
        Check presence and format correctness
    If all correct:
        Return OK
    Else:
        Return "label unreadable or incorrect" defect
Else:
    Return "label missing" defect

```

---

### 4. Jam Level 📏

* **Acquisition**: side or transparent view.
* **Segmentation**: adaptive threshold to isolate liquid/air interface.
* **Measurement**: measure liquid height in pixels, convert to real units.
* **Criterion**: level within tolerance → OK; else defect.

```
Acquire side/transparent image
Convert to grayscale
Define ROI for jam area
Apply adaptive threshold to segment jam vs air
Measure jam segment height in pixels
Convert pixels to mm (calibration)
If height within tolerance:
    Return OK
Else:
    Return "incorrect level" defect

```

---

### 5. Result Fusion and Alert 🚨

* Each check returns a binary result (OK / defect).
* If any defect, camera sends alert to PLC.
* PLC displays **red square ❌** and error message on HMI for operator.

```
Receive results:
    Lid presence OK/defect
    Lid closure OK/defect
    Label OK/defect
    Level OK/defect

If any defect detected:
    Send alert to PLC
    Display red square on HMI
    Show corresponding error message
Else:
    No alert

```
---

## 🔧 Configuration and Programming Details

### 1. Camera Configuration (Inspection Tools) 📷

#### Camera Pipeline Example

| Step                         | Tool/Action                          | Description                                 |
| ---------------------------- | ------------------------------------ | ------------------------------------------- |
| 🎥 **Image acquisition**     | Camera configuration                 | Set resolution, frame rate, trigger source. |
| 🧹 **Preprocessing**         | Median filter + grayscale conversion | Reduce noise and simplify image.            |
| 🎯 **Segmentation**          | Adaptive threshold tool              | Isolate target objects (lid, label, level). |
| 🔍 **Shape detection**       | Hough transform, contour extraction  | Identify lid and status.                    |
| 🎨 **Texture analysis**      | Texture, contrast                    | Check label presence and quality.           |
| ✍️ **OCR/OCV**               | Built-in OCR tool                    | Extract and validate text on label.         |
| 📏 **Dimension measurement** | Segment height measurement           | Check jam level.                            |
| ⚙️ **Decision logic**        | Script or logic blocks               | Combine results, generate defect signals.   |
| 🔗 **Communication**         | Ethernet/IP or Modbus TCP protocol   | Send results to PLC.                        |

---

### 2. PLC Programming (Alert Management) 🤖

* Receive defect signals from camera.
* Control HMI display and trigger actions (e.g. line stop, reject).

```
// Input variables
Cam_defect_lid_presence   // BOOL
Cam_defect_lid_closure    // BOOL
Cam_defect_label          // BOOL
Cam_defect_level          // BOOL

// HMI alert output
HMI_Alert := Cam_defect_lid_presence OR Cam_defect_lid_closure OR Cam_defect_label OR Cam_defect_level;

// Optional line stop output
Line_Stop := HMI_Alert;

```


---

### 3. HMI Configuration 🖥️

* Display red square ❌ if defect.
* Show specific error messages.
* Buttons for reset and acknowledge.

```
If Cam_defect_lid_presence == TRUE:
    Show "Lid missing"
Else If Cam_defect_lid_closure == TRUE:
    Show "Lid not closed properly"
Else If Cam_defect_label == TRUE:
    Show "Label missing or unreadable"
Else If Cam_defect_level == TRUE:
    Show "Jam level incorrect"
Else:
    Show "All OK"

```
---

### 🖥️ Interface HMI — "Quality Control Dashboard"

```

┌─────────────────────────────────────────────┐
│        🍯 Jam Jar Quality Control           │
├─────────────────────────────────────────────┤
│                                             │
│   🔴 [Red square]   ← Visible if HMI_Alert  │
│   ⚠️  [Alert Message Box]                   │
│                                             │
├─────────────────────────────────────────────┤
│   🧢 Lid Presence:         [OK] / [DEFECT]  │
│   🔒 Lid Closure:          [OK] / [DEFECT]  │
│   🏷️ Label:                [OK] / [DEFECT]  │
│   📏 Jam Level:            [OK] / [DEFECT]  │
├─────────────────────────────────────────────┤
│   ✅ [Reset Button]     🚫 [Stop Line Button]│
└─────────────────────────────────────────────┘
```


# 🍯 Jam Jar Quality Control Panel

| Component                | Status / Control                  |
|--------------------------|-----------------------------------|
| 🔴 Red Alert Box         | **Visible when** `HMI_Alert = 1` |
| ⚠️ Message               | Lid missing / Not closed / ...    |
| 🧢 Lid Presence          | ✅ OK / ❌ DEFECT                 |
| 🔒 Lid Closure           | ✅ OK / ❌ DEFECT                 |
| 🏷️ Label Detection       | ✅ OK / ❌ DEFECT                 |
| 📏 Jam Level             | ✅ OK / ❌ DEFECT                 |
| ✅ Reset Button          | Resets alerts                     |
| 🚫 Stop Line Button      | Stops the line                    |



---

## 🚦 Overall System Pipeline

```plaintext
[🚦 Jar passage sensor]
         ↓
[📷 Intelligent camera]
 (Trigger image capture)
         ↓
[🧹 Image processing]
 (Filtering, segmentation, detection)
         ↓
[🧠 Decision analysis]
 (Lid presence, closure, label, level)
         ↓
[⚠️ Defect signal (OK/KO)]
         ↓
[🤖 PLC]
 (Receive signal, decision logic)
         ↓
[🖥️ HMI]
 (Red square ❌ + message display)
         ↓
[🗑️ Line action]
 (Reject product / stop machine)
```

---

## 📦 Required Hardware

| Type                         | Example Hardware                              |
| ---------------------------- | --------------------------------------------- |
| 📷 Intelligent camera        | Keyence CV-X Series / IV Series               |
| 💡 LED Lighting              | Ring or bar LED suitable for the line         |
| 🚦 Optical sensor            | Keyence passage sensor                        |
| 🤖 PLC                       | Siemens S7-1200, Allen-Bradley MicroLogix     |
| 🖥️ HMI Screen               | Siemens KTP700 Basic, Industrial PC + display |
| 🗑️ (Optional) Reject System | Pneumatic or motorized ejectors               |

---

## 🚀 Installation & Commissioning

1. 🛠️ Install hardware (camera, lighting, sensor, PLC, screen).
2. ⚙️ Configure camera (inspection tools) as per described pipeline.
3. 🔧 Program PLC to receive and process defect signals.
4. 🖥️ Configure HMI interface for alert display.
5. ✅ Perform tests and adjustments.

---

## 🔧 Maintenance and Upgrades 🔄

* 🔧 Adjust camera parameters according to product changes.
* ➕ Add new inspection criteria (e.g. contamination detection).
* 📈 Implement defect logging.
* 📱 Advanced notifications.

---

## 📞 Contact & Support

**Project Team / Contact**
✉️ [contact.project@industry.com](mailto: mdomche@gmail.com)
📞 +33 6 12 34 56 78

---

# 🙏 Thank you for your attention!



