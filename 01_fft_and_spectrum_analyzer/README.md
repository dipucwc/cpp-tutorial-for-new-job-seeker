# FFT and Spectrum Analyzer

## Project Status

> **Ongoing project — currently under active development**

This project is being developed as a compact DSP algorithm design, implementation, verification, and benchmarking project.

MATLAB and Python will be used as numerical reference models. C++ will be used for the main algorithm implementation.

Source code, deterministic tests, benchmark results, figures, and technical documentation will be added progressively.

The project is not yet presented as a completed or fully validated software release.

## Project Objective

The objective is to design, implement, and verify a radix-2 Fast Fourier Transform and a windowed spectrum analyzer.

The project will demonstrate the complete engineering path from mathematical definition to software implementation:

- direct DFT reference implementation;
- radix-2 decimation-in-time FFT;
- bit-reversal permutation;
- complex butterfly processing;
- forward FFT and inverse FFT;
- window functions;
- magnitude and phase spectra;
- power spectrum;
- power spectral density;
- frequency-axis generation;
- peak-frequency detection;
- numerical comparison with MATLAB and NumPy;
- runtime and memory benchmarking.

## Engineering Problem

The Discrete Fourier Transform converts a finite-length discrete-time signal from the time domain to the frequency domain.

A direct DFT requires approximately:

```text
O(N^2)
```

operations for an input containing `N` samples.

The Fast Fourier Transform reduces the computational complexity to approximately:

```text
O(N * log2(N))
```

for power-of-two input lengths.

This project will compare the direct DFT and radix-2 FFT in terms of:

- numerical accuracy;
- execution time;
- memory use;
- implementation complexity;
- frequency-domain analysis capability.

## Planned Processing Architecture

```text
Input Signal
    |
    v
Input Validation
    |
    v
Optional Window Function
    |
    v
Bit-Reversal Reordering
    |
    v
Radix-2 Butterfly Stages
    |
    v
Complex FFT Output
    |
    v
Magnitude, Phase, and Power Calculation
    |
    v
Frequency-Axis Generation
    |
    v
Peak Detection
    |
    v
Plots, CSV Results, Tests, and Benchmarks
```

## Mathematical Model

The equations in this README are written as plain text to ensure correct display on GitHub.

### Discrete Fourier Transform

For an input sequence `x[n]` of length `N`, the DFT is:

```text
X[k] = sum from n = 0 to N - 1 of:

       x[n] * exp(-j * 2 * pi * k * n / N)
```

Compact form:

```text
X[k] = SUM{x[n] * exp(-j * 2 * pi * k * n / N)}

n = 0, 1, ..., N - 1
k = 0, 1, ..., N - 1
```

where:

- `x[n]` is the input sequence;
- `X[k]` is the complex frequency-domain coefficient;
- `n` is the time-domain sample index;
- `k` is the frequency-bin index;
- `N` is the transform length;
- `j = sqrt(-1)`.

### Inverse Discrete Fourier Transform

The inverse DFT is:

```text
x[n] = (1/N) * sum from k = 0 to N - 1 of:

       X[k] * exp(j * 2 * pi * k * n / N)
```

Compact form:

```text
x[n] = (1/N) * SUM{X[k] * exp(j * 2 * pi * k * n / N)}
```

The factor `1/N` restores the original signal amplitude.

### Radix-2 FFT Decomposition

For an even transform length `N`, the input sequence is separated into:

```text
Even-indexed samples:

x_even[r] = x[2r]
```

```text
Odd-indexed samples:

x_odd[r] = x[2r + 1]
```

where:

```text
r = 0, 1, ..., N/2 - 1
```

The DFT can then be written as:

```text
X[k] = E[k] + W_N^k * O[k]
```

and:

```text
X[k + N/2] = E[k] - W_N^k * O[k]
```

for:

```text
k = 0, 1, ..., N/2 - 1
```

where:

- `E[k]` is the `N/2`-point DFT of the even-indexed samples;
- `O[k]` is the `N/2`-point DFT of the odd-indexed samples;
- `W_N^k` is the twiddle factor.

### Twiddle Factor

