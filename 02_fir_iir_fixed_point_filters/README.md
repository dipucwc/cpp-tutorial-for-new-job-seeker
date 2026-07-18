# FIR, IIR, and Fixed-Point Digital Filters

## Project Status

> **Ongoing project- currently under active development**

This project focuses on the design, implementation, verification, and performance analysis of floating-point and fixed-point FIR and IIR digital filters.

MATLAB and Python will be used as numerical reference models. C and C++ will be used for the main software implementations.

Source code, deterministic test vectors, fixed-point analysis, benchmark results, figures, and technical documentation will be added progressively.

The project is not yet presented as a completed or fully validated software release.

## Project Objective

The objective is to design and verify practical FIR and IIR filters and then convert the floating-point algorithms into fixed-point implementations suitable for embedded and real-time DSP systems.

The project will demonstrate the complete engineering path from mathematical filter design to implementation:

- FIR filter design;
- IIR filter design;
- direct-form filtering;
- circular-buffer processing;
- biquad implementation;
- floating-point reference modeling;
- coefficient quantization;
- Q15 and optional Q31 arithmetic;
- saturation and overflow handling;
- filter-stability analysis;
- C and C++ implementation;
- MATLAB and Python cross-verification;
- runtime and memory benchmarking.

## Engineering Problem

Digital filters are widely used for:

- noise reduction;
- channel selection;
- anti-aliasing;
- interference suppression;
- signal smoothing;
- equalization;
- audio processing;
- wireless baseband processing;
- sensor-signal processing.

A floating-point filter can normally represent a wide numerical range. Embedded DSP hardware may instead use fixed-point arithmetic because it can provide:

- lower computational cost;
- lower power consumption;
- predictable memory use;
- deterministic execution;
- efficient hardware implementation.

However, fixed-point implementation introduces engineering challenges:

- coefficient quantization;
- input and output scaling;
- accumulator growth;
- overflow;
- saturation;
- rounding error;
- limit cycles;
- IIR stability changes;
- output-noise degradation.

This project will study these effects systematically.

## Project Scope

The planned project contains four main technical areas:

1. floating-point FIR filter design and implementation;
2. floating-point IIR biquad design and implementation;
3. fixed-point FIR and IIR conversion;
4. verification, benchmarking, and numerical analysis.

## Planned Processing Architecture

```text
Input Signal
    |
    v
Input Validation
    |
    v
Optional Input Scaling
    |
    v
FIR or IIR Filter
    |
    v
Floating-Point or Fixed-Point Arithmetic
    |
    v
Saturation and Overflow Monitoring
    |
    v
Output Signal
    |
    v
Reference Comparison
    |
    v
Error, Frequency-Response, and Runtime Analysis
```

## Mathematical Model

The equations in this README are written as plain text so that they display correctly on GitHub without LaTeX support.

## FIR Filter Model

A causal FIR filter of order `M` has `M + 1` coefficients.

The output is:

```text
y[n] = sum from k = 0 to M of:

       b[k] * x[n - k]
```

Compact form:

```text
y[n] = SUM{b[k] * x[n - k]}
```

where:

- `x[n]` is the input sample;
- `y[n]` is the output sample;
- `b[k]` is FIR coefficient `k`;
- `M` is the filter order;
- `M + 1` is the number of taps.

### FIR Transfer Function

The FIR transfer function is:

```text
H(z) = b[0]
       + b[1] * z^(-1)
       + b[2] * z^(-2)
       + ...
       + b[M] * z^(-M)
```

Compact form:

```text
H(z) = sum from k = 0 to M of b[k] * z^(-k)
```

### FIR Frequency Response

The discrete-frequency response is:

```text
H(exp(j*w)) =
sum from k = 0 to M of:

b[k] * exp(-j * w * k)
```

where:

- `w` is digital angular frequency in radians per sample;
- `j = sqrt(-1)`.

### FIR Filter Order and Number of Taps

```text
Number of taps = Filter order + 1
```

Example:

```text
Filter order = 31
Number of taps = 32
```

## FIR Direct-Form Implementation

A direct FIR implementation calculates:

