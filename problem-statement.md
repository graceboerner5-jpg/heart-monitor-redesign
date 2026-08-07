---
title: Problem Statement
layout: home
nav_order: 2
---

# **Problem Statement**

Current wearable ECG devices require the user to remain still during monitoring which limits their use during physical activity.

## Background

Although heart monitoring devices have existed through many iterations, they continue to struggle with motion and muscle artifacts (MA). MA is the unwanted electrical signals from muscular signals or the device itself that confound the desired signal. While motion artifacts are simpler to eliminate, muscle artifacts can be more difficult because of their closeness in frequency to the heart signal. There have been many approaches to reduce muscle artifacts. However, current wearable ECG devices still require the user to remain still while in use, demonstrating that no widely adopted solution has been integrated into consumer wearable ECG devices. 

## Current Limitations

Muscle artifact commonly affects both wearable and implantable cardiac monitoring devices. Because muscle artifacts are typically in the same frequency range as cardiac signals, attenuating muscle noise can lead to unintended distortion or smoothing of the heartbeat signal. Oversensing from myopotentials is a prevalent issue in heart monitors and ICDs that can lead to incorrect treatment plans. Current filtering techniques such as low-frequency attenuation can reduce artifacts such as oversensing of the T-wave but may also attenuate the target signal. Significantly, distortion of the ST segment can lead to misdiagnosed myocardial ischemia in patients with coronary artery disease. 

## Design Opportunity

Adaptive noise cancellation (ANC) is a method that could actually decrease muscle artifact by identifying and removing muscle noise using sEMG. ANC requires two channels, a traditional ECG channel and an EMG channel for the muscle signal. Both signals are recorded in real time and appropriately scaled. Ideally, subtracting the muscle signal from the traditional ECG signal would result in an isolation of the heartbeat signal.

This design idea is relevant to cardiac rhythm management since decisions are made based off the filtered heartbeat signal. In implantable devices, sensing algorithms rely on clean electrograms to accurately detect arrhythmias and deliver appropriate treatment. There are serious consequences for misleading signals since myopotential contamination can lead to pacing inhibition or inappropriate ICD shocks. In wearable monitors, the same muscle artifact reduces the reliability of remote data sent to clinicians. An ANC-based filtering system that preserves QRS morphology while reducing muscle noise could improve signal quality and therefore patient treatment for both implantable and wearable monitors.
