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

# ZetaLatency — C++20 Lock-Free Pipelines & Tail Latency

ZetaLatency is a benchmark-first C++20 project for **measuring and understanding tail latency** in real-time pipelines. It focuses on the practical engineering work that low-latency trading interviews care about:

- lock-free vs mutex-based queues
- cache alignment and false sharing
- CPU pinning and warm-up methodology
- NUMA effects and throughput vs jitter tradeoffs
- reproducible benchmark runs with p50/p99/p99.9 reporting

---

## What ZetaLatency measures

### Queue implementations
- `std::mutex` + condition variable queue (baseline)
- SPSC ring buffer (lock-free)
- MPSC queue (lock-free with contention notes)
- batch vs single-message dequeue variants

### Pipeline shapes
- single producer → single consumer (SPSC)
- fan-in (MPSC) with multiple producers
- staged pipelines (ingest → parse → book update) using interchangeable queue types

### Output metrics
- throughput (msgs/sec)
- latency histograms (p50 / p95 / p99 / p99.9)
- per-stage timing breakdowns (optional)
- allocation counters (where supported)

---

## Reproducible methodology (what makes this credible)
ZetaLatency is designed so results are comparable across commits.

Checklist:
- pin threads to cores (affinity)
- fixed message sizes / fixed datasets
- warm-up period before measurement
- release builds only (`-O3`, LTO optional)
- record machine + kernel + CPU governor
- export results to CSV for dashboards

---

## Quickstart

### Build
```bash
mkdir -p build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
cmake --build . -j
```

### Run benchmarks (example)
```bash
./ZetaLatency_bench --scenario spsc --queue ring --seconds 30 --warmup 10 --out out/spsc_ring.csv
./ZetaLatency_bench --scenario spsc --queue mutex --seconds 30 --warmup 10 --out out/spsc_mutex.csv
```

### Plot (optional)
```bash
python3 python/plot_hist.py out/spsc_ring.csv
```

---

## What to publish on your portfolio site

Include a small table like this (fill with your measured numbers):

| Scenario | Queue | Throughput | p50 | p99 | p99.9 | Notes |
|---|---|---:|---:|---:|---:|---|
| SPSC | ring | TBD | TBD | TBD | TBD | pinned core |
| SPSC | mutex | TBD | TBD | TBD | TBD | baseline |
| MPSC | lock-free | TBD | TBD | TBD | TBD | N producers |
| Pipeline | mixed | TBD | TBD | TBD | TBD | staged |

---

## Suggested repo layout

```
ZetaLatency/
  cpp/
    queues/
    pipeline/
    bench/
  python/
    plot_hist.py
  docs/
    methodology.md
    results.md
  out/
  CMakeLists.txt
  README.md
```

---

## Roadmap
- add a small perf regression CI job (store last-known-good baseline)
- add CPU governor check + warnings
- expand NUMA experiments and document results
- add optional eBPF/perf integration scripts

---
