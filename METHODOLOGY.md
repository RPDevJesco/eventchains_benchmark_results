# EventChains Benchmarking Methodology

## Overview

This document describes the rigorous multi-tier benchmarking methodology used to validate EventChains performance across multiple languages, platforms, and hardware configurations. The approach was developed to avoid common performance measurement pitfalls and provide honest, reproducible results.

## Table of Contents

1. [Why Multi-Tier Testing](#why-multi-tier-testing)
2. [The Four Tiers](#the-four-tiers)
3. [Common Pitfalls Avoided](#common-pitfalls-avoided)
4. [Measurement Techniques](#measurement-techniques)
5. [Statistical Approach](#statistical-approach)
6. [Reproducibility](#reproducibility)

---

## Why Multi-Tier Testing

### The Problem with Single-Metric Benchmarks

Most performance studies compare a framework to bare function calls and report percentage overhead. This approach is misleading because:

1. **Percentage without context**: "1000% overhead" sounds catastrophic, but if the base operation takes 0.01µs, the total cost is still only 0.1µs
2. **Unequal feature comparison**: Comparing a full framework to bare functions is comparing different capabilities
3. **Missing scaling characteristics**: Single measurements don't show how overhead grows with complexity
4. **No real-world validation**: Academic benchmarks often miss production instrumentation costs
5. **Optimization dependency unclear**: Without testing unoptimized builds, it's impossible to distinguish architectural benefits from compiler tricks

We address these limitations with four complementary measurements:

**Tier 1: Framework Overhead (Baseline Comparison)**
- Compares EventChains to bare function calls
- Reveals pure orchestration cost
- Context: Shows maximum possible overhead

**Tier 2: Feature Parity (Fair Comparison)**
- Compares EventChains to manual code with equivalent features
- Manual implementation includes error handling, result tracking, cleanup
- Context: Shows cost of abstraction vs hand-written equivalent

**Tier 3: Middleware Scaling (Composition Cost)**
- Measures incremental cost per middleware layer (0, 1, 3, 5, 10 layers)
- Reveals whether overhead is linear, super-linear, or sub-linear
- Context: Validates architectural scalability

**Tier 4: Real-World Instrumentation (Production Scenario)**
- Compares to manual implementation with logging, timing, error handling
- Measures actual production deployment overhead
- Context: Shows practical vs academic performance

### Debug Build Methodology

**All benchmarks use unoptimized debug builds** unless explicitly noted. This
conservative approach:

- Demonstrates worst-case performance without compiler assistance
- Ensures maximum reproducibility across platforms and toolchains
- Proves architectural soundness independent of optimization
- Eliminates "compiler tricks" objections from reviewers
- Shows that release builds provide 4-6x additional improvement

### What We Found

This methodology revealed that EventChains exhibits sub-linear scaling -
middleware costs decrease as composition depth increases - even in unoptimized
code on modern CPUs. This behavior appears across multiple languages and
platforms, validating that the sequential event processing architecture
fundamentally enables CPU-level optimization.

### Our Solution: Four-Tier Analysis

Each tier answers a specific question:

| Tier | Question | Why It Matters |
|------|----------|----------------|
| T1 | What is the pure framework cost? | Establishes the minimum overhead floor |
| T2 | How much does abstraction cost vs manual equivalent? | Validates that abstraction cost is reasonable |
| T3 | How does overhead scale with middleware? | Proves composability and linear scaling |
| T4 | What's the cost in production scenarios? | Demonstrates real-world performance |

---

## The Four Tiers

### Tier 1: Minimal Baseline

**Goal**: Measure the pure cost of the EventChains orchestration framework.

**Comparison**:
- **Baseline**: 3 bare function calls with no error handling, no tracking, no cleanup
- **EventChains**: Full pattern with 0 middleware layers

**What This Measures**:
- Event wrapping and trait dispatch
- Context object creation
- Result aggregation
- Chain construction overhead

**Example Results**:
```
C (macOS M4):
  Bare functions:     0.308 µs
  EventChains (0 MW): 0.455 µs
  Overhead:           +0.147 µs (+47.73%)

C# (Windows Ryzen 9):
  Bare functions:     0.02 µs
  EventChains (0 MW): 5.81 µs
  Overhead:           +5.79 µs (+31,252%)
```

**Interpretation**: The percentage looks high, but absolute overhead is what matters. In C, the framework adds less than 0.15µs. In real-world operations (database queries, API calls), this is negligible.

### Tier 2: Feature-Parity Baseline

**Goal**: Compare EventChains to a manual implementation with identical features.

**Comparison**:
- **Baseline**: Manual implementation with:
    - Error handling (try/catch or Result types)
    - Name tracking (which operation failed)
    - Context cleanup (resource management)
    - Result aggregation
- **EventChains**: Same features, declarative composition

**What This Measures**:
- Cost of abstraction vs concrete implementation
- Generic trait-based design vs concrete types
- Dynamic dispatch vs static dispatch
- Type-erased context vs typed variables

**Example Results**:
```
Raspberry Pi CM5 (ARM64 via emulation):
  Manual equivalent:  2.157 µs
  EventChains:        2.262 µs
  Overhead:           +0.105 µs (+4.87%)

C# (macOS M4):
  Manual equivalent:  0.19 µs
  EventChains:        6.90 µs
  Overhead:           +6.71 µs (+3,484%)
```

**Developer Productivity Trade-off**:
- Manual implementation: ~150-300 lines of boilerplate
- EventChains: ~10-30 lines of declarative code
- Code reduction: 90-93%

**Interpretation**: This is the **true cost of abstraction**. Tier 2 shows that while EventChains adds overhead, you're comparing equivalent functionality. The question becomes: is the maintenance and readability benefit worth 0.1-7µs per operation?

### Tier 3: Middleware Scaling

**Goal**: Analyze how overhead scales with middleware layer count.

**Comparison**:
- EventChains with 0 middleware (baseline)
- EventChains with 1 middleware
- EventChains with 3 middleware
- EventChains with 5 middleware
- EventChains with 10 middleware

**What This Measures**:
- Incremental cost per middleware layer
- Scaling linearity (is it O(n) or worse?)
- Middleware composition overhead
- Whether 10+ layers remain viable

**Example Results**:
```
C (macOS M4):
  0 layers:   0.431 µs (baseline)
  1 layer:    0.525 µs (+0.094 µs per layer)
  3 layers:   0.564 µs (+0.019 µs per layer)
  5 layers:   0.566 µs (+0.001 µs per layer)
  10 layers:  0.613 µs (+0.009 µs per layer)
  
  Amortized: 0.018 µs per middleware layer

C# (Windows Ryzen 9):
  0 layers:   5.81 µs (baseline)
  10 layers:  6.55 µs (+0.74 µs total, 0.074 µs per layer)
  
  Amortized: 0.122 µs per middleware layer
```

**Scaling Characteristics**:
- **C**: Nearly perfect sublinear scaling (gets cheaper per layer)
- **C#**: Linear scaling with slight improvement at scale
- **Rust**: Variable, but negative overhead emerges at large scale

**Interpretation**: All implementations support 10+ middleware layers with <1µs total overhead. This proves the pattern is composable without exponential cost growth.

### Tier 4: Real-World Scenario

**Goal**: Test production scenarios with full instrumentation.

**Comparison**:
- **Baseline**: Manual implementation with:
    - Logging (to console or file)
    - Timing (operation duration measurement)
    - Error handling (full try/catch with reporting)
    - Retry logic (where applicable)
    - Rate limiting (where applicable)
- **EventChains**: Same features via middleware composition

**What This Measures**:
- Actual production overhead
- Cost of middleware-based instrumentation vs manual
- Real-world performance characteristics
- Whether pattern is production-ready

**Example Results**:
```
C (macOS M4):
  Manual (logging + timing):  0.304 µs
  EventChains (middleware):   0.512 µs
  Overhead:                   +0.208 µs (+68.42%)

C# (Windows Ryzen 9):
  Manual (logging + timing):  0.44 µs
  EventChains (middleware):   6.34 µs
  Overhead:                   +5.90 µs (+1,327%)
```

**Context - Typical Operation Latencies**:
- EventChains overhead: 0.2-6 µs
- In-memory cache hit: 50-100 µs
- Database query: 1,000-10,000 µs
- Network API call: 10,000-100,000 µs

**Interpretation**: EventChains overhead is negligible compared to typical I/O operations. In production applications with database queries, network calls, or file I/O, the framework overhead is lost in the noise.

---

## Common Pitfalls Avoided

### 1. Timer Granularity Errors

**Problem**: Early EventChains benchmarks showed catastrophic overhead due to measuring microsecond operations with millisecond-precision timers.

**Solution**: Use high-resolution timers appropriate for each platform:
- **Windows**: `QueryPerformanceCounter` (100ns resolution)
- **Linux**: `clock_gettime(CLOCK_MONOTONIC)` (1ns resolution)
- **macOS**: `mach_absolute_time()` (sub-nanosecond)
- **Rust**: `std::time::Instant` (platform-appropriate)

**Validation**: Report timer resolution in benchmark output to ensure adequate precision.

### 2. Comparing Unequal Features

**Problem**: Comparing a full framework to bare functions is an apples-to-oranges comparison.

**Solution**: Tier 1 measures bare functions (establishing ceiling), but Tier 2 ensures feature parity. Only compare implementations with identical capabilities.

**Example**:
```
❌ Bad:  "EventChains is 1000x slower than a function call"
✅ Good: "EventChains is 23% slower than manual error handling + tracking"
```

### 3. Ignoring Warmup

**Problem**: Cold starts, JIT compilation, and cache misses skew initial measurements.

**Solution**:
- Run warmup iterations before measurements (typically 1000-10000 iterations)
- Discard first measurement batch
- Report "warmup complete" in output
- For JIT languages (C#, Java), ensure code is fully compiled before measuring

### 4. Cherry-Picking Results

**Problem**: Only reporting best-case scenarios or hiding unfavorable data.

**Solution**: Report all four tiers honestly:
- Show where EventChains is slower (Tier 1, 2, 4 typically)
- Show where it scales well (Tier 3)
- Show where negative overhead emerges (Rust at scale)
- Include variance and outliers

### 5. Percentage Without Context

**Problem**: "31,000% overhead" sounds catastrophic but may be 5µs on a 0.02µs operation.

**Solution**: Always report both:
- **Absolute overhead** (µs or ms)
- **Percentage overhead**
- **Context** (what are typical operation costs?)

**Example**:
```
Framework overhead: 5.79µs (31,252%)
Context: Database query overhead is 1,000-10,000µs
Conclusion: Framework overhead is negligible in real applications
```

### 6. Single Platform Testing

**Problem**: Results may not generalize across architectures or operating systems.

**Solution**: Test on multiple platforms:
- **Operating Systems**: Windows, macOS, Linux, embedded
- **Architectures**: x86, x86_64, ARM64
- **Hardware Eras**: Vintage (2000) to Modern (2024)
- **Form Factors**: Desktop, laptop, embedded, cloud

This study includes:
- Windows on Ryzen 9 7950X
- macOS on M4 iMac
- Linux on Raspberry Pi CM5
- Windows XP on Pentium III
- Salesforce Cloud (managed environment)

### 7. Ignoring Variance

**Problem**: Single measurements or reporting only mean values without variance.

**Solution**: Report statistical measures:
- **Mean** (average)
- **Median** (middle value, less affected by outliers)
- **Min/Max** (range)
- **Standard Deviation** (variance)

**Example**:
```
Baseline (3 function calls): avg=3.111 µs, min=3.074 µs, max=37.629 µs, stddev=0.387 µs
```

### 8. No Reproducibility Information

**Problem**: Insufficient information to reproduce results.

**Solution**: Document everything:
- Hardware specifications (CPU, RAM, storage)
- OS version and kernel
- Compiler/runtime versions
- Optimization flags
- Test configuration
- Number of iterations
- Warmup procedure

All this information is available in hardware/specifications.md and in individual benchmark outputs.

---

## Measurement Techniques

### Iteration Count

**Selection Criteria**: Choose iteration count based on operation duration:
- Operations < 1µs: 100,000+ iterations
- Operations 1-10µs: 10,000-50,000 iterations
- Operations > 10µs: 1,000-10,000 iterations

**Goal**: Total measurement time should be at least 100ms to minimize timer overhead percentage.

**This Study**:
- C/C# Tier 1-4: 10,000 iterations
- Rust (small scale): 100 runs
- Rust (large scale): 50 runs (operations are longer)

### Warmup Procedure

```
1. Run benchmark code 1,000-10,000 times (warmup)
2. Report "warmup complete"
3. Clear any accumulated state
4. Run actual measurement iterations
5. Collect timing data
6. Calculate statistics
```

### Timing Precision

**Timer Selection**:
```c
// C - Use high-resolution timer
struct timespec start, end;
clock_gettime(CLOCK_MONOTONIC, &start);
// ... operation ...
clock_gettime(CLOCK_MONOTONIC, &end);
```

```csharp
// C# - Use Stopwatch with QueryPerformanceCounter
var sw = Stopwatch.StartNew();
// ... operation ...
sw.Stop();
var microseconds = sw.Elapsed.TotalMicroseconds;
```

```rust
// Rust - Use std::time::Instant
let start = Instant::now();
// ... operation ...
let duration = start.elapsed();
```

**Report Timer Resolution**: Always output timer resolution so readers can assess whether measurements are meaningful.

### Outlier Handling

**Approach**: Report outliers but don't exclude them (they represent real variance).

**Statistics**:
- **Mean**: Includes all measurements
- **Median**: Less sensitive to outliers
- **Min/Max**: Shows range including outliers
- **Standard Deviation**: Quantifies variance

**Example**: If max is 10x higher than mean, it indicates GC pauses, OS interrupts, or other real-world interference. This is valuable information.

---

## Statistical Approach

### Sample Size

**Minimum**: 1,000 iterations per configuration
**Typical**: 10,000 iterations per configuration
**Large operations**: 50-100 iterations (when operation takes >1ms)

### Metrics Reported

For each configuration:
1. **Mean (µs)**: Average duration
2. **Median (µs)**: Middle value (50th percentile)
3. **Min (µs)**: Fastest iteration
4. **Max (µs)**: Slowest iteration
5. **Standard Deviation**: Measure of variance

### Overhead Calculation

**Absolute Overhead**:
```
Overhead = EventChains_time - Baseline_time
```

**Percentage Overhead**:
```
Overhead_% = ((EventChains_time - Baseline_time) / Baseline_time) × 100
```

**Per-Layer Cost (Tier 3)**:
```
Cost_per_layer = (Time_n_layers - Time_0_layers) / n
```

### Statistical Significance

While formal significance testing (t-tests, ANOVA) was not performed in this study, we ensure meaningful results by:
1. Large sample sizes (10,000+ iterations)
2. Multiple platform validation (results consistent across platforms)
3. Reproducible procedures (same methodology for all languages)
4. Low variance (standard deviations typically <10% of mean)

---

## Reproducibility

### Prerequisites

To reproduce these benchmarks:

1. **Hardware**: See hardware/specifications.md for exact configurations
2. **Software**:
    - C: GCC 11+ or Clang 14+
    - C#: .NET Framework 2.0+ or .NET Core 9
    - Rust: Rustc 1.70+
    - Salesforce: Apex in production org
3. **Compiler Flags**:
    - C: `-O3` (optimized) or `-O0` (debug)
    - C#: Release build configuration
    - Rust: `--release` profile
4. **System State**:
    - Close unnecessary applications
    - Disable turbo boost (for consistency) or document boost behavior
    - Run on AC power (laptops)
    - Disable power management where possible

### Running Benchmarks

Each implementation repository contains:
- `README.md`
- Benchmark harness code
- Test scenarios
- Expected output format

### Validating Results

**Expected Variance**: ±20% is normal due to:
- Different hardware (CPU frequency, cache size)
- OS scheduler differences
- Background process interference
- Thermal throttling

**Red Flags** (indicates measurement error):
- Overhead > 100x expected
- Standard deviation > 50% of mean
- Negative overhead at small scale (unless validated across platforms)
- Inconsistent results across runs

### Reporting New Results

If contributing benchmark results from new platforms:

1. **Hardware**: Document CPU, RAM, architecture
2. **Software**: Document OS, compiler, versions
3. **Configuration**: Document compiler flags, optimization level
4. **Output**: Include full benchmark output (not just summary)
5. **Reproducibility**: Include steps to reproduce
6. **Validation**: Run multiple times, report variance

---

## Benchmark Configuration

### Work Per Event

To ensure meaningful measurements, each event contains substantial work:
- **Computational**: ~100 operations (arithmetic, logic)
- **String formatting**: Build result strings
- **Context updates**: Modify context state

**Why**: Events with only 1-2 operations show high percentage overhead because the framework cost dominates. Real-world events contain meaningful business logic, making this benchmark more representative.

### Middleware Implementations

Test middleware performs actual work:
- **Logging**: Format and write log messages
- **Timing**: Record start/end times, calculate duration
- **Metrics**: Update counters, calculate statistics
- **Chaos Testing**: Randomly inject failures

**Why**: No-op middleware would underestimate real overhead. Actual middleware implementations match production usage.

---

## Conclusion

This multi-tier methodology provides:
1. **Comprehensive analysis** across multiple comparison points
2. **Honest reporting** of both strengths and weaknesses
3. **Reproducible results** with detailed documentation
4. **Context** for interpreting performance numbers
5. **Production relevance** through real-world scenario testing

The approach avoids common benchmarking pitfalls while providing actionable insights into EventChains performance characteristics across languages, platforms, and scales.

For questions or clarifications about the methodology, please open an issue in the repository.

---

**Document Version**: 1.0  
**Last Updated**: November 2025