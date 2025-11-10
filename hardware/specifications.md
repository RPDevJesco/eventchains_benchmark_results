# Hardware Specifications

Complete specifications for all systems used in EventChains performance validation. This document enables reproduction of benchmarks and provides context for performance comparisons.

## Table of Contents

1. [Modern Desktop Systems](#modern-desktop-systems)
2. [Modern Laptop Systems](#modern-laptop-systems)
3. [Embedded Systems](#embedded-systems)
4. [Vintage Hardware](#vintage-hardware)
5. [Cloud Platforms](#cloud-platforms)
6. [Architecture Comparison](#architecture-comparison)

---

## Modern Desktop Systems

### Windows Desktop - High-End Workstation

**System Identifier**: `windows_ryzen9_7950x`

**Hardware**:
- **CPU**: AMD Ryzen 9 7950X
    - Architecture: x86_64 (Zen 4)
    - Cores: 16 physical / 32 logical
    - Base Clock: 4.5 GHz
    - Boost Clock: Up to 5.7 GHz
    - Cache: 64MB L3, 16MB L2
    - TDP: 170W
    - Process: TSMC 5nm
- **RAM**: 128GB DDR5
    - Speed: DDR5-5200
    - Configuration: Dual channel.
- **GPU**: RTX 5070TI
- **Storage**: NVMe SSD
- **Motherboard**: AM5 platform

**Software**:
- **OS**: Windows 11 Pro
    - Version: 22H2 or later
    - Build: 22621+
- **C# Runtime**: .NET Framework 2.0.50727.9179 + .NET Core 8+
- **C Compiler**:
    - GCC (MinGW-w64) or
    - Clang/LLVM or
    - MSVC 2019+
- **Rust**: rustc 1.70+

**Timer Resolution**:
- Performance Counter Frequency: 10,000,000 Hz
- Timer Resolution: 0.100 µs

**Benchmark Results Available**:
- ✅ C# Multi-Tier (Tier 1-4)
- ✅ C Multi-Tier (Tier 1-4)
- ✅ Rust Multi-Tier

**Notes**:
- Background applications minimized
- Power plan: High performance
- Represents high-end desktop performance (2024)

---

## Modern Laptop Systems

### macOS Laptop - Apple Silicon

**System Identifier**: `macos_m4_imac`

**Hardware**:
- **CPU**: Apple M4
    - Architecture: ARM64 (Apple Silicon)
    - Cores: 8-10 (performance + efficiency cores)
    - Base Clock: ~3.5 GHz (performance cores)
    - Cache: Unified memory architecture
    - TDP: ~40W (thermal design)
    - Process: TSMC 3nm
- **RAM**: 16GB Unified Memory
    - Type: LPDDR5X
    - Bandwidth: ~400 GB/s
- **Storage**: SSD (NVMe, integrated)
- **GPU**: Integrated (10-core)

**Software**:
- **OS**: macOS Tacoma
- **C# Runtime**: .NET 8+
- **C Compiler**: Clang 15+ (Xcode Command Line Tools)
- **Rust**: rustc 1.70+

**Timer Resolution**:
- Uses `mach_absolute_time()`
- Resolution: Sub-nanosecond (hardware counter)

**Benchmark Results Available**:
- ✅ C# Multi-Tier (Tier 1-4)
- ✅ C Multi-Tier (Tier 1-4)
- ✅ Rust Multi-Tier (100 nodes, 500 nodes)

**Notes**:
- ARM64 native execution
- Unified memory architecture (CPU/GPU share RAM)
- Excellent power efficiency
- No active cooling in iMac (fanless design)
- Represents modern ARM laptop performance (2024)

---

## Embedded Systems

### Raspberry Pi CM5 - ARM Embedded

**System Identifier**: `raspberrypi_cm5_arm64`

**Hardware**:
- **CPU**: ARM Cortex-A76
    - Architecture: ARM64 (ARMv8.2-A)
    - Cores: 4
    - Clock: 2.4 GHz (typical)
    - Cache: 512KB L2 per core, 2MB L3 shared
    - TDP: ~5-8W
    - Process: TSMC 16nm
- **RAM**: 4GB or 8GB LPDDR4X
    - Speed: LPDDR4X-3200
    - Configuration: Single channel
- **Storage**: microSD or eMMC
- **Form Factor**: Compute Module 5

**Software**:
- **OS**: Raspberry Pi OS (Debian-based)
    - Version: Bookworm (Debian 12)
    - Kernel: Linux 6.1+
- **Emulation**: Box64 v0.3.9
    - Purpose: x86_64 → ARM64 translation
    - Dynarec: JIT recompilation enabled
    - Hardware counter: 54.0 MHz (emulating 3.4 GHz)
- **C Compiler**: GCC 12+ (ARM64 native)

**Timer Resolution**:
- Uses `clock_gettime(CLOCK_MONOTONIC)`
- Resolution: Nanosecond (kernel-limited)

**Benchmark Results Available**:
- ✅ C Multi-Tier via Box64 (x86_64 binary emulated on ARM64)

**Notes**:
- **Critical**: Benchmarks run x86_64 binary via Box64 emulation
- Native ARM64 compilation would show better performance
- Budget hardware (~$50-80)
- Represents embedded/IoT platform
- Demonstrates cross-architecture compatibility
- Excellent power efficiency (5-8W total system power)

**Box64 Configuration**:
```
Box64 arm64 v0.3.9 with Dynarec
Extensions: ASIMD AES CRC32 PMULL ATOMICS SHA1 SHA2
Pagesize: 16384
Address space: 48+ bits
```

**Optimized Binary Note**:
- Standard build: ✅ Works perfectly
- AVX-512 optimized build: ❌ SIGILL (Box64 cannot emulate AVX-512)

---

## Vintage Hardware

### Compaq Armada E500 - Vintage Laptop (2000)

**System Identifier**: `compaq_armada_e500_pentium3`

**Hardware**:
- **CPU**: Intel Pentium III Mobile
    - Architecture: x86 (P6 microarchitecture)
    - Cores: 1 (single core, no SMT)
    - Clock: 500-700 MHz (varies by model)
    - Cache: 256KB L2
    - TDP: ~15-20W
    - Process: 250nm or 180nm
    - Year: 1999-2000
- **RAM**: 256MB SDRAM
    - Speed: PC100 (100 MHz) or PC133
    - Configuration: Single channel
- **Storage**: IDE Hard Drive
    - Capacity: 10-20GB typical
    - Interface: IDE/PATA
    - Speed: 4200-5400 RPM
- **Chipset**: Intel 440BX or similar

**Software**:
- **OS**: Windows XP Professional SP3
    - Version: 5.1.2600
    - Last updated: 2008
    - 32-bit only
- **C# Runtime**: .NET Framework 2.0.50727.42
- **C Compiler**: Not tested (focus on C# validation)

**Timer Resolution**:
- Performance Counter Frequency: 3,579,545 Hz
- Timer Resolution: 0.279 µs

**Benchmark Results Available**:
- ✅ C# Advanced Performance Benchmark (8 tiers)

**Notes**:
- 24 years older than modern systems (2000 vs 2024)
- Represents late 1990s/early 2000s laptop performance
- Single-core, no hyper-threading
- Demonstrates EventChains viability on resource-constrained hardware
- Shows consistent performance scaling over 24-year hardware evolution

**Historical Context**:
- Released: 1999-2000
- Target market: Business laptops
- Contemporary OS: Windows 98, Windows 2000, Windows XP
- Era: Before multi-core CPUs, before 64-bit consumer systems

---

## Cloud Platforms

### Salesforce Cloud - Managed Multi-Tenant Platform

**System Identifier**: `salesforce_cloud_managed`

**Hardware**: Unknown (Managed by Salesforce)
- **CPU**: Not disclosed (likely Intel Xeon or similar)
- **Architecture**: Assumed x86_64
- **Virtualization**: Multi-tenant cloud environment

**Software**:
- **Platform**: Salesforce Lightning Platform
- **Language**: Apex (Java-like, JVM-based)
- **Runtime**: Managed Apex runtime
- **Governor Limits**: Strict resource enforcement

**Resource Limits (Governor Limits)**:
- **CPU Time**: 10,000 ms per transaction
- **Heap Size**: 6 MB per transaction
- **SOQL Queries**: 100 per transaction
- **DML Statements**: 150 per transaction
- **DML Rows**: 10,000 per transaction
- **Callouts**: 100 per transaction

**Timer Resolution**:
- Platform-managed (not directly accessible)
- Event timing in milliseconds

**Benchmark Results Available**:
- ✅ Apex Test Results (Governor limit compliance, event timing)

**Notes**:
- **Unique Constraints**: Unlike other platforms, Salesforce enforces hard resource limits
- **Multi-Tenant**: Shared infrastructure across all customers
- **Managed Runtime**: No direct hardware access
- **Performance Characteristics**: Different from traditional environments
- Represents cloud platform constraints

**Test Methodology**:
- Focus on governor limit compliance (not raw speed)
- Measure CPU usage percentage
- Validate event execution within time limits
- Ensure memory efficiency

---

## Architecture Comparison

### Summary Table

| System | CPU | Architecture | Cores | Clock (GHz) | RAM | Year | Era |
|--------|-----|--------------|-------|-------------|-----|------|-----|
| Windows Desktop | Ryzen 9 7950X | x86_64 (Zen 4) | 32 logical | 4.5-5.7 | 128GB | 2024 | Modern |
| macOS Laptop | Apple M4 | ARM64 (Apple) | 8-10 | ~3.5 | 16GB | 2024 | Modern |
| Raspberry Pi | Cortex-A76 | ARM64 (ARMv8) | 4 | 2.4 | 4-8GB | 2024 | Embedded |
| Vintage Laptop | Pentium III | x86 (P6) | 1 | 0.5-0.7 | 384MB | 2000 | Legacy |
| Salesforce | Unknown | x86_64 (assumed) | N/A | N/A | Managed | 2024 | Cloud |

### Performance Scaling (Relative to Vintage)

Comparing Ryzen 9 7950X (2024) to Pentium III (2000):

| Metric | Modern | Vintage | Ratio |
|--------|--------|---------|-------|
| CPU Time/Chain | 0.547 µs | 10.265 µs | 18.8x faster |
| CPU Time/Event | 0.182 µs | 3.422 µs | 18.8x faster |
| Ops per Second | 12.5M | 290K | 43.1x faster |
| Branch Prediction | 64% penalty | 90% penalty | 1.4x better |
| Cache Miss Penalty | 8.59% | 8.23% | ~Equal |

**Key Finding**: EventChains maintains consistent ~19x performance scaling over 24 years, tracking hardware improvements linearly.

### Architecture-Specific Notes

**x86_64 (Ryzen 9)**:
- Excellent branch prediction
- Large L3 cache (64MB) reduces memory latency
- High clock speeds (5.7 GHz boost)
- Best single-threaded performance

**ARM64 (Apple M4)**:
- Unified memory architecture
- Excellent power efficiency
- Lower clock speeds but efficient IPC
- Competitive performance with x86_64

**ARM64 (Raspberry Pi)**:
- Budget hardware, excellent value
- Lower power consumption (5-8W)
- Via emulation: proves cross-architecture compatibility
- Native performance would be even better

**x86 (Pentium III)**:
- Single-core, no SMT
- Limited cache (256KB L2)
- Shows pattern viability on resource-constrained hardware
- Validates 24-year backward compatibility

---

## Thermal and Power Considerations

### Desktop Systems

**Ryzen 9 7950X**:
- TDP: 170W
- Cooling: High-end air or liquid cooling required
- Thermal throttling: Unlikely during benchmarks (good cooling)
- Power plan: High performance mode used

### Laptop Systems

**Apple M4 iMac**:
- TDP: ~40W (estimated)
- Cooling: Passive (fanless) or minimal fan
- Thermal throttling: Possible during sustained load
- Power: AC-powered during testing

### Embedded Systems

**Raspberry Pi CM5**:
- TDP: 5-8W
- Cooling: Passive heatsink or small fan
- Thermal throttling: Possible but minimal impact
- Power: 5V DC, very efficient

### Vintage Systems

**Pentium III**:
- TDP: 15-20W
- Cooling: Small fan (noisy by modern standards)
- Thermal throttling: Not applicable (no throttling mechanism)
- Power: Battery or AC

---

## Compiler and Build Configurations

### C Benchmarks

**GCC**:
```bash
gcc -O3 -march=native -std=c11 -o benchmark benchmark.c
```

**Clang**:
```bash
clang -O3 -march=native -std=c11 -o benchmark benchmark.c
```

**Optimizations**:
- `-O3`: Maximum optimization
- `-march=native`: Use CPU-specific instructions
- Standard: C11

### C# Benchmarks

**Configuration**: Release
- Optimization: Enabled
- Debug info: Disabled
- Target: .NET Framework 2.0+ or .NET Core/5+

**Build Command**:
```bash
csc /optimize+ /target:exe Program.cs
```

or

```bash
dotnet build -c Release
```

### Rust Benchmarks

**Profile**: Release
```toml
[profile.release]
opt-level = 3
lto = true
codegen-units = 1
```

**Build Command**:
```bash
cargo build --release
```

---

## System State During Testing

### Consistency Measures

1. **Background Applications**: Minimized (only OS essentials)
2. **Network**: Active but idle (no downloads/uploads)
3. **Power Management**:
    - Desktop: High performance mode
    - Laptop: AC-powered, high performance
4. **Thermal State**: Systems allowed to reach steady-state temperature
5. **Other Processes**: Antivirus paused or excluded benchmark directory

### Reproducibility Notes

**Expected Variance**: ±10-20% due to:
- OS scheduler decisions
- Background interrupts
- Thermal state differences
- Memory layout randomization (ASLR)
- CPU frequency scaling (turbo boost)

**Acceptable Variance**: Results within 20% of reported values are considered consistent.

**Red Flags**:
- Results >2x different suggest measurement error
- Inconsistent ordering across tiers suggests system issues
- Very high standard deviations (>50% of mean) suggest interference

---

## Additional Test Configurations

### Future Test Platforms

Potential platforms for future validation:
- **RISC-V**: Emerging open-source architecture
- **IBM Power**: Enterprise RISC architecture
- **ARM Cortex-A53/A55**: Lower-end ARM cores
- **Intel Xeon**: Enterprise server processors
- **AMD EPYC**: Enterprise server processors
- **Apple M1/M2**: Earlier Apple Silicon generations

### Community Contributions

Benchmark results from community members on other platforms are welcome. Please include:
1. Complete hardware specifications (this template)
2. Software versions and build configuration
3. Full benchmark output (not just summary)
4. System state information
5. Multiple runs showing consistency

---

## Document Information

**Version**: 1.0  
**Last Updated**: November 2024

For questions about hardware configurations or to contribute new platform results, please open an issue in the repository.