```text
y[n] =
b[0] * x[n]
+ b[1] * x[n - 1]
+ b[2] * x[n - 2]
+ ...
+ b[M] * x[n - M]
```

The implementation requires:

```text
M + 1 multiplications per output sample
```

and approximately:

```text
M additions per output sample
```

## FIR Circular-Buffer Implementation

A circular buffer avoids shifting all stored samples after every new input.

The processing sequence is:

```text
1. Write the newest sample into the current buffer position.
2. Multiply the newest and previous samples by the FIR coefficients.
3. Accumulate the products.
4. Move the write index.
5. Wrap the index when the end of the buffer is reached.
```

The circular-buffer implementation will be compared with the sample-shifting implementation in terms of:

- output accuracy;
- runtime;
- memory operations;
- implementation complexity.

## IIR Filter Model

A general causal IIR filter is described by:

```text
y[n] =
sum from k = 0 to M of b[k] * x[n - k]
-
sum from r = 1 to N of a[r] * y[n - r]
```

where:

- `b[k]` are feedforward coefficients;
- `a[r]` are feedback coefficients;
- `M` is the feedforward order;
- `N` is the feedback order.

The coefficient `a[0]` is normally normalized to:

```text
a[0] = 1
```

## IIR Transfer Function

The IIR transfer function is:

```text
              b[0] + b[1]z^(-1) + ... + b[M]z^(-M)
H(z) = -------------------------------------------------
              1 + a[1]z^(-1) + ... + a[N]z^(-N)
```

The zeros are determined by the numerator.

The poles are determined by the denominator.

A causal IIR filter is stable when all poles are inside the unit circle:

```text
Magnitude of every pole < 1
```

## Second-Order Biquad Filter

A second-order IIR section is called a biquad.

Its difference equation is:

```text
y[n] =
b0 * x[n]
+ b1 * x[n - 1]
+ b2 * x[n - 2]
- a1 * y[n - 1]
- a2 * y[n - 2]
```

The transfer function is:

```text
              b0 + b1*z^(-1) + b2*z^(-2)
H(z) = -------------------------------------------
              1 + a1*z^(-1) + a2*z^(-2)
```

Higher-order IIR filters will be implemented as cascades of biquad sections.

## Direct Form I

Direct Form I stores separate input and output delay lines.

The main operations are:

```text
Feedforward part:
b0*x[n] + b1*x[n-1] + b2*x[n-2]

Feedback part:
a1*y[n-1] + a2*y[n-2]

Output:
Feedforward part - Feedback part
```

Advantages:

- clear correspondence with the difference equation;
- separate input and output states;
- simple mathematical interpretation.

Disadvantages:

- more delay storage than Direct Form II;
- fixed-point internal scaling must still be controlled.

## Direct Form II Transposed

Direct Form II Transposed uses internal state variables.

For one biquad, a possible sample-by-sample form is:

```text
y[n] = b0*x[n] + state1
```

```text
new_state1 =
b1*x[n]
- a1*y[n]
+ state2
```

```text
new_state2 =
b2*x[n]
- a2*y[n]
```

Then:

```text
state1 = new_state1
state2 = new_state2
```

This structure is widely used in practical DSP implementations because it has:

- low state-memory requirements;
- efficient sample-by-sample processing;
- convenient biquad cascading.

## Planned Filter Types

### FIR Filters

The project may include:

- low-pass FIR;
- high-pass FIR;
- band-pass FIR;
- band-stop FIR;
- moving-average FIR;
- optional Hilbert-transform FIR.

### IIR Filters

The project may include:

- Butterworth low-pass;
- Butterworth high-pass;
- Chebyshev Type I;
- Chebyshev Type II;
- elliptic filter;
- notch filter;
- cascaded biquad implementation.

The first stable implementation will focus on a smaller verified set before additional filter families are added.

## FIR Window-Based Design

An ideal low-pass impulse response has infinite length.

A practical FIR filter can be formed by truncating the ideal impulse response using a window.

For cutoff frequency `fc` and sampling frequency `Fs`, define:

```text
normalized cutoff = fc / Fs
```

A simplified ideal low-pass impulse response is:

```text
h_ideal[n] =
2 * fc / Fs
```