The FFT twiddle factor is:

```text
W_N^k = exp(-j * 2 * pi * k / N)
```

Equivalent rectangular form:

```text
W_N^k = cos(2 * pi * k / N)
        - j * sin(2 * pi * k / N)
```

### Radix-2 Butterfly

One radix-2 butterfly combines two complex input values.

The butterfly outputs are:

```text
Y0 = A + W_N^k * B
```

```text
Y1 = A - W_N^k * B
```

where:

- `A` is the first butterfly input;
- `B` is the second butterfly input;
- `W_N^k` is the twiddle factor;
- `Y0` is the upper butterfly output;
- `Y1` is the lower butterfly output.

### Number of FFT Stages

For a radix-2 FFT:

```text
Number of stages = log2(N)
```

The number of butterflies in each stage is:

```text
Butterflies per stage = N/2
```

The total number of butterflies is:

```text
Total butterflies = (N/2) * log2(N)
```

### Bit-Reversal Ordering

For an FFT length of:

```text
N = 2^m
```

each sample index is represented using `m` binary bits.

The bit-reversed index is produced by reversing the order of those bits.

Example for `N = 8`:

| Original index | Binary | Reversed binary | Reversed index |
|---:|---:|---:|---:|
| 0 | 000 | 000 | 0 |
| 1 | 001 | 100 | 4 |
| 2 | 010 | 010 | 2 |
| 3 | 011 | 110 | 6 |
| 4 | 100 | 001 | 1 |
| 5 | 101 | 101 | 5 |
| 6 | 110 | 011 | 3 |
| 7 | 111 | 111 | 7 |

The iterative radix-2 decimation-in-time FFT will use bit-reversed input ordering before the butterfly stages.

## Spectrum Analysis

### Magnitude Spectrum

For complex FFT value `X[k]`:

```text
Magnitude[k] = abs(X[k])
```

Equivalent form:

```text
Magnitude[k] =
sqrt(real(X[k])^2 + imag(X[k])^2)
```

### Normalized Magnitude Spectrum

A normalized double-sided magnitude spectrum is:

```text
Magnitude_normalized[k] = abs(X[k]) / N
```

### Phase Spectrum

The phase spectrum is:

```text
Phase[k] = atan2(imag(X[k]), real(X[k]))
```

The phase may be reported in:

- radians;
- degrees;
- wrapped form;
- unwrapped form.

### Power Spectrum

The power spectrum is:

```text
Power[k] = abs(X[k])^2
```

A normalized power spectrum may be calculated as:

```text
Power_normalized[k] = abs(X[k])^2 / N
```

### Frequency Axis

For sampling frequency `Fs` and FFT length `N`:

```text
Frequency[k] = k * Fs / N
```

The frequency-bin spacing is:

```text
Delta_f = Fs / N
```

where:

- `Fs` is the sampling frequency;
- `N` is the FFT length;
- `Delta_f` is the frequency resolution.

### Single-Sided Spectrum

For a real-valued signal, the positive-frequency bins are:

```text
k = 0, 1, ..., N/2
```

The single-sided amplitude spectrum is calculated from:

```text
Amplitude_single[k] = abs(X[k]) / N
```

The non-DC and non-Nyquist bins are multiplied by two:

```text
Amplitude_single[k] = 2 * abs(X[k]) / N
```

for:

```text
k = 1, 2, ..., N/2 - 1
```

The DC bin and Nyquist bin are not doubled.

### Peak-Frequency Detection

The dominant FFT bin is:

```text
k_peak = index of maximum magnitude
```

The corresponding estimated frequency is:

```text
f_peak = k_peak * Fs / N
```

The basic detector is limited to the FFT-bin resolution.

An optional future extension may use parabolic interpolation around the peak to estimate a sub-bin frequency.

## Power Spectral Density

A basic periodogram estimate is:

```text
PSD[k] = abs(X[k])^2 / (Fs * N)
```

When a window is applied, window-power correction is required.

For window samples `w[n]`, the window-power normalization is:

```text
U = (1/N) * sum from n = 0 to N - 1 of w[n]^2
```

The corrected periodogram is:

