# 🅿️ ParkVision AI

### Intelligent Urban Parking Analytics & Space Optimisation Platform

> **ParkVision AI** is a computer-vision system that analyses parking-lot images and live camera feeds, detects individual parking spaces, classifies them as **empty** or **occupied**, and converts those detections into parking availability, occupancy, congestion, and recommendation insights.

**Built using Python, Ultralytics YOLO, and Streamlit.**

---

## 📌 Project Overview

Finding a parking space can be inefficient, especially in busy urban environments. Drivers may spend unnecessary time searching for available spaces, contributing to congestion, fuel consumption, and frustration.

ParkVision AI approaches this as a **slot-level object detection problem**. Instead of classifying an entire image as simply busy or free, the system detects individual parking spaces and determines their occupancy state.

### Core pipeline

```text
Parking Image / Camera
        ↓
YOLO Object Detection
        ↓
Parking-Space Detection
        ↓
Empty / Occupied Classification
        ↓
Parking Logic
        ↓
Occupancy & Availability Metrics
        ↓
Congestion Analysis
        ↓
Actionable Recommendation
```

---

# 🎯 Problem Statement

Urban parking environments present several computer-vision challenges:

- A single image can contain many parking spaces.
- Spaces can have different orientations and sizes.
- Vehicles can partially occlude parking spaces.
- Lighting and shadows can change significantly.
- Camera viewpoints can vary between parking areas.
- Weather and environmental conditions can affect image appearance.
- The system must convert individual detections into useful parking information rather than only producing bounding boxes.

Therefore, ParkVision AI was designed to answer:

> **Can computer vision automatically detect parking spaces, determine their occupancy status, and convert those detections into useful parking decisions?**

---

# 🎯 Project Objectives

1. Detect individual parking spaces in an image.
2. Classify detected spaces according to their occupancy state.
3. Calculate total, occupied, and available spaces.
4. Calculate overall parking occupancy.
5. Categorise parking congestion.
6. Generate a simple recommendation for the user.
7. Provide image-based parking analysis through Streamlit.
8. Provide live-camera monitoring.
9. Visualise parking analytics over time.
10. Maintain detection history for previous analyses.
11. Provide optional sound alerts.
12. Present computer-vision results through an accessible user interface.

---

# 🧠 Why Object Detection?

Parking occupancy is naturally a **multi-object problem**.

A whole-image classifier could answer:

> “Is this parking lot busy?”

ParkVision instead needs to answer:

> “Which individual spaces are occupied, and which are empty?”

Object detection is therefore appropriate because it can:

- Locate individual parking spaces.
- Assign a class to each detection.
- Produce bounding boxes.
- Process multiple spaces in a single image.
- Support parking lots with different layouts.

ParkVision uses the **YOLO object-detection framework** for this purpose.

---

# 📊 Dataset

The project uses an annotated parking-space image dataset.

The final prepared dataset contains **12,416 images**:

| Split | Images | Labels |
|---|---:|---:|
| Train | 8,691 | 8,691 |
| Validation | 2,483 | 2,483 |
| Test | 1,242 | 1,242 |
| **Total** | **12,416** | **12,416** |

### Classes

The prepared annotations contain three classes:

```text
0 → spaces
1 → space-empty
2 → space-occupied
```

---

# 🔄 Data Preparation

The original annotations were provided in **COCO JSON format** and were converted into the YOLO annotation format required by the Ultralytics training pipeline.

### Conversion workflow

```text
COCO Dataset
     ↓
COCO JSON Annotations
     ↓
Annotation Conversion
     ↓
YOLO TXT Labels
     ↓
YOLO Dataset Structure
     ↓
Model Training
```

The final dataset was verified so that every image had a corresponding label file.

### Final dataset verification

```text
Train:       8,691 images / 8,691 labels
Validation:  2,483 images / 2,483 labels
Test:        1,242 images / 1,242 labels
Total:      12,416 images / 12,416 labels
```

---

# 🤖 Model Development

ParkVision AI uses an **Ultralytics YOLO26n** object-detection model.

### Final training configuration

| Parameter | Configuration |
|---|---|
| Model | YOLO26n |
| Task | Object Detection |
| Image size | 512 × 512 |
| Epochs | 15 |
| Batch size | 4 |
| Device | CPU |
| Framework | Ultralytics YOLO |
| Final weights | `best.pt` |

The model was trained on the prepared parking-space dataset. The final trained model used by the application is stored as `best.pt`.

---

# 📈 Model Performance

Final validation results:

| Metric | Result |
|---|---:|
| Precision | **0.984** |
| Recall | **0.981** |
| mAP@50 | **0.993** |
| mAP@50–95 | **0.904** |
| Space-empty mAP@50–95 | **0.914** |
| Space-occupied mAP@50–95 | **0.894** |

### Interpretation

- **Precision 0.984:** most predicted detections were correct.
- **Recall 0.981:** the model detected a very high proportion of annotated objects.
- **mAP@50 0.993:** very strong detection performance at IoU 0.50.
- **mAP@50–95 0.904:** strong performance across stricter IoU thresholds.
- **Space-empty mAP@50–95 0.914** and **space-occupied mAP@50–95 0.894** show strong performance for the two parking-status categories.