at the center sample.

For other samples:

```text
h_ideal[n] =
sin(
    2 * pi * fc * (n - M/2) / Fs
)
/
(
    pi * (n - M/2)
)
```

The practical FIR coefficients are:

```text
b[n] = h_ideal[n] * w[n]
```

where `w[n]` is the selected window.

Planned windows include:

- rectangular;
- Hann;
- Hamming;
- Blackman.

## Magnitude Response

For complex frequency response `H[k]`, the magnitude is:

```text
Magnitude[k] = abs(H[k])
```

The magnitude in decibels is:

```text
Magnitude_dB[k] =
20 * log10(abs(H[k]))
```

A small lower limit should be used before taking the logarithm to avoid:

```text
log10(0)
```

## Phase Response

The phase response is:

```text
Phase[k] =
atan2(imag(H[k]), real(H[k]))
```

The phase may be shown as:

- wrapped phase;
- unwrapped phase;
- radians;
- degrees.

## Group Delay

Group delay describes the delay applied to frequency components.

A numerical approximation is:

```text
Group delay =
-negative derivative of phase with respect to angular frequency
```

Compact form:

```text
GroupDelay(w) = -d(Phase(w)) / dw
```

A linear-phase FIR filter should have approximately constant group delay in its passband.

For a symmetric FIR filter of order `M`:

```text
Group delay = M / 2 samples
```

## Fixed-Point Arithmetic

## Q-Format Representation

A signed fixed-point value can be represented using:

```text
Qm.n
```

where:

- `m` is the number of integer bits excluding or including the sign depending on the stated convention;
- `n` is the number of fractional bits.

This project will state the exact convention explicitly.

For signed Q15 processing, the stored integer typically represents:

```text
real_value = stored_integer / 2^15
```

The approximate representable range is:

```text
-1.0 <= value <= 1.0 - 2^(-15)
```

The quantization step is:

```text
Delta = 2^(-15)
```

For Q31:

```text
real_value = stored_integer / 2^31
```

with quantization step:

```text
Delta = 2^(-31)
```

## Floating-Point to Fixed-Point Conversion

A floating-point value `x` can be converted to Q15 using:

```text
q15_integer = round(x * 2^15)
```

The result must then be limited to the signed 16-bit range:

```text
-32768 to 32767
```

The reconstructed value is:

```text
x_quantized = q15_integer / 2^15
```

For Q31:

```text
q31_integer = round(x * 2^31)
```

followed by saturation to the signed 32-bit integer range.

## Coefficient Quantization

For floating-point coefficient `b[k]`, Q15 quantization is:

```text
b_q15[k] = round(b[k] * 2^15)
```

The quantized floating-point equivalent is:

```text
b_quantized[k] = b_q15[k] / 2^15
```

Coefficient quantization error is:

```text
coefficient_error[k] =
b_quantized[k] - b[k]
```

The project will evaluate how coefficient quantization changes:

- cutoff frequency;
- passband ripple;
- stopband attenuation;
- phase response;
- pole location;
- filter stability.

## Fixed-Point Multiplication

Multiplying two Q15 values gives a Q30 intermediate result:

```text
Q15 * Q15 -> Q30
```

Example:

```text
product_q30 =
input_q15 * coefficient_q15
```

To return to Q15:

```text
output_q15 =
rounded_product_q30 shifted right by 15 bits
```

A simple truncating conversion is:

```text
output_q15 = product_q30 >> 15
```

A rounded conversion may add a rounding offset before the shift.

For positive values:

```text
output_q15 =
(product_q30 + 2^14) >> 15
```

Signed rounding behavior must be implemented carefully.

## Accumulator Growth

For an FIR filter with `T` taps, the accumulator may require additional bits.

An approximate guard-bit requirement is:

```text
guard bits = ceil(log2(T))
```

Example:

```text
T = 32 taps
guard bits = 5
```

A Q15 FIR implementation may therefore use:

- 16-bit input;
- 16-bit coefficients;
- 32-bit products;
- 64-bit accumulator for additional safety;
- saturation to 16-bit output.

The exact accumulator choice will be documented and verified.

## Saturation Arithmetic

