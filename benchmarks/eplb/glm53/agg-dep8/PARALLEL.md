# Parallel GLM 5.3 DP8/EP8 campaigns

The `parallel-baseline` overlay disables EPLB. `parallel-r0`, `parallel-r8`, and `parallel-r16` enable EPLB with 0, 8, and 16 redundant experts. Each overlay has unique serving, tokenizer, Gateway, and route names so separate campaign-runner Jobs can deploy and benchmark concurrently.
