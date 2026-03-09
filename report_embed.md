# vLLM Embedding Benchmark Report

## 1. System Information

| Component | Value |
|-----------|--------|
| GPU | NVIDIA RTX 3090 |
| VRAM | 24 GB |
| Framework | vLLM |
| Model | BAAI/bge-m3 |
| Endpoint | http://localhost:8001/v1/embeddings |

## 2. Test Configuration

- **Input:** `hello world`
- **API:** `POST /v1/embeddings`
- **Method:** `curl` + `xargs` concurrency test

## 3. Functional Test

Single request completed successfully and returned a valid embedding vector.

**Example request:**

```bash
curl http://localhost:8001/v1/embeddings \
  -H "Content-Type: application/json" \
  -d '{"model": "BAAI/bge-m3", "input": "hello world"}'
```

**Result:** Embedding response OK; model loaded; endpoint functioning.

## 4. Concurrency Test (50 requests, concurrency 10)

| Phase | Latency |
|-------|---------|
| First batch (warm-up) | ~34–41 ms |
| Steady-state | ~13–16 ms |

Indicates initial warm-up overhead, then stable low-latency embedding.

## 5. Success Rate Test (100 requests, concurrency 20)

- **Result:** 100 requests → 100 HTTP 200 → **100% success rate**

## 6. Average Latency (100 requests, concurrency 20)

- **Measured:** avg embed latency 0.0216 s → **~21.6 ms**

## 7. Performance Summary

| Metric | Result |
|--------|--------|
| Single request | Success |
| Avg embedding latency | ~21.6 ms |
| Concurrency tested | 20 |
| Success rate | 100% |
| Steady-state latency | ~13–16 ms |

## 8. Throughput Estimate

With concurrency 20 and avg latency 0.0216 s:

**Throughput ≈ 20 / 0.0216 ≈ 925 req/s**

(Rough estimate; sustained throughput may vary.)

## 9. Observations

- Service stable under concurrent load.
- Warm-up overhead in first batch; steady-state latency very low.
- Embedding much faster than LLM inference on the same machine.

## 10. Conclusion

BAAI/bge-m3 + vLLM + RTX 3090 provides low latency, 100% success rate, and good concurrency stability. Well suited for local RAG, real-time query embeddings, document ingestion, and vector search.