Without saturation, integer overflow can wrap around and create a large incorrect value.

Saturation limits the result to the representable range.

For signed 16-bit output:

```text
if value > 32767:
    value = 32767
```

```text
if value < -32768:
    value = -32768
```

The project will compare:

- wrap-around arithmetic;
- saturation arithmetic.

Saturation is expected to be the default fixed-point behavior.

## Overflow Detection

Overflow monitoring will record:

- multiplication overflow;
- accumulator overflow;
- state overflow;
- output saturation count;
- maximum internal magnitude.

A typical result record may include:

| Quantity | Planned result |
|---|---:|
| Input saturation count | To be measured |
| Coefficient saturation count | To be measured |
| Accumulator overflow count | To be measured |
| Output saturation count | To be measured |
| Maximum accumulator magnitude | To be measured |

## Rounding and Truncation

The project may compare:

- truncation;
- round to nearest;
- symmetric rounding;
- optional convergent rounding.

Rounding error is:

```text
rounding_error =
quantized_value - original_value
```

The effect on output SNR and DC bias will be evaluated.

## Fixed-Point IIR Stability

IIR filters are especially sensitive to coefficient quantization.

The floating-point poles are calculated from the denominator:

```text
1 + a1*z^(-1) + a2*z^(-2)
```

The quantized poles are calculated after replacing:

```text
a1 -> quantized a1
a2 -> quantized a2
```

The stability condition is:

```text
Magnitude of every quantized pole < 1
```

The project will not accept a fixed-point IIR configuration without checking quantized pole locations.

## Limit Cycles

A fixed-point IIR filter can produce nonzero output even after the input becomes zero.

This behavior is called a limit cycle.

A planned test is:

```text
1. Excite the IIR filter.
2. Set all future input samples to zero.
3. Observe the output.
4. Check whether the output decays to zero.
```

The test will compare:

- floating-point implementation;
- fixed-point implementation;
- truncation;
- rounding;
- different word lengths.

## Planned Features

### FIR Filter Design

- low-pass FIR;
- high-pass FIR;
- optional band-pass FIR;
- window-based coefficient generation;
- configurable filter order;
- configurable sampling frequency;
- configurable cutoff frequency;
- coefficient export.

### FIR Implementation

- direct-form FIR;
- circular-buffer FIR;
- floating-point C++;
- fixed-point C;
- fixed-point C++;
- optional block-processing interface.

### IIR Filter Design

- second-order biquad;
- Butterworth design;
- optional Chebyshev design;
- second-order-section conversion;
- pole-zero analysis;
- stability verification.

### IIR Implementation

- Direct Form I;
- Direct Form II Transposed;
- biquad cascade;
- floating-point C++;
- fixed-point C and C++;
- state reset;
- sample-by-sample processing.

### Fixed-Point Processing

- Q15 representation;
- optional Q31 representation;
- coefficient quantization;
- input scaling;
- output scaling;
- rounding;
- truncation;
- saturation;
- overflow counting;
- accumulator-width analysis;
- quantized-pole stability checking.

### Verification

- MATLAB reference comparison;
- Python reference comparison;
- impulse-response comparison;
- step-response comparison;
- frequency-response comparison;
- sample-by-sample output comparison;
- floating-point versus fixed-point comparison;
- overflow and saturation tests;
- stability tests;
- limit-cycle tests.

### Benchmarking

- runtime per sample;
- samples per second;
- memory use;
- number of multiplications;
- number of additions;
- buffer operations;
- direct-form versus circular-buffer FIR;
- Direct Form I versus Direct Form II Transposed;
- floating-point versus fixed-point runtime.

## Planned Software Architecture

```text
Filter Design
├── FIR coefficient design
├── IIR coefficient design
├── Biquad conversion
├── Pole-zero calculation
└── Coefficient export

Floating-Point Reference
├── MATLAB implementation
├── Python implementation
├── FIR processing
├── IIR processing
└── Frequency-response analysis

Fixed-Point Conversion
├── Q-format definition
├── Coefficient quantization
├── Input scaling
├── Accumulator sizing
├── Rounding
└── Saturation

C and C++ Implementation
├── FIR direct form
├── FIR circular buffer
├── IIR Direct Form I
├── IIR Direct Form II Transposed
├── Biquad cascade
└── Fixed-point arithmetic

Verification
├── Deterministic test vectors
├── MATLAB comparison
├── Python comparison
├── Frequency-response comparison
├── Overflow tests
├── Stability tests
└── Regression tests

Benchmarking
├── Runtime
├── Throughput
├── Memory use
├── Operation count
└── Numerical error
```