These results describe performance on the validation data; real-world performance can vary with unseen lighting, viewpoints, occlusion, and environmental conditions.

---

# 🅿️ Parking Insight Logic

The YOLO model provides detections. ParkVision’s logic layer converts those detections into user-facing information.

### Calculations

```text
Total = Occupied + Empty
Available = Empty
Occupancy (%) = (Occupied / Total) × 100
```

### 🚦 Congestion classification

| Occupancy | Status | Recommendation |
|---:|---|---|
| `< 40%` | 🟢 Low | Parking availability is good — proceed to park. |
| `40–75%` | 🟡 Moderate | Parking is moderately occupied — available slots remain. |
| `> 75%` | 🔴 High | Parking is highly occupied — consider another area. |

If no valid detections are available, the application reports that a valid parking image should be uploaded.

---

# 🖥️ Streamlit Application

ParkVision AI is implemented as a multi-page Streamlit dashboard.

### Navigation

```text
🏠 Home
🖼️ Upload Image
📹 Live Camera
📊 Analytics
🕒 Detection History
⚙️ Settings
ℹ️ About
```

### Live application

**https://parkvision1234.streamlit.app**

---

# 🖼️ Upload Image

The Upload Image page allows a user to submit a parking-lot image.

The application:

1. Loads the image.
2. Runs YOLO inference.
3. Detects parking spaces.
4. Classifies detections.
5. Counts occupied and empty spaces.
6. Calculates occupancy.
7. Determines congestion.
8. Generates a recommendation.
9. Displays the annotated image.
10. Records analysis information for the application’s analytics/history features.

Detected spaces are visualised with colour-coded boxes:

- 🟢 **Green** → Empty
- 🔴 **Red** → Occupied

The image-analysis interface also provides parking statistics, parking status, sound controls, a parking map/visualisation area, parking intelligence, image analytics, and detection details.

---

# 📹 Live Camera

ParkVision also supports live parking monitoring through a camera interface using Streamlit WebRTC.

Live monitoring includes:

- Real-time YOLO detection.
- Occupied-space count.
- Empty-space count.
- Total detected spaces.
- Occupancy percentage.
- FPS information.
- Peak occupancy tracking.
- Recent occupancy history.
- Live parking-status information.

---

# 📊 Analytics

The Analytics page visualises parking behaviour over time.

Available metrics include:

- Occupancy
- Available spaces
- Occupied spaces
- Total spaces

Supported time windows:

```text
15 seconds
30 seconds
60 seconds
120 seconds
```

The interface supports:

- Line charts
- Bar charts
- Occupancy trends
- Session statistics
- CSV export

---

# 🕒 Detection History

Completed analyses can contribute to the detection-history view so that previous parking analyses can be reviewed rather than treating every prediction as an isolated event.

Relevant information includes:

- Detection time
- Occupancy
- Occupied spaces
- Available spaces
- Total spaces

---

# 🔊 Sound Alerts

ParkVision includes optional sound feedback.

Features include:

- Global sound enable/disable control.
- Test Sound option.
- Automatic sound feedback after image analysis when enabled.
- Sound support within the live monitoring experience.

---

# ⚙️ Settings

The Settings page provides application-level controls, including sound preferences and other user-facing configuration options.

---

# 🧩 System Architecture

```text
                    ┌─────────────────────┐
                    │       USER          │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   STREAMLIT APP     │
                    └──────────┬──────────┘
                               │
                 ┌─────────────┴─────────────┐
                 ▼                           ▼
        ┌────────────────┐          ┌────────────────┐
        │ Upload Image   │          │  Live Camera   │
        └───────┬────────┘          └───────┬────────┘
                │                           │
                └─────────────┬─────────────┘
                              ▼
                    ┌─────────────────────┐
                    │    YOLO26n Model    │
                    │   Object Detection  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Parking Detections │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Parking Logic     │
                    └──────────┬──────────┘
                               │
                ┌──────────────┼──────────────┐
                ▼              ▼              ▼
           Availability    Occupancy      Congestion
                │              │              │
                └──────────────┼──────────────┘
                               ▼
                    ┌─────────────────────┐
                    │ Actionable Insight  │
                    │  & Recommendation   │
                    └─────────────────────┘
```

---

# 🧪 Testing

Testing was carried out at multiple levels.

### 1. Dataset verification

The prepared dataset was checked to confirm that images and corresponding YOLO label files were present across the train, validation, and test splits.

### 2. Model evaluation

The final model was evaluated on validation data and produced the performance metrics reported above.

### 3. Parking logic testing

The parking insight logic was tested for different occupancy conditions, including no detections, low occupancy, moderate occupancy, and high occupancy.

### 4. Application testing

The Streamlit application was tested through:

- Image upload.
- YOLO inference.
- Detection visualisation.
- Parking statistics.
- Congestion classification.
- Recommendation generation.
- Live camera workflow.
- Analytics.
- Detection history.
- Settings and sound controls.

