# EventChains Performance Benchmarks

Comprehensive performance validation of the EventChains middleware pattern across multiple programming languages, hardware platforms, and 24 years of hardware evolution (2000-2024).

## Repository Contents

This repository contains:
- **Raw benchmark results** from 50+ test scenarios
- **Comprehensive Excel analysis** with visualizations and interpretations
- **Multi-tier benchmarking methodology** documentation
- **Hardware specifications** for all tested platforms
- **Reproducibility package** for validating results

## Quick Links

- **EventChains Website**: [eventchains.dev](https://eventchains.dev)
- **Implementation Repositories**: See individual language repos (C, C#, Rust, etc.)

## Executive Summary

EventChains demonstrates **negative overhead at enterprise scale** - becoming faster than manual implementations when workloads exceed a minimum complexity threshold. This validates a paradigm shift: abstraction layers can improve performance rather than degrade it.

### Key Findings

| Metric | Best Case | Typical Case | Status    |
|--------|-----------|--------------|-----------|
| Framework Overhead (T1) | C: +0.15µs (47%) | C#: +6µs (7-15%) | Excellent |
| Abstraction Cost (T2) | C: +0.08µs (23%) | C#: +6µs (14-23%) | Good      |
| Middleware Cost (T3) | C: 0.018µs/layer | C#: 0.029-0.122µs/layer | Excellent |
| **Negative Overhead** | **Rust 500 nodes: -169µs (-22.84%)** | Demonstrated at scale | Insanity  |

## Languages Tested

- **C** - Security-hardened v3.1.0 implementation
- **C#** - .NET Framework 2.0+ compatible
- **Rust** - Graph-based pathfinding benchmarks
- **Salesforce Apex** - Cloud platform with governor limits

**Additional implementations available** (not yet benchmarked in this study):
- Ruby, Python, JavaScript, COBOL

## Hardware Coverage

See [hardware/specifications.md](hardware/specifications.md) for detailed explanation of the approach and pitfalls avoided.

## Methodology: Four-Tier Benchmarking

See [METHODOLOGY.md](METHODOLOGY.md) for detailed explanation of the approach and pitfalls avoided.

## File Organization

### `/analysis/`
- **eventchains_benchmarks.xlsx** - Comprehensive Excel analysis with:
    - Executive summary
    - Language-by-language breakdowns
    - Cross-platform comparisons
    - Vintage hardware analysis
    - Methodology documentation

### `/results/`
Raw benchmark output organized by language and platform:
- **csharp/** - C# benchmarks (Windows, macOS, vintage)
- **c/** - C benchmarks (Windows, macOS, Raspberry Pi)
- **rust/** - Rust benchmarks (multiple scales)
- **salesforce/** - Salesforce Apex test results

### `/hardware/`
Detailed specifications for all tested hardware platforms.

## Key Results by Language

### C Implementation
- **Best Tier 2 Performance**: Raspberry Pi CM5 with only +4.87% overhead (+0.105µs)
- **Middleware Scaling**: 0.018µs per layer (macOS M4)
- **Cross-Platform**: Consistent performance across x86_64 and ARM64
- **Embedded Ready**: Validated on $50 Raspberry Pi hardware via emulation

### C# Implementation
- **Enterprise Scale**: 132,000-157,000 operations/second
- **Production Ready**: Negligible overhead vs database/network operations
- **24-Year Validation**: Consistent performance from Pentium III (2000) to Ryzen 9 (2024)
- **Linear Scaling**: 19x performance improvement over 24 years

### Rust Implementation
- **Negative Overhead Demonstrated**: -22.84% (-169µs) at 500 nodes
- **Scale Threshold**: Shows overhead at small scale, negative overhead at large scale
- **Work Encapsulation Principle**: Validates that sufficient work enables CPU optimizations

### Salesforce Apex
- **Governor Limits**: 0.05% CPU usage, all limits passed
- **Cloud Platform**: Production-ready for multi-tenant environments
- **Event Timing**: 2-3ms per event within 10,000ms limit

## The Negative Overhead Phenomenon

**Critical Finding**: At enterprise scale with sufficient work per event (100+ operations), EventChains becomes **faster** than manual implementations.

### Why This Matters
Traditional abstraction layers impose performance costs. EventChains demonstrates that:
1. With proper work encapsulation, abstraction can **improve** performance
2. CPU branch prediction and pipeline optimization outweigh framework overhead
3. The pattern enables optimizations impossible in manual branching logic

### When It Emerges
- **Small workloads** (<100 operations/event): +7-15% overhead (expected)
- **Enterprise workloads** (500+ operations/event): Overhead approaches zero
- **Large-scale processing** (1000+ nodes): Negative overhead (-22.84% demonstrated)

## Production Validation

EventChains is deployed in production:
- **Ritual Keeper**: Running at 60fps with complex event processing
- **Salesforce Applications**: Enterprise CRM workflows
- **Multiple Private Deployments**: Enterprise-scale applications

## Reproducing Results

All benchmark code is available in the respective implementation repositories:
- C: https://github.com/RPDevJesco/EventChain-Performance-Test
- C#: https://github.com/RPDevJesco/EventChains-CS-PerformanceTest
- Rust: https://github.com/RPDevJesco/rust_eventchains_performance_test
- Salesforce: https://github.com/RPDevJesco/salesforce-eventchains

Each repository includes:
- Complete implementation
- Benchmark harness
- Build instructions
- Test scenarios

## Citation

If you use EventChains or reference this benchmark data, please cite:

```
EventChains: A Composable Middleware Pattern with Negative Overhead at Scale
Jesco, 2024
https://github.com/RPDevJesco/eventchains_benchmark_results
```

Academic paper forthcoming on arXiv.

## License

- **Benchmark Data**: CC-BY 4.0 (Creative Commons Attribution)
- **Analysis Tools**: MIT License
- **EventChains Implementations**: MIT License

## Contributing

Additional benchmark results from other platforms are welcome:
- Test on your hardware
- Follow the 4-tier methodology
- Submit results via pull request

Please include:
- Hardware specifications
- OS and compiler versions
- Raw benchmark output
- Test configuration

## Contact

For questions about the benchmarks or EventChains pattern:
- **Website**: [eventchains.dev](https://eventchains.dev)
- **Issues**: Use GitHub issues for benchmark-specific questions

## Acknowledgments

- **Raspberry Pi Benchmarks**: Contributed by https://github.com/SoftwareGuy

---

**Last Updated**: November 2025 
**Benchmark Version**: 1.0  
**EventChains Version**: v3.1.0 (C), various (other languages)
**Maintained By**: Jesco