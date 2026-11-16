# GLM 5.3 aggregated DP8/EP8 EPLB campaign

These three Kustomize directories are managed overlays for campaign-runner. Each uses the same release, Gateway, model label, and serving selector because the campaign applies one case, benchmarks it, and removes it before applying the next.

| Directory | EPLB | Redundant experts |
| --- | --- | ---: |
| `eplb-r0` | enabled | 0 |
| `eplb-r8` | enabled | 8 |
| `eplb-r16` | enabled | 16 |

The shared HTTPRoute sends ordinary generation requests to the llm-d InferencePool. Its exact `/tokenize` and `/detokenize` rules send tokenizer calls to four rank-specific Services, each selecting both decode Pods and one local vLLM port. Equal backend weights distribute tokenization across all eight DP ranks while keeping measured generation traffic on the InferencePool.

Each directory includes the source Manifesto spec, its rendered deployment resources, the four tokenizer Services, and the `kustomization.yaml` that appends both tokenizer routes. The campaign uses these endpoints to prepare the fixed-length ShareGPT prompt cache on the Lustre PVC before measured requests. Re-render these files from the agentX research workspace with `eplb-research/scripts/build_managed_overlays.py`.