## Repository Structure

```text
02_fir_iir_fixed_point_filters/
├── README.md
├── matlab/
│   ├── design_fir_filters.m
│   ├── design_iir_filters.m
│   ├── fir_reference.m
│   ├── iir_biquad_reference.m
│   ├── fixed_point_reference.m
│   ├── analyze_filter_response.m
│   └── run_filter_analysis.m
├── python/
│   ├── design_fir_filters.py
│   ├── design_iir_filters.py
│   ├── fir_reference.py
│   ├── iir_biquad_reference.py
│   ├── fixed_point_reference.py
│   ├── analyze_filter_response.py
│   └── run_filter_analysis.py
├── c/
│   ├── include/
│   │   ├── fir_q15.h
│   │   ├── biquad_q15.h
│   │   └── fixed_point_math.h
│   ├── src/
│   │   ├── fir_q15.c
│   │   ├── biquad_q15.c
│   │   └── fixed_point_math.c
│   └── CMakeLists.txt
├── cpp/
│   ├── include/
│   │   ├── fir_filter.hpp
│   │   ├── biquad_filter.hpp
│   │   ├── fixed_point.hpp
│   │   └── filter_metrics.hpp
│   ├── src/
│   │   ├── fir_filter.cpp
│   │   ├── biquad_filter.cpp
│   │   ├── fixed_point.cpp
│   │   └── filter_metrics.cpp
│   ├── CMakeLists.txt
│   └── main.cpp
├── tests/
│   ├── deterministic_vectors/
│   ├── test_fir_impulse/
│   ├── test_fir_frequency_response/
│   ├── test_iir_impulse/
│   ├── test_iir_stability/
│   ├── test_fixed_point_accuracy/
│   ├── test_saturation/
│   ├── test_overflow/
│   └── test_limit_cycles/
├── benchmarks/
│   ├── runtime/
│   ├── memory/
│   ├── operation_count/
│   └── floating_vs_fixed/
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

- FIR coefficient design;
- IIR coefficient design;
- frequency-response analysis;
- pole-zero analysis;
- impulse and step responses;
- fixed-point reference calculations;
- deterministic test-vector generation;
- result plotting.

Possible MATLAB reference functions include:

```matlab
filter
freqz
impz
zplane
butter
cheby1
cheby2
ellip
fir1
tf2sos
```

Custom implementations will be compared with the corresponding MATLAB reference functions.

### Python Reference Model

Python will be used for:

- independent FIR and IIR implementation;
- SciPy filter-design comparison;
- coefficient analysis;
- fixed-point simulation;
- automated tests;
- CSV processing;
- plotting.

Expected packages:

```text
NumPy
SciPy
Matplotlib
pandas
pytest
```

Possible Python reference functions include:

```python
scipy.signal.lfilter
scipy.signal.sosfilt
scipy.signal.freqz
scipy.signal.sosfreqz
scipy.signal.firwin
scipy.signal.butter
```

### C Implementation

C will focus on:

- Q15 FIR filtering;
- Q15 biquad filtering;
- fixed-width integer arithmetic;
- saturation functions;
- overflow monitoring;
- embedded-oriented interfaces;
- deterministic memory use.

### C++ Implementation

C++ will focus on:

- reusable FIR and biquad classes;
- floating-point and fixed-point templates;
- circular buffers;
- safe configuration;
- coefficient loading;
- runtime benchmarking;
- unit-test integration.

## Planned C++ Interfaces

A possible floating-point FIR interface is:

```cpp
class FirFilter {
public:
    explicit FirFilter(
        const std::vector<double>& coefficients
    );

    double processSample(double input);

    void processBlock(
        const std::vector<double>& input,
        std::vector<double>& output
    );

