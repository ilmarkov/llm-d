# GLM 5.3 aggregated DP16/EP16 EPLB stats-only (ilmarkov)

This is the Kubernetes overlay used by `hard-tools/src/nm_hard_tools/campaign/run.py`
for the `ilmarkov` owner. It runs one four-member LWS with DP16/EP16, external
DP load balancing, FlashInfer CuteDSL, and DSpark-5 adaptive verification.

EPLB is enabled with zero redundant experts and `stats_only: true`, which logs
expert loads without rearranging experts. DSpark explicitly sets
`draft_enable_eplb: true`, and inherits the same stats-only config. The GLM
5.3 draft is a dense Qwen3 model with no MoE layers, so it has no expert-load
snapshots; JSONL records come from the target MoE model. Snapshots are written to
`/mnt/lustre/ilmarkov/eplb-research/glm53-agg-dep16-stats-only/eplb-jsonl`;
balancedness logging is enabled every 1000 steps.

`deployment.yaml`, `tokenize-services.yaml`, and `kustomization.yaml` are the
actual Kubernetes resources. Four local-rank Services provide `/tokenize` and
`/detokenize` backends across all 16 DP ranks.
The EPP image is pinned to v0.11.0 because this manifest uses
`token-producer.vllm.endpointDiscovery`.

`rbac-epp.yaml` contains the EPP Role and RoleBinding. It is deliberately not
included in the campaign Kustomize build: the `benchmark-campaign` service
account cannot grant itself the InferencePool API permissions it needs. Apply
this bootstrap manifest with the cluster submission identity before submitting
the campaign, then remove the Role and RoleBinding after the run if the overlay
has been cleaned up.

The smoke campaign config is
`eplb-research/campaigns/glm53-agg-dep16-eplb-stats-sharegpt-ilmarkov-smoke.json`
in the AgentX workspace. It uses ShareGPT ISL 8192 / OSL 1024 at concurrency
64, 128, 256, 384, and 512 with short stages.
