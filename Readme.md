# Multi-Attribute Vehicle Classification System

## Overview
Automated vehicle detection, tracking, and classification system that identifies vehicle type, size, color, and license plates from video footage.

## Features
- **Vehicle Detection & Tracking**: YOLOv11-based detection with persistent tracking
- **Multi-attribute Classification**: 
  - Type: sedan, SUV, compact_car, pickup_truck, truck, van
  - Size: small, medium, large (based on bounding box area)
  - Color: trained YOLOv11 classification model
- **License Plate Recognition**: Detection + OCR text extraction
- **Line Crossing Detection**: Counts vehicles crossing a defined boundary
- **Real-time Statistics**: Live display of vehicle counts by type/size

## Repository Structure
```
├── Detection_Tracking_Counting.ipynb    # Vehicle detection and tracking
├── Licence_Plate_Detection.ipynb        # License plate model training
├── Vehicle_Color_Recognition.ipynb      # Color classification training
├── 1.ipynb                             # Basic detection experiments
├── 2.ipynb                             # Full integrated system
├── Data/                              # Location of all the datasets
├── models/                             # Trained YOLO models
├── videos/                            # Input video files
└── results/                            # Results, cropped images and output videos
```

## Quick Start
1. Install requiremtns.txt
2. Place test video file in `videos/` directory.
3. Run notebook cells in sequence first the training notebooks.
4. Check `output/` for processed video and vehicle crops.

## Datasets
1. Detection_Tracking_Counting.ipynb --> No dataset needed here
2. Licence_Plate_Detection.ipynb --> Dataset can be downloaded in notebook
3. Vehicle_Color_Recognition.ipynb --> Can be found in: [Kaggle](https://www.kaggle.com/datasets/landrykezebou/vcor-vehicle-color-recognition-dataset)
4. Test Video: [Here](https://www.pexels.com/video/traffic-flow-in-the-highway-2103099/)
5. Sample Ouput Video: [Youtube](https://youtu.be/qKxwxCQBhdk)
---

# Technical Documentation

## Core Components
1. **Vehicle Detection Pipeline**
   - YOLOv11 object detection for vehicles
   - ByteTrack for multi-object tracking
   - Bounding box analysis for size classification

2. **Attribute Classification**
   - Color: YOLOv11 classification model
   - Size: Area-based thresholds (small <2%, medium 2-5%, large >5%)
   - Type: Class + size + aspect ratio logic

3. **License Plate Recognition**
   - YOLOv11 detection on vehicle crops
   - EasyOCR for text extraction
   - Preprocessing: resize, grayscale conversion

## Data Structure
```python
vehicle_data = {
    track_id: {
        'class': 'car',           # YOLO class name
        'type': 'sedan',          # Refined type
        'size': 'medium',         # Size classification
        'color': 'blue',          # Color model prediction
        'has_lp': True,           # License plate detected
        'crossed': True,          # Crossed counting line
        'lp_text': 'ABC123'       # OCR result
    }
}
```

## Processing Workflow

### Frame-by-Frame Processing
1. **Detection**: Run YOLOv11 on frame
2. **Tracking**: Associate detections with existing tracks
3. **Line Crossing**: Check if vehicle center crosses threshold
4. **Analysis** (on crossing):
   - Crop vehicle region with padding
   - Run color classification
   - Detect license plates
   - Extract OCR text
5. **Visualization**: Draw bounding boxes, labels, statistics

### Performance Optimizations
- Process only crossing vehicles for expensive operations
- Limit frame processing (`frame_limit = 100`)
- Use confidence thresholds (LP detection: 0.25)
- Batch processing where possible

## Output Format

### Video Output
- Annotated video with bounding boxes
- Vehicle labels: `ID:X size type color [LP_text]`
- Real-time statistics overlay
- Line crossing visualization

### Saved Files
- Processed video: `output/videos/tracked_video.mp4`
- Vehicle crops: `output/crops/vehicle_ID_size_type_color.jpg`