---

# 🌍 Real-World Considerations & Limitations

Parking environments are visually complex.

### Lighting

Sunlight, shadows, nighttime conditions, and changing illumination can affect detection.

### Occlusion

Vehicles, trees, poles, and other objects can partially hide parking spaces.

### Camera angle

Different parking areas may use different camera heights, orientations, and perspectives.

### Environmental conditions

Rain, reflections, and changes in the surroundings can affect visual appearance.

### Dataset diversity

A model trained on the available dataset may not perform identically in every real-world location.

---

# 🚀 Future Improvements

Potential future development includes:

1. **Larger and more diverse datasets** — include more locations, camera viewpoints, weather conditions, and lighting conditions.
2. **Real-time parking maps** — display available spaces directly on a live parking map.
3. **Multi-camera support** — combine multiple camera feeds for larger parking facilities.
4. **Historical demand prediction** — use occupancy history to predict busy periods.
5. **Smart-city integration** — connect parking analytics with broader urban systems.
6. **Improved deployment hardware** — use suitable GPU or edge-computing hardware for higher real-time performance.
7. **More advanced recommendations** — combine real-time and historical information for context-aware parking decisions.

---

# 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| **Python** | Core programming language |
| **Ultralytics YOLO** | Object detection |
| **YOLO26n** | Parking-space detection model |
| **Streamlit** | Web application |
| **Streamlit WebRTC** | Live camera processing |
| **Pandas** | Data handling and analytics |
| **NumPy** | Numerical operations |
| **Pillow** | Image processing |
| **Plotly** | Analytics visualisation |
| **OpenCV headless stack** | Computer-vision support |
| **GitHub** | Version control and deployment |
| **Streamlit Community Cloud** | Public application deployment |

---

# 📁 Deployment Repository Structure

The final deployment repository is kept focused on the files required to run the application:

```text
PARK_VISION_SA/
├── app.py
├── best.pt
├── parking_logic.py
├── requirements.txt
└── README.md
```

| File | Purpose |
|---|---|
| `app.py` | Main Streamlit application |
| `best.pt` | Final trained YOLO26n model |
| `parking_logic.py` | Occupancy, congestion, and recommendation logic |
| `requirements.txt` | Python dependencies |
| `README.md` | Project documentation |

If the assessment repository requires the dataset itself to be included, a `dataset/` directory can also be added separately according to the submission requirements.

---

# 📦 Installation & Local Run

## 1. Clone the repository

```bash
git clone <YOUR-GITHUB-REPOSITORY-URL>
cd PARK_VISION_SA
```

## 2. Create a virtual environment

### Windows

```bash
python -m venv venv
venv\Scripts\activate
```

### macOS / Linux

```bash
python3 -m venv venv
source venv/bin/activate
```

## 3. Install dependencies

```bash
pip install -r requirements.txt
```

Recommended deployment requirements:

```text
ultralytics-opencv-headless==8.4.143
streamlit
pandas
numpy
Pillow
plotly
streamlit-webrtc
av
streamlit-autorefresh
```

## 4. Run the application

```bash
streamlit run app.py
```

---

# ☁️ Deployment

ParkVision AI is deployed using **Streamlit Community Cloud**.

### Live application

**https://parkvision1234.streamlit.app**

The deployment uses the trained `best.pt` model stored with the application.

---

# 📋 Final Project Results

ParkVision AI combines:

```text
Computer Vision
       +
YOLO Object Detection
       +
Parking Logic
       +
Analytics
       +
Streamlit
       =
Intelligent Parking Application
```

The final system can:

- Detect parking spaces.
- Identify empty and occupied spaces.
- Calculate parking availability.
- Calculate occupancy percentage.
- Determine congestion level.
- Generate parking recommendations.
- Analyse uploaded parking images.
- Monitor parking through a live camera.
- Visualise parking analytics.
- Maintain detection history.
- Provide optional sound feedback.
- Run as a publicly accessible Streamlit application.

---

# 📚 References

The project was informed by research and technical documentation related to parking occupancy detection, computer vision, object detection, and Streamlit development.

- **PKLot: A Robust Dataset for Parking Lot Classification**
- **Deep Learning Based Smart Parking Occupancy Detection using Computer Vision**
- **Vision-Based Parking Slot Detection using Deep Learning**
- **Real-Time Parking Occupancy Detection using CNN and Computer Vision**
- **LearnOpenCV — Object Detection using YOLO**
- **Ultralytics YOLO Documentation**
- **Streamlit Documentation**
- **OpenCV Documentation**

---

# 👩‍💻 ParkVision AI

**Intelligent Urban Parking Analytics & Space Optimisation Platform**

### Live Demo

👉 **https://parkvision1234.streamlit.app**

### GitHub

👉 **Add your GitHub repository link here**

---

## 💡 Project Vision

> **Smarter parking. Better decisions. More efficient cities.**

ParkVision AI demonstrates how computer vision can transform raw visual data into practical, human-readable information — moving from **detection → intelligence → action**.
