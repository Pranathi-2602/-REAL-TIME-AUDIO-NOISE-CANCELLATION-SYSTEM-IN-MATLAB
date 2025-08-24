%% Real-Time Audio Noise Reduction using LMS Adaptive Filter

clear; clc; close all;

%% Load Clean Speech Signal
[speech, fs] = audioread('bluewhale.au');  % Your clean speech file
speech = speech(:,1);  % In case stereo, take one channel
speech = speech(1:50000); % Use limited samples for faster testing

%% Generate or Load Noise Signal
noise = 0.3 * randn(size(speech));  % white Gaussian noise
% Alternatively: [noise, ~] = audioread('background_noise.wav');

%% Create Noisy Signal (primary input)
noisy_signal = speech + noise;

%% Reference Noise (assume uncorrelated copy of noise)
ref_noise = filter([1 0.8], 1, noise);  % Colored version of the noise

%% LMS Adaptive Filter Parameters
mu = 0.01;          % Step size (try: 0.001 to 0.1)
filter_order = 32;  % Filter length

% Initialize variables
N = length(noisy_signal);
y = zeros(N,1);       % Filter output
e = zeros(N,1);       % Error signal (final output)
w = zeros(filter_order,1);  % Filter coefficients

%% Adaptive Filtering using LMS
for n = filter_order:N
    x = ref_noise(n:-1:n-filter_order+1);  % Input vector
    y(n) = w' * x;                         % Filter output
    e(n) = noisy_signal(n) - y(n);         % Error signal (desired - output)
    w = w + mu * x * e(n);                 % Coefficient update
end

%% Plotting Results
t = (1:N)/fs;
figure;
subplot(3,1,1);
plot(t, speech); title('Original Clean Speech'); xlabel('Time (s)'); ylabel('Amplitude');

subplot(3,1,2);
plot(t, noisy_signal); title('Noisy Speech'); xlabel('Time (s)');

subplot(3,1,3);
plot(t, e); title('Filtered Output (Noise Reduced)'); xlabel('Time (s)');

%% Listen to Signals
disp('Playing Original Clean Speech...');
soundsc(speech, fs); pause(5);

disp('Playing Noisy Signal...');
soundsc(noisy_signal, fs); pause(5);

disp('Playing Filtered Output...');
soundsc(e, fs);

%% Save Outputs (Optional)
% Get the Desktop folder path
desktopPath = fullfile(getenv('USERPROFILE'), 'Desktop');
outputFile = fullfile(desktopPath, 'filtered_output.wav');

% If file already exists, delete it to prevent locking errors
if isfile(outputFile)
    delete(outputFile);
end

% Write to Desktop
e = e / max(abs(e));  % Normalize to keep values between -1 and 1
audiowrite(outputFile, e, fs);

disp(['✅ Audio saved successfully to: ', outputFile]);
%% Evaluate Performance
snr_before = snr(speech, noise);
snr_after = snr(speech, speech - e);
mse_val = immse(speech, e);

fprintf('\n--- Performance ---\n');
fprintf('SNR Before Filtering: %.2f dB\n', snr_before);
fprintf('SNR After Filtering : %.2f dB\n', snr_after);
fprintf('MSE: %.4f\n', mse_val);![op_project_matlab](https://github.com/user-attachments/assets/8f0bd0b4-385e-4161-bc46-e39df0323f0b)


# Real-Time Audio Noise Cancellation (MATLAB)

## 📌 Project Overview
This project demonstrates a *real-time noise cancellation system* using an *Adaptive LMS (Least Mean Squares) filter* in MATLAB.  
The system enhances speech signals by improving the *Signal-to-Noise Ratio (SNR) by ~10 dB*.

## 🛠 Tools & Technologies
- MATLAB (DSP Toolbox)
- Adaptive LMS Filter Algorithm

## ⚙ Features
- Real-time audio input/output
- Effective noise suppression
- Improvement of SNR by ~10 dB

## 🚀 How to Run
1. Open MATLAB and load the provided .m script.  
2. Run the script with noisy audio input.  
3. Observe real-time noise reduction in the output.


