# DSP Algorithm Design and Implementation

## Repository Overview

This repository contains compact, verification-driven Digital Signal Processing projects focused on algorithm design, reference modeling, C/C++ implementation, numerical validation, and performance benchmarking.

The repository currently contains two active projects:

1. FFT and Spectrum Analyzer
2. FIR, IIR, and Fixed-Point Digital Filters

MATLAB and Python are used as reference models. C and C++ are used for efficient, embedded-oriented, and real-time-oriented implementations.

## Repository Status

> **Ongoing portfolio — currently under active development**

The repository structure and project documentation have been created. Source code, deterministic tests, reference vectors, benchmark results, plots, and technical reports will be added progressively.

No project should be considered complete until:

- the mathematical model is documented;
- the MATLAB or Python reference model is implemented;
- the C or C++ implementation is available;
- deterministic tests pass;
- numerical differences remain within defined tolerances;
- runtime and memory results are reported;
- limitations are documented.

## Current Projects

| No. | Project | Main Technical Focus | Current Status |
|---:|---|---|---|
| 01 | [FFT and Spectrum Analyzer](./01_fft_and_spectrum_analyzer/) | Direct DFT, radix-2 FFT, bit-reversal, windowing, spectral analysis, PSD, peak detection, and benchmarking | **Ongoing — design and documentation in progress** |
| 02 | [FIR, IIR, and Fixed-Point Filters](./02_fir_iir_fixed_point_filters/) | FIR/IIR design, biquads, circular buffers, Q15/Q31 arithmetic, quantization, saturation, stability, and benchmarking | **Ongoing — design and documentation in progress** |

## Project 01: FFT and Spectrum Analyzer

### Objective

Design, implement, and verify a radix-2 FFT and windowed spectrum analyzer using:

- MATLAB as the primary mathematical reference;
- Python as an independent numerical reference;
- C++ as the main implementation language.

### Planned Technical Scope

- direct DFT reference implementation;
- radix-2 decimation-in-time FFT;
- iterative butterfly processing;
- bit-reversal permutation;
- forward FFT;
- inverse FFT;
- rectangular, Hann, Hamming, and Blackman windows;
- single-sided and double-sided spectra;
- magnitude and phase spectra;
- power spectrum;
- power spectral density;
- frequency-axis generation;
- peak-bin and peak-frequency detection;
- Parseval energy verification;
- runtime and memory benchmarking.

### Core DFT Model

For an input sequence `x[n]` of length `N`:

```text
X[k] = sum from n = 0 to N - 1 of:

       x[n] * exp(-j * 2 * pi * k * n / N)
```

The inverse transform is:

```text
x[n] = (1/N) * sum from k = 0 to N - 1 of:

       X[k] * exp(j * 2 * pi * k * n / N)
```

### Radix-2 Butterfly

The butterfly outputs are:

```text
Y0 = A + W_N^k * B
```

```text
Y1 = A - W_N^k * B
```

with:

```text
W_N^k = exp(-j * 2 * pi * k / N)
```

### Frequency Resolution

For sampling frequency `Fs` and FFT length `N`:

```text
Delta_f = Fs / N
```

### Project 01 Status

- [x] Project folder created
- [x] Project scope defined
- [x] Initial README created
- [x] GitHub-compatible equation formatting defined
- [ ] MATLAB direct DFT implemented
- [ ] MATLAB radix-2 FFT implemented
- [ ] Python direct DFT implemented
- [ ] Python radix-2 FFT implemented
- [ ] C++ radix-2 FFT implemented
- [ ] Bit-reversal verification completed
- [ ] FFT/IFFT round-trip test completed
- [ ] Window functions implemented
- [ ] Spectrum analyzer implemented
- [ ] Peak detector implemented
- [ ] MATLAB comparison completed
- [ ] NumPy comparison completed
- [ ] Parseval test completed
- [ ] Runtime benchmarks completed
- [ ] Memory analysis completed
- [ ] Final figures and report completed

## Project 02: FIR, IIR, and Fixed-Point Digital Filters

### Objective

Design, implement, and verify floating-point and fixed-point FIR and IIR filters using:

- MATLAB for filter design and reference analysis;
- Python for independent verification;
- C for fixed-point and embedded-oriented implementation;
- C++ for reusable floating-point and fixed-point filter modules.

### Planned Technical Scope

- FIR low-pass and high-pass filters;
- direct-form FIR;
- circular-buffer FIR;
- IIR biquad filters;
- Direct Form I;
- Direct Form II Transposed;
- cascaded second-order sections;
- Q15 arithmetic;
- optional Q31 arithmetic;
- coefficient quantization;
- accumulator-width analysis;
- rounding and truncation;
- saturation arithmetic;
- overflow monitoring;
- IIR pole-stability verification;
- limit-cycle analysis;
- runtime and memory benchmarking.

