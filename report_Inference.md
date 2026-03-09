# vLLM Inference Benchmark Report

## 1. System Information

| Component | Value |
|-----------|--------|
| GPU | NVIDIA RTX 3090 |
| VRAM | 24 GB |
| CUDA | 13.1 |
| Driver | 590.48 |
| Framework | vLLM |
| Model | Qwen/Qwen2.5-7B-Instruct |
| API | OpenAI-compatible `/v1/chat/completions` |
| Endpoint | http://localhost:8000 |

## 2. Model & Test Config

- **Model:** Qwen/Qwen2.5-7B-Instruct  
- **max_tokens:** 64 · **temperature:** default · **context_length:** default  

**Prompt:** *Explain AI in one sentence*

## 3. Test Methodology

- **Tool:** `curl` + `xargs` concurrency load test  
- **Requests per run:** 200  

**Example (variable concurrency `$c`):**

```bash
seq 1 200 | xargs -I{} -P $c bash -c '
curl -s -o /dev/null -w "%{time_total}\n" \
  http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '\''{"model":"Qwen/Qwen2.5-7B-Instruct","messages":[{"role":"user","content":"Explain AI in one sentence"}],"max_tokens":64}'\''
' | awk '{sum+=$1} END {print "avg latency:",sum/NR,"s"}'
```

## 4. Latency vs Concurrency

| Concurrency | Avg Latency |
|-------------|-------------|
| 1 | 0.80 s |
| 2 | 0.82 s |
| 5 | 0.83 s |
| 10 | 0.86 s |
| 20 | 0.83 s |
| 30 | 0.90 s |
| 40 | 0.95 s |
| 50 | 0.96 s |
| 60 | 1.06 s |
| 80 | 1.38 s |
| 100 | 1.45 s |

## 5. Stability Test

- **Config:** Concurrency 80, 200 requests  
- **Result:** 200 success, 0 failures → **100% success rate**

```bash
seq 1 200 | xargs -I{} -P 80 bash -c '
curl -s -o /dev/null -w "%{http_code}\n" \
  http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '\''{"model":"Qwen/Qwen2.5-7B-Instruct","messages":[{"role":"user","content":"Explain AI in one sentence"}],"max_tokens":64}'\''
' | sort | uniq -c
# Output: 200 200
```

## 6. Throughput

- Concurrency = 80, avg latency ≈ 1.49 s  
- **Throughput ≈ 80 / 1.49 ≈ 53.7 req/s**

## 7. Performance Summary

| Metric | Result |
|--------|--------|
| Single-request latency | ~0.8 s |
| Latency @ 40 concurrency | ~0.95 s |
| Latency @ 80 concurrency | ~1.49 s |
| Stable throughput | ~54 req/s |
| Success rate | 100% |

**Observations:** Latency grows slowly up to ~60 concurrency (effective continuous batching); saturation beyond ~60; 100% success at 80 concurrent requests.

## 8. Recommended Operating Range

| Mode | Concurrency |
|------|-------------|
| Low latency | 40–50 |
| Balanced | 50–60 |
| High throughput | ~80 |

## 9. Real-World Expectations

**This benchmark:** short prompt, `max_tokens=64`, localhost, no RAG.

**Production-style workloads** add embedding latency, vector search, longer prompts/outputs.

| Scenario | Expected concurrency |
|----------|----------------------|
| RAG API | 30–50 |
| Agent workflows | 20–40 |
| Short-prompt inference | 60–80 |

## 10. Hardware Summary (RTX 3090 + Qwen2.5-7B)

| Metric | Estimate |
|--------|----------|
| Max stable concurrency | ~80 |
| Optimal concurrency | 50–60 |
| Throughput | ~50 req/s |
| Single-request latency | ~0.8 s |

## 11. Suggested Next Benchmarks

- **Longer outputs:** `max_tokens = 256`
- **Longer prompts:** 500–1000 tokens
- **RAG pipeline:** embedding + vector search + LLM + end-to-end latency

## 12. Conclusion

Single-GPU vLLM inference is stable up to ~80 concurrent requests with ~54 req/s and ~0.8 s single-request latency. Suitable for local AI development, RAG prototyping, and small-to-medium inference workloads.
