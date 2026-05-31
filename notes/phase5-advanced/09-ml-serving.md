# ML serving infrastructure

> Phase 5 · Tags: `ml` `infra`

## 1. The concept
Serving = the part of ML that turns a trained model into a low-latency API your product calls.

Components:
- **Model registry** (MLflow, SageMaker Model Registry) — versioned artifacts.
- **Feature store** (Feast, Tecton) — online (low-latency lookup) + offline (training) feature parity.
- **Inference server** (Triton, TorchServe, BentoML, vLLM for LLMs) — runs the model, batches requests, manages GPU.
- **Model gateway** — fronts inference, does AB tests, fallback, observability.
- **Online vs batch inference** — request/response vs scheduled scoring of millions of rows.

## 2. The rule / the why
ML productionization fails mostly on **operational** problems: training/serving feature drift, model rot, no monitoring, runaway costs. The infra is what stops that.

## 3. Java-specific behavior
- Most serving stacks aren't Java-native. Java apps usually call inference via HTTP/gRPC.
- ONNX Runtime has Java bindings if you want in-process scoring (small models).
- DJL (Deep Java Library) for training/serving from Java directly.

## 4. System design angle
- Latency budgets: a 10ms inference call inside a 100ms user request is fine; a 500ms one isn't.
- Batching requests on the inference server cuts GPU cost dramatically (10× throughput often).
- Shadow deploys for new models (mirror traffic, compare predictions, don't expose).
- Cache predictions where keys are stable.

## 5. Common mistakes / traps
- Training/serving skew: features computed differently in pipeline vs request path → wrong predictions.
- No monitoring of input distributions → silent degradation as data shifts.
- Hosting LLMs without batching → 100× the GPU cost.
- One model per service explosion — use a shared model server.

## 6. Revision checklist
- Feature store online vs offline: ______
- Why batching helps: ______
- One ML-specific failure mode: ______
- Shadow deploy purpose: ______
