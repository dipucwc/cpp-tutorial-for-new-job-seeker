# FFT and Spectrum Analyzer

## Project Status

> **Ongoing project — currently under active development**

This project is being developed as a compact DSP algorithm design and implementation study. MATLAB and Python will be used as numerical reference models, while C/C++ will be used for the main implementation.

Source code, tests, benchmark results, figures, and technical notes will be added progressively. No final performance claim is made at the current stage.

## Project Objective

The objective is to design, implement, and verify a radix-2 FFT and windowed spectrum analyzer.

The project will demonstrate the complete engineering path from mathematical definition to software implementation:

- direct DFT reference;
- radix-2 decimation-in-time FFT;
- bit-reversal ordering;
- complex butterfly processing;
- signal windowing;
- magnitude and power spectra;
- power spectral density;
- peak-frequency detection;
- numerical comparison with MATLAB and NumPy;
- runtime and memory benchmarking.

## Engineering Problem

A direct discrete Fourier transform requires approximately

```text
O(N²)
```

complex operations for an input of length \(N\).

The FFT reduces the computational complexity to approximately

```text
O(N log2 N)
```

for power-of-two input lengths.

This project will compare the direct DFT and radix-2 FFT in terms of:

- numerical accuracy;
- execution time;
- memory use;
- implementation complexity;
- spectral-analysis capability.

## Mathematical Model

### Discrete Fourier Transform

For a sequence \(x[n]\) of length \(N\), the DFT is

$$
X[k]
=
\sum_{n=0}^{N-1}
x[n]
e^{-j2\pi kn/N},
$$

where:

- \(x[n]\) is the input sequence;
- \(X[k]\) is the complex frequency-domain coefficient;
- \(n\) is the time-domain sample index;
- \(k\) is the frequency-bin index;
- \(j=\sqrt{-1}\).

### Inverse Discrete Fourier Transform

The inverse DFT is

$$
x[n]
=
\frac{1}{N}
\sum_{k=0}^{N-1}
X[k]
e^{j2\pi kn/N}.
$$

### Radix-2 Butterfly

For a radix-2 decimation-in-time FFT, one butterfly stage combines two values using

$$
Y_0
=
A
+
W_N^k B,
$$

$$
Y_1
=
A
-
W_N^k B,
$$

with

$$
W_N^k
=
e^{-j2\pi k/N}.
$$

Here:

- \(A\) and \(B\) are the butterfly inputs;
- \(Y_0\) and \(Y_1\) are the butterfly outputs;
- \(W_N^k\) is the twiddle factor.

## Planned Features

### FFT Core

- direct DFT reference implementation;
- radix-2 decimation-in-time FFT;
- iterative butterfly implementation;
- recursive reference implementation;
- bit-reversal permutation;
- in-place processing;
- out-of-place processing;
- inverse FFT;
- power-of-two input-length checking.

### Window Functions

- rectangular;
- Hann;
- Hamming;
- Blackman.

### Spectrum Analysis

- complex spectrum;
- magnitude spectrum;
- phase spectrum;
- power spectrum;
- single-sided spectrum;
- double-sided spectrum;
- power spectral density;
- frequency-axis generation;
- peak-bin detection;
- sub-bin peak interpolation as an optional extension;
- spectral-leakage analysis;
- frequency-resolution analysis.

### Verification and Benchmarking

- MATLAB reference comparison;
- NumPy reference comparison;
- Parseval energy check;
- maximum absolute error;
- RMS error;
- runtime measurement;
- memory-use analysis;
- complexity comparison.

## Planned Software Architecture

```text
Input Signal
    ↓
Input Validation
    ↓
Optional Windowing
    ↓
Bit-Reversal Reordering
    ↓
Radix-2 Butterfly Stages
    ↓
Complex FFT Output
    ↓
Magnitude / Phase / Power Calculation
    ↓
Frequency-Axis Generation
    ↓
Peak Detection
    ↓
Plots, CSV Results, and Benchmarks
```

## Planned Repository Structure