```text
PSD[k] = abs(X_windowed[k])^2 / (Fs * N * U)
```

where:

- `X_windowed[k]` is the FFT of the windowed signal;
- `U` is the window-power normalization factor.

## Parseval Energy Verification

The energy in the time domain should match the properly scaled frequency-domain energy.

```text
Time-domain energy =
sum from n = 0 to N - 1 of abs(x[n])^2
```

```text
Frequency-domain energy =
(1/N) * sum from k = 0 to N - 1 of abs(X[k])^2
```

Therefore:

```text
sum(abs(x[n])^2)
=
(1/N) * sum(abs(X[k])^2)
```

The difference between the two energies will be used as a verification metric.

## Window Functions

Window functions reduce spectral leakage when the observed signal does not contain an integer number of cycles inside the analysis interval.

### Rectangular Window

```text
w[n] = 1
```

for:

```text
n = 0, 1, ..., N - 1
```

### Hann Window

```text
w[n] =
0.5
- 0.5 * cos(2 * pi * n / (N - 1))
```

### Hamming Window

```text
w[n] =
0.54
- 0.46 * cos(2 * pi * n / (N - 1))
```

### Blackman Window

```text
w[n] =
0.42
- 0.5 * cos(2 * pi * n / (N - 1))
+ 0.08 * cos(4 * pi * n / (N - 1))
```

### Window Comparison

The windows will be compared using:

- main-lobe width;
- peak sidelobe level;
- leakage suppression;
- amplitude error;
- frequency resolution;
- equivalent noise bandwidth.

## Planned Features

### FFT Core

- direct DFT reference;
- radix-2 decimation-in-time FFT;
- iterative FFT implementation;
- optional recursive FFT reference;
- bit-reversal permutation;
- complex butterfly processing;
- in-place processing;
- out-of-place processing;
- inverse FFT;
- power-of-two input validation;
- configurable FFT length.

### Spectrum Analyzer

- optional input window;
- double-sided spectrum;
- single-sided spectrum;
- magnitude spectrum;
- phase spectrum;
- power spectrum;
- power spectral density;
- frequency-axis generation;
- dominant-peak detection;
- configurable sampling frequency;
- CSV result export;
- result plotting.

### Verification

- MATLAB FFT comparison;
- NumPy FFT comparison;
- direct DFT comparison;
- Parseval energy verification;
- FFT and IFFT round-trip verification;
- deterministic input vectors;
- numerical tolerance reporting;
- invalid-input tests.

### Benchmarking

- execution time;
- samples processed per second;
- memory usage;
- temporary-buffer size;
- DFT versus FFT comparison;
- MATLAB versus Python versus C++ comparison;
- algorithm complexity.

## Planned Software Architecture

```text
FFT Module
├── Input validation
├── Power-of-two check
├── Bit-reversal permutation
├── Twiddle-factor generation
├── Butterfly processing
├── Forward FFT
└── Inverse FFT

Spectrum Analyzer
├── Window selection
├── Window application
├── FFT execution
├── Magnitude calculation
├── Phase calculation
├── Power calculation
├── PSD calculation
├── Frequency-axis generation
└── Peak detection

Verification
├── Deterministic test-vector generation
├── MATLAB reference comparison
├── NumPy reference comparison
├── Parseval test
├── Round-trip test
└── Tolerance reporting

Benchmarking
├── Runtime measurement
├── Throughput measurement
├── Memory analysis
└── Complexity comparison
```

## Repository Structure

```text
01_fft_and_spectrum_analyzer/
├── README.md
├── matlab/
│   ├── dft_reference.m
│   ├── fft_radix2_reference.m
│   ├── spectrum_analyzer.m
│   ├── generate_test_signals.m
│   └── run_fft_analysis.m
├── python/
│   ├── dft_reference.py
│   ├── fft_radix2_reference.py
│   ├── spectrum_analyzer.py
│   ├── generate_test_signals.py
│   └── run_fft_analysis.py
├── cpp/
│   ├── include/
│   │   ├── fft_radix2.hpp
│   │   └── spectrum_analyzer.hpp
│   ├── src/
│   │   ├── fft_radix2.cpp
│   │   └── spectrum_analyzer.cpp
│   ├── CMakeLists.txt
│   └── main.cpp
├── tests/
│   ├── deterministic_vectors/
│   ├── test_fft_accuracy/
│   ├── test_fft_round_trip/
│   ├── test_spectrum_analysis/
│   └── test_invalid_inputs/
├── benchmarks/
│   ├── runtime/
│   ├── complexity/
│   └── memory/
└── results/
    ├── figures/
    ├── csv/
    ├── logs/
    └── reports/
```

