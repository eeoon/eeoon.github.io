---
layout: page
title: "Vanishing-Point Lane Detection"
description: "Vanishing point plus perspective transform classify four driving states without machine learning."
img: assets/img/projects/autonomous-driving-image-processing/img-3.png
importance: 6
category: personal
---

**Period** 2022.03 – 2022.06 · **Affiliation** Keimyung University (personal project) · **Role** Algorithm design and implementation overall

## Background and Goals

A driver-assistance system that determines the steering direction from the vanishing point where detected lane lines meet. The goal was to compensate for the error that arises when the driving direction is decided from the vanishing point alone by adding lane detection, introduce a new interpretation of driving states from the two complementary techniques, and implement a low-cost ADAS without machine learning.

## Approach

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/autonomous-driving-image-processing/img-3.png" title="Vanishing Point" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/autonomous-driving-image-processing/img-4.png" title="Perspective Transform" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Vanishing point detection on real footage (left, red dot) and before/after applying the perspective transform (right).</div>

- **Vanishing Point:** Based on the principle that parallel lines in 3D meet at a single point in the 2D image because of the FOV. Edge detection → Hough transform → line detection → intersection. Defined the vanishing point range with a bounding box (validated the box with a Gaussian distribution) and classified IN the box as straight driving and OUT as a turning section.
- **Perspective Transform:** Selected four coordinates in a straight section and applied a perspective transform to generate a bird's-eye view, then changed the ROI to the lanes. If the two lines are parallel, the state is Parallel (driving centered); if not, Bias (off-center). Started with a comparison test of 45° vs. 90° FOV.
- **Combined decision:** Using the four-way classification of VP (IN/OUT) × PT (Parallel/Bias), determined "straight centered / straight biased / turning centered / turning biased" and output it as a video overlay and to the console.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/autonomous-driving-image-processing/img-7.png" title="Parallel/Bias" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/autonomous-driving-image-processing/img-8.png" title="Combination matrix" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/autonomous-driving-image-processing/img-9.png" title="Output" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Lane transform and Parallel/Bias decision (left), the VP × PT combined decision matrix (center), and video and console output (right).</div>

## Results

Proposed a technique that determines straight/turning sections with a bounding box and vehicle bias with a perspective transform, improving driving stability through the complementary combination of the two. Validated on a public driving video dataset; combining it with existing ML-based autonomous driving is expected to improve stability. The implementation is documented in a [technical note](/blog/2021/vanishing-point-lane-detection/).

## Tech Stack

OpenCV · Python / C++ · Hough Transform · Perspective Transform (Homography) · Gaussian Distribution · Confusion matrix application