    void reset();

private:
    std::vector<double> coefficients_;
    std::vector<double> delay_line_;
    std::size_t write_index_;
};
```

A possible biquad interface is:

```cpp
struct BiquadCoefficients {
    double b0;
    double b1;
    double b2;
    double a1;
    double a2;
};

class BiquadFilter {
public:
    explicit BiquadFilter(
        const BiquadCoefficients& coefficients
    );

    double processSample(double input);

    void reset();

private:
    BiquadCoefficients coefficients_;
    double state1_;
    double state2_;
};
```

A possible Q15 saturation function is:

```cpp
std::int16_t saturateQ15(std::int64_t value);
```

The final software interfaces may change during implementation.

## Planned Test Signals

The project will use deterministic signals such as:

- impulse;
- unit step;
- passband sinusoid;
- stopband sinusoid;
- two-tone input;
- white noise;
- chirp;
- full-scale input;
- near-overflow input;
- zero input after IIR excitation.

## Planned Verification Tests

### Test 1: FIR Impulse Response

Input:

```text
x[0] = 1
x[n] = 0 for n > 0
```

Expected result:

```text
The FIR output equals the coefficient sequence.
```

For an FIR filter with coefficients:

```text
b[0], b[1], ..., b[M]
```

the expected output is:

```text
y[0] = b[0]
y[1] = b[1]
...
y[M] = b[M]
```

### Test 2: FIR Step Response

Input:

```text
x[n] = 1
```

The final steady-state output of a low-pass FIR filter should approach:

```text
sum of all FIR coefficients
```

For a unity-DC-gain filter:

```text
sum of coefficients approximately equals 1
```

### Test 3: FIR Passband Tone

A sinusoid inside the passband should retain its amplitude within the allowed ripple.

### Test 4: FIR Stopband Tone

A sinusoid inside the stopband should be attenuated according to the design requirement.

### Test 5: Circular-Buffer Equivalence

The circular-buffer FIR output must match the direct-form FIR output sample by sample.

### Test 6: IIR Impulse Response

The custom IIR output will be compared with MATLAB and SciPy reference outputs.

### Test 7: IIR Step Response

The response will be checked for:

- expected steady-state gain;
- overshoot;
- settling behavior;
- numerical stability.

### Test 8: IIR Pole Stability

The floating-point and quantized poles must satisfy:

```text
abs(pole) < 1
```

for every stable configuration.

### Test 9: Fixed-Point Coefficient Accuracy

The quantized coefficient error will be recorded.

### Test 10: Fixed-Point Output Accuracy

The Q15 or Q31 output will be compared with the floating-point reference.

### Test 11: Saturation Test

An input large enough to exceed the output range should produce the maximum or minimum representable value without wrap-around.

### Test 12: Overflow Test

Internal accumulator ranges will be monitored under worst-case or near-worst-case input.

### Test 13: Limit-Cycle Test

After excitation, the IIR input will be set to zero. The fixed-point output will be observed for persistent oscillation.

### Test 14: Reset Test

After calling the reset function:

```text
all delay-line and state values must equal zero
```

### Test 15: Block Versus Sample Processing

Block-processing output must match repeated sample-by-sample processing.

### Test 16: Invalid Configuration

The implementation should reject:

- empty coefficient arrays;
- invalid denominator coefficients;
- unstable quantized IIR configurations;
- unsupported Q-format settings;
- null pointers where applicable.

## Verification Metrics

| Metric | Purpose |
|---|---|
| Maximum absolute error | Largest reference-to-implementation difference |
| RMS error | Average numerical difference |
| Relative error | Error normalized to reference output |
| Output SNR | Fixed-point signal quality |
| Coefficient error | Quantization effect on coefficients |
| Frequency-response error | Difference between reference and implementation |
| Passband ripple | Maximum passband variation |
| Stopband attenuation | Rejection inside the stopband |
| Pole-radius margin | Distance of poles from the unit circle |
| Saturation count | Number of clipped output samples |
| Overflow count | Number of internal overflow events |
| Runtime per sample | Processing speed |
| Throughput | Samples processed per second |
| Memory use | State, coefficient, and temporary storage |
| Unit-test status | PASS or FAIL |

## Maximum Absolute Error

```text
Maximum absolute error =
maximum of abs(y_implementation[n] - y_reference[n])
```

## RMS Error

```text
RMS error =
sqrt(
    sum(
        (y_implementation[n] - y_reference[n])^2
    )
    /
    number of samples
)
```

## Relative Error

```text
Relative error =
norm(y_implementation - y_reference)
/
norm(y_reference)
```

## Output SNR

For reference output `y_ref[n]` and error `e[n]`:

```text
e[n] = y_fixed[n] - y_ref[n]
```

Output SNR is:

```text
Output SNR in dB =
10 * log10(
    sum(y_ref[n]^2)
    /
    sum(e[n]^2)
)
```

## Passband Ripple

```text
Passband ripple =
maximum passband magnitude
-
minimum passband magnitude
```

This may be reported in linear units or decibels.

## Stopband Attenuation

```text
Stopband attenuation in dB =
-negative maximum stopband magnitude in dB
```

The exact definition and frequency region will be documented with each filter.

## Planned Numerical Tolerances

Initial floating-point comparisons may use:

```text
Maximum absolute error <= 1e-10
Relative error <= 1e-10
```

Initial Q15 comparisons may instead use:

```text
Error tolerance based on:
- one or more least-significant bits;
- coefficient quantization;
- accumulator scaling;
- rounding mode;
- expected output SNR.
```

Fixed-point tolerances will be defined before accepting a result.

## Planned Example Configurations

### Configuration 1: FIR Low-Pass Filter

```text
Sampling frequency: 48 kHz
Passband edge: 5 kHz
Stopband edge: 8 kHz
Filter type: FIR low-pass
Implementation: floating point and Q15
```

### Configuration 2: FIR High-Pass Filter

```text
Sampling frequency: 48 kHz
Stopband edge: 2 kHz
Passband edge: 4 kHz
Implementation: floating point and Q15
```

### Configuration 3: IIR Butterworth Low-Pass

```text
Sampling frequency: 48 kHz
Cutoff frequency: 5 kHz
Filter order: 4
Implementation: cascaded biquads
```

### Configuration 4: IIR Notch Filter

```text
Sampling frequency: configurable
Interference frequency: configurable
Implementation: second-order biquad
```

These are planned examples and may be adjusted during implementation.

## Planned Benchmark Scenarios

The benchmark may compare:

- FIR with 16 taps;
- FIR with 32 taps;
- FIR with 64 taps;
- FIR with 128 taps;
- one biquad;
- two cascaded biquads;
- four cascaded biquads;
- floating-point C++;
- Q15 C;
- Q15 C++;
- direct-form FIR;
- circular-buffer FIR;
- Direct Form I IIR;
- Direct Form II Transposed IIR.

## Planned Benchmark Table

| Configuration | Runtime per sample | Throughput | Memory | Maximum error | Saturation count |
|---|---:|---:|---:|---:|---:|
| 32-tap float FIR | Planned | Planned | Planned | Planned | Not applicable |
| 32-tap Q15 FIR | Planned | Planned | Planned | Planned | Planned |
| Float biquad | Planned | Planned | Planned | Planned | Not applicable |
| Q15 biquad | Planned | Planned | Planned | Planned | Planned |

No benchmark values are claimed until the implementations have been executed.

## Planned Result Figures

The completed project may include:

- FIR impulse response;
- FIR step response;
- FIR magnitude response;
- FIR phase response;
- FIR group delay;
- IIR impulse response;
- IIR step response;
- IIR magnitude response;
- IIR pole-zero plot;
- floating-point versus fixed-point output;
- coefficient-quantization error;
- Q15 error waveform;
- output SNR versus word length;
- passband ripple versus coefficient precision;
- stopband attenuation versus coefficient precision;
- pole movement after quantization;
- saturation-event plot;
- runtime versus filter length;
- direct-form versus circular-buffer runtime;
- Direct Form I versus Direct Form II Transposed runtime.

No final result figures are currently included.

## Current Development Checklist

- [x] Project topic selected
- [x] Project folder created
- [x] Initial project scope defined
- [x] GitHub-compatible README prepared
- [ ] FIR mathematical derivation completed
- [ ] IIR mathematical derivation completed
- [ ] MATLAB FIR design implemented
- [ ] MATLAB IIR design implemented
- [ ] MATLAB fixed-point reference implemented
- [ ] Python FIR model implemented
- [ ] Python IIR model implemented
- [ ] Python fixed-point model implemented
- [ ] C Q15 FIR implemented
- [ ] C Q15 biquad implemented
- [ ] C++ floating-point FIR implemented
- [ ] C++ circular-buffer FIR implemented
- [ ] C++ biquad implemented
- [ ] C++ fixed-point implementation completed
- [ ] Coefficient quantization implemented
- [ ] Saturation arithmetic implemented
- [ ] Overflow monitoring implemented
- [ ] FIR impulse test completed
- [ ] FIR frequency-response test completed
- [ ] IIR impulse test completed
- [ ] Quantized-pole stability test completed
- [ ] Fixed-point accuracy test completed
- [ ] Limit-cycle test completed
- [ ] MATLAB cross-verification completed
- [ ] Python cross-verification completed
- [ ] Runtime benchmarks completed
- [ ] Memory analysis completed
- [ ] Result figures generated
- [ ] Final technical documentation completed

This checklist will be updated as development progresses.

## Build and Run

The project is not yet ready for complete execution.

### Planned MATLAB Command

```matlab
run("matlab/run_filter_analysis.m")
```

### Planned Python Command

```bash
python python/run_filter_analysis.py
```

### Planned C Build

```bash
cmake -S c -B c/build
cmake --build c/build
```

### Planned C++ Build

```bash
cmake -S cpp -B cpp/build
cmake --build cpp/build
```

### Planned C++ Execution

Linux or macOS:

```bash
./cpp/build/filter_analysis
```

Windows:

```text
cpp\build\Release\filter_analysis.exe
```

The final commands will be updated to match the actual source files and generated executables.

## Expected Engineering Outcomes

After completion, this project is expected to demonstrate:

- FIR filter design;
- IIR filter design;
- filter difference-equation implementation;
- direct-form FIR processing;
- circular-buffer processing;
- biquad implementation;
- Direct Form I;
- Direct Form II Transposed;
- floating-point DSP;
- Q15 and optional Q31 arithmetic;
- coefficient quantization;
- saturation and overflow handling;
- accumulator-width analysis;
- IIR stability checking;
- limit-cycle analysis;
- MATLAB modeling;
- Python verification;
- C implementation;
- C++ implementation;
- deterministic testing;
- runtime and memory benchmarking;
- technical documentation.

## Initial Limitations

The first implementation is expected to have the following limitations:

- single-channel processing;
- real-valued signals;
- single-threaded execution;
- Q15 as the primary fixed-point format;
- no SIMD optimization;
- no processor-specific DSP instructions;
- no hardware-in-the-loop validation;
- no real-time operating-system integration;
- no automatic code generation;
- limited initial filter families.

These limitations will be documented clearly.

## Future Extensions

Possible future extensions include:

- complex-valued filtering;
- Q31 implementation;
- configurable fixed-point templates;
- block floating-point processing;
- SIMD optimization;
- ARM CMSIS-DSP comparison;
- Intel IPP comparison;
- hardware-specific DSP intrinsics;
- multichannel filtering;
- real-time audio input;
- streaming IQ filtering;
- filter coefficient reload during processing;
- automatic gain scaling;
- noise-shaping quantization;
- lattice IIR structures;
- hardware-in-the-loop validation;
- FPGA-oriented fixed-point design;
- Simulink fixed-point model;
- continuous-integration testing.

## Skills Demonstrated

After completion, this project is expected to demonstrate:

- DSP algorithm derivation;
- digital-filter design;
- FIR implementation;
- IIR implementation;
- fixed-point arithmetic;
- numerical stability analysis;
- coefficient quantization;
- saturation arithmetic;
- overflow management;
- embedded C;
- C++;
- MATLAB;
- Python;
- CMake;
- unit testing;
- regression testing;
- numerical cross-verification;
- runtime profiling;
- memory-aware design;
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