The filenames above are currently planned.

After source files are created and connected to build scripts or imports, their exact filenames should be preserved.

## Implementation Roles

### MATLAB Reference Model

MATLAB will be used for:

- direct DFT reference;
- built-in FFT comparison;
- signal generation;
- window generation;
- spectrum calculation;
- reference-vector generation;
- numerical validation;
- plotting.

The custom MATLAB radix-2 FFT should be compared against:

```matlab
fft(x)
```

### Python Reference Model

Python will be used for:

- independent implementation;
- NumPy FFT comparison;
- SciPy window comparison;
- automated result processing;
- test automation;
- CSV generation;
- plotting.

Expected packages:

```text
NumPy
SciPy
Matplotlib
pandas
pytest
```

The custom Python FFT should be compared against:

```python
numpy.fft.fft(x)
```

### C++ Implementation

C++ will be used for:

- the main radix-2 FFT implementation;
- bit-reversal logic;
- iterative butterfly processing;
- complex arithmetic;
- spectrum analysis;
- reusable software interfaces;
- runtime benchmarking;
- memory-efficient processing.

The C++ FFT implementation should contain the FFT algorithm directly.

It should not only call an external FFT library.

An optimized FFT library may later be used as a performance baseline.

## Planned C++ Interface

A possible FFT interface is:

```cpp
class Radix2Fft {
public:
    explicit Radix2Fft(std::size_t fft_size);

    void forward(
        std::vector<std::complex<double>>& samples
    ) const;

    void inverse(
        std::vector<std::complex<double>>& samples
    ) const;

    std::size_t size() const noexcept;

private:
    std::size_t fft_size_;
};
```

A possible spectrum-analyzer interface is:

```cpp
struct SpectrumResult {
    std::vector<double> frequency_hz;
    std::vector<double> magnitude;
    std::vector<double> phase_rad;
    std::vector<double> power;
    std::vector<double> psd;
    std::size_t peak_bin;
    double peak_frequency_hz;
};

class SpectrumAnalyzer {
public:
    SpectrumResult analyze(
        const std::vector<std::complex<double>>& input,
        double sampling_frequency_hz
    ) const;
};
```

The final interface may change during implementation.

## Planned Test Cases

### Test 1: Impulse Input

Input:

```text
x[0] = 1
x[n] = 0 for n > 0
```

Expected result:

```text
X[k] = 1 for every frequency bin
```

This test verifies:

- basic FFT correctness;
- bit-reversal ordering;
- butterfly connectivity.

### Test 2: Constant Input

Input:

```text
x[n] = 1
```

Expected result:

```text
X[0] = N
X[k] = 0 for k != 0
```

within numerical tolerance.

This test verifies DC-bin scaling.

### Test 3: Single Complex Tone

Input:

```text
x[n] = exp(j * 2 * pi * k0 * n / N)
```

Expected result:

```text
The dominant FFT component appears at bin k0.
```

### Test 4: Real Sinusoid

Input:

```text
x[n] = cos(2 * pi * f0 * n / Fs)
```

Expected result:

```text
The spectrum contains symmetric positive-
and negative-frequency components.
```

### Test 5: Two-Tone Signal

Input:

```text
x[n] =
A1 * cos(2 * pi * f1 * n / Fs)
+
A2 * cos(2 * pi * f2 * n / Fs)
```

Expected result:

```text
Two dominant spectral peaks appear near f1 and f2.
```

### Test 6: Random Complex Input

The custom FFT output will be compared sample by sample with:

- MATLAB `fft`;
- NumPy `fft`;
- the direct DFT reference.

