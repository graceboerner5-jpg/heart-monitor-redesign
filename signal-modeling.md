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

```matlab
rng(42); % Set random seed for reproducible results
white_noise = randn(N, 1);

% Apply a bandpass filter to shape the white noise into realistic muscle artifact
[b, a] = butter(4, [30 150]/(Fs/2), 'bandpass');
v_raw = filter(b, a, white_noise);
```

### (iii) Signal Conditioning & Standardization of Vectors

```matlab
s = (s_raw - mean(s_raw)) / std(s_raw);
v = (v_raw - mean(v_raw)) / std(v_raw);
```

### (iv) Synthesize the Dirty Primary Channel Input

```matlab
alpha = 0.6; % Contamination factor (60% muscle noise intensity)
d = s + alpha * v;
```
### (v) Results
<img src="./noisy_heartbeat_signal_v1.png" alt="Signal Modeling Sandbox" width="70%">



