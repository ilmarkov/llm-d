# GLM 5.3 aggregated DP16/EP16 EPLB stats-only

This Kustomize overlay is intended for `hard-tools/src/nm_hard_tools/campaign/run.py`.
It reuses the DP8 GLM 5.3 stack, scaled to DP16 across four 4-GPU LWS members.
It keeps external DP load balancing, the `megamoe` fabric profile, FlashInfer
CuteDSL MoE backend, and DSpark-5 with adaptive verification.

EPLB remains enabled with `num_redundant_experts: 0`. The image's
`eplb_config.stats_only: true` setting collects stats while skipping expert
rearrangement. EP rank zero appends JSONL snapshots under
`/mnt/lustre/ilmarkov-2/eplb-research/glm53-agg-dep16-stats-only/eplb-jsonl`;
balancedness text logs are also enabled every 1000 steps. The path is on the
Lustre PVC mounted by the base manifest, so it survives campaign cleanup.

`manifesto-spec.yaml` is the flattened, self-contained source spec.
`deployment.yaml`, `tokenize-services.yaml`, and `kustomization.yaml` are the
rendered campaign overlay. `/tokenize` and `/detokenize` use four local-rank
Services; with four LWS members, each Service selects that local rank on all
four members, covering all 16 DP ranks. The deployment is not applied by
rendering or by `kustomize build`.

The matching `run.py` config is
`eplb-research/campaigns/glm53-agg-dep16-eplb-stats-sharegpt-c64-512.json` in
the AgentX workspace. It defines ShareGPT ISL 8192 / OSL 1024 at concurrency
64, 128, 256, 384, and 512. The campaign is prepared for the `eplb-bench`
branch and is not submitted yet.