### Test 7: FFT and IFFT Round Trip

Processing:

```text
x[n]
  |
  v
FFT
  |
  v
IFFT
  |
  v
x_recovered[n]
```

The maximum reconstruction error should remain below a defined tolerance.

### Test 8: Parseval Energy

The time-domain and frequency-domain energies should agree within numerical tolerance.

### Test 9: Invalid Input Length

A non-power-of-two input should produce a clear error or an explicitly documented fallback.

Example invalid lengths:

```text
N = 10
N = 100
N = 1000
```

### Test 10: Window Comparison

The same non-bin-centered sinusoid will be evaluated with:

- rectangular window;
- Hann window;
- Hamming window;
- Blackman window.

The test will compare:

- leakage;
- main-lobe width;
- peak amplitude;
- sidelobe behavior.

### Test 11: Peak Detection

The detected peak frequency will be compared with the known input frequency.

### Test 12: Zero Input

Input:

```text
x[n] = 0
```

Expected result:

```text
All FFT outputs are zero.
```

### Test 13: Alternating Input

Input:

```text
x[n] = (-1)^n
```

Expected result:

```text
The dominant component appears at the Nyquist bin.
```

## Verification Metrics

| Metric | Purpose |
|---|---|
| Maximum absolute error | Largest difference from the reference output |
| RMS error | Average numerical difference |
| Relative error | Difference normalized by the reference magnitude |
| Parseval error | Time-domain and frequency-domain energy difference |
| Round-trip error | Difference after FFT and IFFT |
| Peak-bin error | Difference between expected and detected FFT bin |
| Peak-frequency error | Difference between true and detected frequency |
| Unit-test status | PASS or FAIL |
| Runtime | Execution time for one transform |
| Throughput | Samples processed per second |
| Memory use | Temporary and total memory requirements |

### Maximum Absolute Error

```text
Maximum absolute error =
maximum of abs(X_custom[k] - X_reference[k])
```

### RMS Error

```text
RMS error =
sqrt(
    sum(abs(X_custom[k] - X_reference[k])^2) / N
)
```

### Relative Error

```text
Relative error =
norm(X_custom - X_reference)
/
norm(X_reference)
```

### Parseval Error

```text
Parseval error =
abs(
    time-domain energy
    -
    frequency-domain energy
)
```

## Planned Numerical Tolerances

Initial floating-point tolerances may use values such as:

```text
Maximum absolute error <= 1e-10
Relative error <= 1e-10
Round-trip error <= 1e-10
```

The final tolerances will be selected based on:

- floating-point precision;
- FFT length;
- implementation language;
- accumulated rounding error.

The tolerance should not be chosen after looking at the result. It should be defined before accepting the test.

## Planned Benchmark Sizes

The benchmark may evaluate:

```text
N = 8
N = 16
N = 32
N = 64
N = 128
N = 256
N = 512
N = 1024
N = 2048
N = 4096
N = 8192
N = 16384
```

The direct DFT will be used only for practical reference sizes because its complexity grows as `N^2`.

## Planned Benchmark Results

The benchmark table may contain:

| FFT length | Direct DFT time | Custom FFT time | NumPy/MATLAB time | Maximum error | Memory use |
|---:|---:|---:|---:|---:|---:|
| 64 | Planned | Planned | Planned | Planned | Planned |
| 256 | Planned | Planned | Planned | Planned | Planned |
| 1024 | Planned | Planned | Planned | Planned | Planned |
| 4096 | Planned | Planned | Planned | Planned | Planned |

No benchmark values are claimed until the implementation has been executed.

## Planned Result Figures

The completed project may include:

- input time-domain signal;
- FFT magnitude spectrum;
- phase spectrum;
- power spectrum;
- power spectral density;
- single-sided spectrum;
- rectangular-window leakage;
- Hann-window leakage;
- Hamming-window leakage;
- Blackman-window leakage;
- two-tone resolution;
- peak-frequency detection;
- FFT error versus transform length;
- runtime versus transform length;
- direct DFT versus radix-2 FFT runtime;
- MATLAB versus Python versus C++ comparison.