```text
01_fft_and_spectrum_analyzer/
├── README.md
├── matlab/
│   ├── dft_reference.m
│   ├── fft_radix2_reference.m
│   ├── spectrum_analyzer.m
│   └── run_fft_analysis.m
├── python/
│   ├── dft_reference.py
│   ├── fft_radix2_reference.py
│   ├── spectrum_analyzer.py
│   └── run_fft_analysis.py
├── cpp/
│   ├── include/
│   ├── src/
│   ├── CMakeLists.txt
│   └── main.cpp
├── tests/
│   ├── deterministic_vectors/
│   ├── test_fft_accuracy/
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

The filenames above are planned and may change during implementation. After source files are integrated, their exact filenames should be preserved to avoid breaking build scripts and tests.

## Reference and Implementation Models

### MATLAB

MATLAB will be used for:

- direct DFT reference;
- FFT comparison;
- window generation;
- spectral plots;
- numerical verification;
- reference-vector generation.

### Python

Python will be used for:

- independent reference verification;
- NumPy FFT comparison;
- automated result analysis;
- CSV processing;
- plotting;
- optional test automation.

Expected packages:

```text
NumPy
SciPy
Matplotlib
pandas
pytest
```

### C++

C++ will be used for:

- the main radix-2 FFT implementation;
- complex-number processing;
- bit-reversal logic;
- iterative butterfly stages;
- reusable spectrum-analyzer functions;
- runtime benchmarking;
- memory-efficient processing.

The main C++ FFT should implement the butterfly operations directly rather than only calling a library FFT.

## Planned Test Cases

### Test 1: Impulse Input

Input:

```text
x[0] = 1
x[n] = 0 for n > 0
```

Expected result:

```text
All FFT bins have equal magnitude.
```

### Test 2: Constant Input

Input:

```text
x[n] = 1
```

Expected result:

```text
Only the DC bin is nonzero within numerical tolerance.
```

### Test 3: Single Complex Tone

Input:

```text
x[n] = exp(j 2π k0 n / N)
```

Expected result:

```text
The dominant FFT peak appears at bin k0.
```

### Test 4: Two-Tone Signal

The spectrum should contain two dominant peaks at the configured frequencies.

### Test 5: Real Sinusoid

A real sinusoid should produce symmetric positive- and negative-frequency components.

### Test 6: Random Complex Input

The custom FFT output will be compared sample by sample with MATLAB and NumPy.

### Test 7: Parseval Energy Check

The time-domain and frequency-domain energies should satisfy

$$
\sum_{n=0}^{N-1}
|x[n]|^2
=
\frac{1}{N}
\sum_{k=0}^{N-1}
|X[k]|^2.
$$

### Test 8: Invalid Input Length

A non-power-of-two input should be rejected or handled through an explicitly documented fallback.

### Test 9: Window Comparison

The project will compare:

- main-lobe width;
- sidelobe level;
- spectral leakage;
- amplitude accuracy.

## Planned Verification Metrics

| Metric | Purpose |
|---|---|
| Maximum absolute error | Largest difference from the reference FFT |
| RMS error | Average numerical difference |
| Relative error | Error normalized to reference magnitude |
| Parseval error | Energy-consistency check |
| Peak-frequency error | Difference between true and detected peak |
| Runtime | Execution time for each FFT length |
| Throughput | Samples processed per second |
| Memory use | Temporary and total buffer requirements |
| Complexity | Estimated operation count |
| Unit-test result | PASS or FAIL |

## Planned Benchmark Sizes

The benchmark may evaluate:

```text
N = 8
N = 16
N = 64
N = 256
N = 1024
N = 4096
N = 16384
```

The direct DFT will be used only for practical reference sizes because of its quadratic complexity.

## Planned Result Figures

The completed project may include:

- input time-domain waveform;
- magnitude spectrum;
- phase spectrum;
- power spectral density;
- rectangular versus windowed spectrum;
- spectral leakage comparison;
- single-tone peak detection;
- two-tone resolution comparison;
- numerical error versus FFT length;
- runtime versus FFT length;
- DFT versus FFT runtime;
- C++ versus MATLAB and Python output comparison.

No final result figures are included while the implementation is still under development.

## Current Development Checklist

- [x] Project topic selected
- [x] Project folder created
- [x] Initial project scope defined
- [x] Initial README prepared
- [ ] Mathematical derivation completed
- [ ] MATLAB DFT reference implemented
- [ ] MATLAB FFT reference implemented
- [ ] Python DFT reference implemented
- [ ] Python FFT reference implemented
- [ ] C++ radix-2 FFT implemented
- [ ] Bit-reversal logic verified
- [ ] Window functions implemented
- [ ] Spectrum analyzer implemented
- [ ] Peak detector implemented
- [ ] Deterministic test vectors created
- [ ] MATLAB comparison completed
- [ ] Python comparison completed
- [ ] Parseval test completed
- [ ] Runtime benchmarks completed
- [ ] Memory analysis completed
- [ ] Result figures generated
- [ ] Technical report completed

This checklist will be updated as development progresses.

## Build and Run

The project is not yet ready for complete execution.

Planned MATLAB command:

```matlab
run("matlab/run_fft_analysis.m")
```

Planned Python command:

```bash
python python/run_fft_analysis.py
```

Planned C++ build:

```bash
cmake -S cpp -B cpp/build
cmake --build cpp/build
```

Planned C++ execution:

```bash
./cpp/build/fft_spectrum_analyzer
```

The final commands will be updated to match the actual source filenames and generated executable.

## Expected Engineering Outcomes

After completion, this project is expected to demonstrate:

- understanding of DFT and FFT mathematics;
- radix-2 butterfly implementation;
- complex-number processing;
- bit-reversal indexing;
- spectral windowing;
- frequency-domain analysis;
- deterministic numerical testing;
- MATLAB and Python reference modeling;
- C++ implementation;
- runtime and memory benchmarking;
- clear engineering documentation.

## Initial Limitations

The first implementation is expected to have the following limitations:

- radix-2 input lengths only;
- single-threaded execution;
- floating-point processing;
- no SIMD optimization;
- no GPU acceleration;
- no mixed-radix FFT;
- no streaming overlap-save processing;
- no real-time hardware validation.

These limitations will be documented and extended only when useful.

## Future Extensions

Possible extensions include:

- fixed-point FFT;
- Q15 or Q31 butterfly processing;
- real-input optimized FFT;
- mixed-radix FFT;
- Bluestein FFT for arbitrary lengths;
- SIMD optimization;
- multithreaded FFT;
- streaming spectrum analysis;
- overlap-save and overlap-add processing;
- waterfall spectrum display;
- real-time IQ input;
- comparison with FFTW or another optimized library.

## Skills Demonstrated

After completion, the project is expected to demonstrate:

- DSP algorithm derivation;
- FFT implementation;
- complex arithmetic;
- spectral analysis;
- MATLAB;
- Python;
- C++;
- CMake;
- unit testing;
- numerical cross-verification;
- runtime profiling;
- memory-aware implementation;
- technical reporting.

## License

A license will be selected before the source code is released as a stable package.

Until a `LICENSE` file is added, this project should be treated as **all rights reserved**.

## Author

**Md Moklesur Rahman**  
Wireless/RF/PHY and DSP System Engineer  
Finland

## Notice

This is an ongoing engineering project.

Features marked as planned have not yet been presented as completed or verified. Source code, tests, benchmarks, figures, and documentation will be updated progressively.
