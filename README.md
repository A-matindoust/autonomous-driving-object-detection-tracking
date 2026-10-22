# Autonomous Driving Object Detection and Tracking

A modular perception pipeline for autonomous driving that detects and tracks road users and other objects from vehicle-mounted camera footage. The project also explores combining object detection and tracking with lane perception from the separate [Autonomous Driving Lane Perception](https://github.com/search?q=Autonomous+Driving+Lane+Perception&type=repositories) project.

## Overview

Autonomous vehicles need to interpret objects in their surroundings and use that information to support downstream driving decisions. This project detects objects such as pedestrians, cars, motorcycles, vans, traffic lights, bridges, and road signs, then tracks them across video frames.

Object categories can matter beyond basic detection. For example, passing a passenger car may call for different planning than passing a motorcycle or a van. The dataset labels therefore distinguish vehicle types and sizes where the annotations support those distinctions, rather than grouping every vehicle into one generic class. Detection results are perception inputs; driving actions such as overtaking or braking belong to a separate planning and control system.

## Project challenges

1. **Convert the dataset to YOLO format.** The source dataset is in CSV form and is not directly compatible with Ultralytics YOLO. A conversion script prepares the images, labels, and dataset configuration for training.
2. **Support tracker choices beyond the detector's built-in options.** The project is structured to allow tracker integrations such as BoT-SORT or ByteTrack, trackers from other libraries (for example, BoxMOT), or a custom tracker.
3. **Combine object and lane perception.** The combined pipeline runs object detection and the lane segmentation model from [Autonomous Driving Lane Perception](https://github.com/search?q=Autonomous+Driving+Lane+Perception&type=repositories), then displays the results together.
4. **Keep bounding boxes aligned during augmentation.** Geometric transforms such as shifts or rotations must be applied consistently to images and their boxes. Bounding-box coordinates are represented relative to image dimensions and updated to match the transformed image.

## Pipeline

### Dataset conversion

The source CSV annotations contain bounding-box coordinates in the order `xmin`, `ymin`, `ymax`, and `xmax`, along with class names. The conversion script:

- Converts corner coordinates to YOLO's normalized `class_id x_center y_center width height` format.
- Handles multiple objects annotated in one image by writing one label row per bounding box.
- Maps class names to numeric class IDs.
- Produces the image and label structure and configuration required for YOLO training.

### Object detection

The detector uses **YOLO11n** (also known as YOLO11 nano), selected for its light compute requirements on the available hardware. Training currently uses an image size of **320 pixels** and a modest number of epochs to balance training time and accuracy.

Larger models, a larger image size such as 640 pixels, and longer training may improve detection quality, while requiring more compute and time. Ultralytics training augmentations can also be configured as part of the training workflow.

### Object tracking

The tracking workflow uses detections from the YOLO model and associates objects across video frames. The design allows evaluation of the supported tracker integrations, including trackers provided by Ultralytics, external libraries, or a custom implementation.

### Combined lane and object perception

The combined model loads the best object-detection weights and the best lane-segmentation weights from the companion project. It applies the segmentation model's required preprocessing, predicts lane masks, detects objects, and tracks both outputs for joint visualization.

The companion segmentation model is DeepLabV3+ with a ResNet-50 backbone. Its preprocessing must remain consistent with the preprocessing used during its training.

## Repository structure

The repository contains four main scripts:

- **YOLO dataset conversion:** converts the source CSV annotations into YOLO-compatible labels.
- **YOLO object detection:** configures and trains the YOLO11n detector.
- **Object tracking:** runs object detection with a selected tracker.
- **Combined model:** runs the lane segmentation and object detection pipelines together and visualizes their tracked results.

Update this section if the script filenames differ in the repository.

## Data augmentation notes

Photometric augmentations change image values (for example, brightness or contrast) and do not change box coordinates. Geometric augmentations change pixel locations and must also transform the bounding boxes. After transformation, boxes should be clipped to image bounds and invalid or empty boxes discarded.

## Datasets

Dataset sources and download links are documented in the repository's `Dataset` file. Refer to the original dataset terms and attribution requirements when using or redistributing the data.

## Related project

- [Autonomous Driving Lane Perception](https://github.com/search?q=Autonomous+Driving+Lane+Perception&type=repositories) — lane segmentation and tracking, combined with this repository's object detection pipeline.

Replace the search links with the exact repository URL when it is available.

## Intended use

This repository is for computer-vision experimentation and autonomous-driving perception research. It provides detection, segmentation, and tracking outputs for downstream systems; it is not a complete or safety-validated autonomous-driving controller.

## Suggested GitHub topics

`autonomous-driving` · `object-detection` · `object-tracking` · `yolo11` · `ultralytics` · `lane-segmentation` · `computer-vision` · `deep-learning` · `botsort` · `dataset-conversion`