### FIR Model

For an FIR filter of order `M`:

```text
y[n] = sum from k = 0 to M of:

       b[k] * x[n - k]
```

where:

- `x[n]` is the input sample;
- `y[n]` is the output sample;
- `b[k]` is FIR coefficient `k`;
- `M + 1` is the number of taps.

### IIR Model

A general IIR filter is:

```text
y[n] =
sum from k = 0 to M of b[k] * x[n - k]
-
sum from r = 1 to N of a[r] * y[n - r]
```

### Biquad Model

A second-order IIR section is:

```text
y[n] =
b0 * x[n]
+ b1 * x[n - 1]
+ b2 * x[n - 2]
- a1 * y[n - 1]
- a2 * y[n - 2]
```

### Q15 Representation

A Q15 integer represents a real value as:

```text
real_value = stored_integer / 2^15
```

The approximate range is:

```text
-1.0 <= value <= 1.0 - 2^(-15)
```

The quantization step is:

```text
Delta = 2^(-15)
```

### Fixed-Point Conversion

Floating-point to Q15 conversion:

```text
q15_integer = round(real_value * 2^15)
```

The integer is then saturated to:

```text
-32768 to 32767
```

### Fixed-Point Multiplication

```text
Q15 * Q15 -> Q30 intermediate result
```

The result is returned to Q15 using a right shift:

```text
output_q15 = product_q30 >> 15
```

Rounding and saturation will be handled explicitly.

### IIR Stability Condition

A stable causal IIR filter requires:

```text
Magnitude of every pole < 1
```

The floating-point and quantized pole locations will both be checked.

### Project 02 Status

- [x] Project folder created
- [x] Project scope defined
- [x] Initial README created
- [x] GitHub-compatible equation formatting defined
- [ ] MATLAB FIR design implemented
- [ ] MATLAB IIR design implemented
- [ ] Python FIR model implemented
- [ ] Python IIR model implemented
- [ ] C Q15 FIR implemented
- [ ] C Q15 biquad implemented
- [ ] C++ floating-point FIR implemented
- [ ] C++ circular-buffer FIR implemented
- [ ] C++ biquad implementation completed
- [ ] Coefficient quantization implemented
- [ ] Saturation arithmetic implemented
- [ ] Overflow monitoring implemented
- [ ] FIR impulse-response test completed
- [ ] FIR frequency-response test completed
- [ ] IIR impulse-response test completed
- [ ] Quantized-pole stability test completed
- [ ] Fixed-point accuracy test completed
- [ ] Limit-cycle test completed
- [ ] MATLAB cross-verification completed
- [ ] Python cross-verification completed
- [ ] Runtime benchmarks completed
- [ ] Memory analysis completed
- [ ] Final figures and report completed

## Common Engineering Methodology

Each project follows the same development workflow.

1. Define the engineering problem.
2. Write the mathematical model.
3. Define dimensions, units, normalization, and numerical conventions.
4. Implement a MATLAB reference model.
5. Implement an independent Python model.
6. Implement the main C or C++ version.
7. Generate deterministic input vectors.
8. Compare stage-by-stage outputs.
9. Add unit and regression tests.
10. Measure numerical error.
11. Measure runtime and memory use.
12. Generate result figures and benchmark tables.
13. Document limitations and future improvements.

## Reference and Implementation Roles

| Language | Main Role |
|---|---|
| MATLAB | Mathematical modeling, reference calculations, filter design, signal generation, and verification |
| Python | Independent reference implementation, automated analysis, plotting, and test support |
| C | Fixed-point and embedded-oriented DSP implementation |
| C++ | Main reusable DSP implementation, benchmarking, and software architecture |

## Verification Strategy

The projects will not rely only on visually reasonable plots.

Verification will include:

- deterministic test vectors;
- fixed random seeds where random data are used;
- analytical test cases;
- sample-by-sample reference comparison;
- maximum absolute error;
- RMS error;
- relative error;
- frequency-response comparison;
- Parseval energy checking;
- FFT/IFFT round-trip checking;
- FIR impulse-response checking;
- IIR stability checking;
- saturation and overflow monitoring;
- fixed-point output-SNR analysis;
- runtime and memory benchmarking.

## Common Verification Metrics

### Maximum Absolute Error

```text
Maximum absolute error =
maximum of abs(output_implementation - output_reference)
```

### RMS Error

```text
RMS error =
sqrt(
    sum((output_implementation - output_reference)^2)
    /
    number of samples
)
```

### Relative Error

```text
Relative error =
norm(output_implementation - output_reference)
/
norm(output_reference)
```

### Mean-Square Error

```text
MSE =
sum(abs(reference[n] - estimate[n])^2)
/
number of samples
```

### Output SNR

```text
error[n] = fixed_output[n] - reference_output[n]
```

