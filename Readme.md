# Primary stack

- C++20

- CMake

- Google Benchmark or custom harness

- GoogleTest (ring buffer correctness tests)

- pthread affinity + Linux-only perf options

- Python: pandas + matplotlib for histogram charts

- Perf tooling (docs + scripts)

- Linux: perf, taskset, chrt (optional), CPU governor notes

- bash scripts to run benchmarks and collect outputs

# CI

GitHub Actions: build/tests

nightly perf baseline (store CSV artifacts)