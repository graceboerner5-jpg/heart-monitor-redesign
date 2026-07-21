---
title: Signal Modeling
layout: home
nav_order: 3
---

# Signal Modeling

## Objectives
1. Develop a signal representing a "noisy" heartbeat signal similar to what a sEMG setup would read.  
2. Develop an Adaptive Noise Canceling (ANC) filter and test it on a "noisy" heartbeat signal.  
3. Analyze and improve the ANC filter by optimizing the signal-to-noise ratio.

---

## 1. Develop a "noisy" heartbeat signal.

### Creating the Heartbeat Signal
The heartbeat signal is coded with constituent Gaussian pulses where each part (R-wave, S-wave and T-wave, and P-wave) are graphed with the Gaussian equation using different values for the variables (A, mu, sigma). The phase variable is used to track the position in the signal to know which version of the Gaussian equation to graph within a single cardiac cycle.

```matlab
% Generate rhythmic QRS spikes
for i = 1:N
    % Normalize phase to track position within a single cardiac cycle (0.0 to 1.0)
    phase = mod(t(i), 1/freq) * freq;
    if phase < 0.05
        % R-wave (sharp spike)
        heartbeat_signal(i) = 1.5 * exp(-((phase-0.02)/0.005)^2);
    elseif phase >= 0.05 && phase < 0.15
        % S-wave and T-wave
        heartbeat_signal(i) = -0.3 * exp(-((phase-0.05)/0.01)^2) + 0.35 * exp(-((phase-0.12)/0.03)^2);
    elseif phase > 0.85
        % P-wave (placed at end to help with adaptive filter later)
        heartbeat_signal(i) = 0.2 * exp(-((phase-0.92)/0.03)^2);
    end
end
s_raw = heartbeat_signal;
```
### (ii) Developing the Muscle Artifact Signal
The muscle artifact signal is developed by using a 4th order Butterworth bandpass filter with a passband of 30 to 150 Hz, scaled relative to the Nyquist frequency. The lower and bound was determined because muscle fiber frequency can be as low as 30 Hz. The upper bound was chosen because the dominant power spectral density of sEMG ranges between 50 and 150 Hz. The model of a muscle artifact signal is completed when Gaussian white noise is passed through the developed filter. 

```matlab
rng(42); % Set random seed for reproducible results
white_noise = randn(N, 1);

% Apply a bandpass filter to shape the white noise into realistic muscle artifact
[b, a] = butter(4, [30 150]/(Fs/2), 'bandpass');
v_raw = filter(b, a, white_noise);
```

### (iii) Signal Conditioning & Standardization of Vectors
To standardize the signal vectors, both the cardiac signal vector and muscle artifact signal vector undergo a z-score normalization. It is crucial to put both the ECG and EMG signals on the same scale to ensure that the filter is able to correctly filter out unwanted noise and not deform the original signal.

```matlab
s = (s_raw - mean(s_raw)) / std(s_raw);
v = (v_raw - mean(v_raw)) / std(v_raw);
```

### (iv) Synthesize the Dirty Primary Channel Input
This final step pulls both signals together to become a noisy heartbeat signal similar to what a real ECG electrode experiences. The final signal 'd' now contains both the heart signal and the muscle noise signal. The contamination factor allows for adjustment of the added muscle noise intensity. 

```matlab
alpha = 0.6; % Contamination factor (60% muscle noise intensity)
d = s + alpha * v;
```
### (v) Results
**Figure 1** shows a ten second interval of the clean heartbeat signal, isolated muscle sEMG noise vector, and the signals combined to create the noisy heartbeat signal.
<img src="./noisy_heartbeat_signal_v1.png" alt="Signal Modeling Sandbox" width="105%">
**Figure 1.** Multi-Channel Signals and Combined Signal



