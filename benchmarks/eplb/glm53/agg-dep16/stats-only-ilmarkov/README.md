# GLM 5.3 aggregated DP16/EP16 EPLB stats-only (ilmarkov)

This is the Kubernetes overlay used by `hard-tools/src/nm_hard_tools/campaign/run.py`
for the `ilmarkov` owner. It runs one four-member LWS with DP16/EP16, external
DP load balancing, FlashInfer CuteDSL, and DSpark-5 adaptive verification.

EPLB is enabled with zero redundant experts and `stats_only: true`, which logs
expert loads without rearranging experts. JSONL snapshots are written to
`/mnt/lustre/ilmarkov/eplb-research/glm53-agg-dep16-stats-only/eplb-jsonl`;
balancedness logging is enabled every 1000 steps.

`deployment.yaml`, `tokenize-services.yaml`, and `kustomization.yaml` are the
actual Kubernetes resources. Four local-rank Services provide `/tokenize` and
`/detokenize` backends across all 16 DP ranks.

The smoke campaign config is
`eplb-research/campaigns/glm53-agg-dep16-eplb-stats-sharegpt-ilmarkov-smoke.json`
in the AgentX workspace. It uses ShareGPT ISL 8192 / OSL 1024 at concurrency
64, 128, 256, 384, and 512 with short stages.