No final result figures are currently included.

## Current Development Checklist

- [x] Project topic selected
- [x] Project folder created
- [x] Initial scope defined
- [x] GitHub-compatible README prepared
- [ ] Mathematical derivation completed
- [ ] MATLAB direct DFT implemented
- [ ] MATLAB radix-2 FFT implemented
- [ ] MATLAB spectrum analyzer implemented
- [ ] Python direct DFT implemented
- [ ] Python radix-2 FFT implemented
- [ ] Python spectrum analyzer implemented
- [ ] C++ radix-2 FFT implemented
- [ ] C++ spectrum analyzer implemented
- [ ] Bit-reversal logic verified
- [ ] Forward FFT verified
- [ ] Inverse FFT verified
- [ ] Window functions implemented
- [ ] Peak detector implemented
- [ ] Deterministic test vectors created
- [ ] MATLAB comparison completed
- [ ] NumPy comparison completed
- [ ] Parseval test completed
- [ ] FFT/IFFT round-trip test completed
- [ ] Invalid-input tests completed
- [ ] Runtime benchmarks completed
- [ ] Memory analysis completed
- [ ] Result figures generated
- [ ] Final technical documentation completed

This checklist will be updated as development progresses.

## Build and Run

The project is not yet ready for complete execution.

### Planned MATLAB Command

```matlab
run("matlab/run_fft_analysis.m")
```

### Planned Python Command

```bash
python python/run_fft_analysis.py
```

### Planned C++ Build

```bash
cmake -S cpp -B cpp/build
cmake --build cpp/build
```

### Planned C++ Execution

Linux or macOS:

```bash
./cpp/build/fft_spectrum_analyzer
```

Windows:

```text
cpp\build\Release\fft_spectrum_analyzer.exe
```

The final commands will be updated to match the actual source files and generated executable.

## Expected Engineering Outcomes

After completion, this project is expected to demonstrate:

- understanding of DFT mathematics;
- understanding of FFT decomposition;
- radix-2 butterfly implementation;
- bit-reversal indexing;
- complex-number processing;
- forward and inverse FFT implementation;
- spectral-window analysis;
- power spectral density calculation;
- deterministic numerical verification;
- MATLAB reference modeling;
- Python reference modeling;
- C++ implementation;
- CMake-based build configuration;
- runtime and memory benchmarking;
- technical documentation.

## Initial Limitations

The first implementation is expected to have the following limitations:

- radix-2 transform lengths only;
- power-of-two input sizes;
- single-threaded execution;
- floating-point processing;
- no SIMD optimization;
- no GPU acceleration;
- no mixed-radix implementation;
- no arbitrary-length FFT;
- no real-time IQ input;
- no hardware validation.

These limitations will be documented clearly.

## Future Extensions

Possible future extensions include:

- fixed-point FFT;
- Q15 processing;
- Q31 processing;
- saturation arithmetic;
- fixed-point scaling;
- real-input optimized FFT;
- mixed-radix FFT;
- Bluestein FFT;
- SIMD optimization;
- multithreaded processing;
- streaming FFT;
- overlap-save processing;
- overlap-add processing;
- real-time IQ input;
- waterfall spectrum display;
- spectrogram generation;
- comparison with FFTW;
- comparison with processor-optimized DSP libraries.

## Skills Demonstrated

After completion, this project is expected to demonstrate:

- DSP algorithm derivation;
- FFT implementation;
- spectral analysis;
- complex arithmetic;
- MATLAB;
- Python;
- C++;
- CMake;
- unit testing;
- regression testing;
- numerical cross-verification;
- runtime profiling;
- memory-aware implementation;
- technical reporting.

## License

A license will be selected before the source code is released as a stable package.

Until a `LICENSE` file is added, the project should be treated as:

```text
All rights reserved
```

## Author

**Md Moklesur Rahman**  
Wireless/RF/PHY and DSP System Engineer  
Finland

## Notice

This is an ongoing engineering project.

Features marked as planned have not yet been presented as completed or verified. Source code, tests, benchmarks, figures, and documentation will be updated progressively as development continues.