```text
Output SNR in dB =
10 * log10(
    sum(reference_output[n]^2)
    /
    sum(error[n]^2)
)
```

## Repository Structure

```text
DSP-Algorithm-Design-and-Implementation/
├── README.md
├── 01_fft_and_spectrum_analyzer/
│   ├── README.md
│   ├── matlab/
│   ├── python/
│   ├── cpp/
│   ├── tests/
│   ├── benchmarks/
│   └── results/
└── 02_fir_iir_fixed_point_filters/
    ├── README.md
    ├── matlab/
    ├── python/
    ├── c/
    ├── cpp/
    ├── tests/
    ├── benchmarks/
    └── results/
```

Only the two current project folders are shown because they are the projects presently created in this repository.

## Folder Purpose

| Folder | Purpose |
|---|---|
| `matlab/` | MATLAB reference models |
| `python/` | Independent Python verification models |
| `c/` | C fixed-point and embedded-oriented implementation |
| `cpp/` | Main C++ implementation |
| `tests/` | Unit, regression, and deterministic-vector tests |
| `benchmarks/` | Runtime, memory, and complexity measurements |
| `results/` | Figures, CSV files, logs, tables, and reports |

## Software Tools

### MATLAB

Planned use includes:

- signal generation;
- direct DFT and FFT reference;
- filter design;
- frequency-response analysis;
- impulse and step responses;
- pole-zero analysis;
- reference-vector generation;
- result plotting.

### Python

Expected packages include:

```text
NumPy
SciPy
Matplotlib
pandas
pytest
```

### C and C++

Planned tools include:

```text
C11 or later
C++17 or later
CMake
GCC
Clang
Microsoft Visual C++
CTest or another unit-test framework
```

## Coding Principles

The implementations will follow these principles:

- use clear input and output contracts;
- validate array and buffer sizes;
- avoid unnecessary allocation inside processing loops;
- separate reference code from optimized code;
- use deterministic tests;
- define numerical tolerances before accepting results;
- use fixed-width integer types for fixed-point processing;
- apply saturation intentionally;
- record overflow and saturation events;
- preserve exact source filenames after build and import dependencies are created;
- measure performance before optimization.

## Benchmarking Plan

Each project will report relevant measurements such as:

- execution time;
- samples processed per second;
- operations per sample;
- memory consumption;
- temporary-buffer use;
- floating-point versus fixed-point error;
- direct versus optimized implementation speed;
- reference versus C/C++ implementation speed.

Benchmark conditions will be documented so the results can be reproduced.

## Repository Development Status

- [x] Repository created
- [x] Root README prepared
- [x] Project 01 folder created
- [x] Project 01 README prepared
- [x] Project 02 folder created
- [x] Project 02 README prepared
- [x] Root equation formatting converted to GitHub-compatible plain text
- [x] Project status added for both active projects
- [ ] Project 01 source implementation completed
- [ ] Project 01 verification completed
- [ ] Project 01 benchmark results added
- [ ] Project 02 source implementation completed
- [ ] Project 02 verification completed
- [ ] Project 02 benchmark results added
- [ ] Automated test workflow added
- [ ] Stable release prepared

## Planned Future Expansion

Additional projects may be added after the first two projects are implemented and verified.

Possible future projects include:

```text
03_polyphase_sample_rate_converter
04_lms_nlms_adaptive_noise_canceller
05_timing_and_frequency_synchronization
06_streaming_iq_dsp_pipeline
```

These projects are planned ideas and are not currently presented as active repository contents.

## Skills Demonstrated

After completion, this repository is expected to demonstrate:

- Digital Signal Processing;
- DFT and FFT implementation;
- spectrum analysis;
- FIR filter design;
- IIR filter design;
- fixed-point arithmetic;
- coefficient quantization;
- saturation and overflow handling;
- IIR stability analysis;
- MATLAB modeling;
- Python verification;
- C implementation;
- C++ implementation;
- CMake;
- unit and regression testing;
- numerical cross-verification;
- runtime profiling;
- memory-aware design;
- technical documentation.

## How to Use This Repository

Open the relevant project folder and read its `README.md`.

Each project README describes:

- project objective;
- mathematical model;
- planned algorithms;
- implementation structure;
- verification tests;
- benchmark plan;
- development status;
- limitations;
- build and execution instructions.

The projects are not yet complete executable releases. Final run instructions will be added after the corresponding source implementations are available and verified.

## License

A repository license will be selected before the source code is released as a stable package.

Until a `LICENSE` file is added, this repository should be treated as:

```text
All rights reserved
```

## Author

**Md Moklesur Rahman**  
Wireless/RF/PHY and DSP System Engineer  
Finland

## Notice

This repository contains ongoing engineering projects.

Features marked as planned have not yet been presented as completed or verified. Source code, tests, benchmarks, figures, and documentation will be updated progressively as development continues.
