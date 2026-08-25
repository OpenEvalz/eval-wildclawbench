# eval-wildclawbench

**WildClawBench: Productivity & Safety Evaluation**

> ⚠️ **Third-party eval.** This is a `register/` pointer in inspect_evals — the task code lives in an external repository of unaudited provenance and will execute on OpenEvalz infrastructure. Onboarding it is a security review, not a packaging task.

**Paper:** https://arxiv.org/abs/2605.10912v1

This is an Inspect AI wrapper of the original WildClawBench implementation. It does not
contain its own dataset or scoring logic. The original implementation can be found at
https://github.com/internlm/WildClawBench. Users provide a pinned checkout of the original
repository, tested here at commit 86d71447413d38f38740a021cb776f64eb396ee0, and the wrapper
reports native WildClawBench results through Inspect's scoring interface. WildClawBench
contains 60 human-authored bilingual and multimodal long-horizon agent tasks across
productivity flow, code intelligence, social interaction, search and retrieval, creative
synthesis, and safety alignment. The native harness runs OpenClaw-style agents in Docker and
grades with deterministic rule checks, environment-state auditing, and LLM/VLM judges; this
wrapper parses native summary*.json or per-task score.json outputs and reports the mean native
overall_score across scored tasks with Inspect mean/stderr metrics. It requires Docker, an
OpenAI-compatible model endpoint, the pinned Docker base image
node:22-bookworm@sha256:c601a46abb4d2ab80a9dc3da208d50d1122642d53f17a101926ace71e5a9bf1c,
openclaw@2026.6.10, and the exact Python dependencies pinned in the upstream pyproject.toml
and Dockerfile.

## At a glance

| | |
|---|---|
| Upstream | [`register/wildclawbench`](https://github.com/UKGovernmentBEIS/inspect_evals/tree/main/register/wildclawbench) |
| Group | — |
| Total samples | 60 |
| Execution class | `sandbox-local` |
| Cost class | `high` |
| Flags | sandboxed · needs internet |
| Tags | — |

### Tasks

| Task | Samples |
|---|---|
| `wildclawbench` | 60 |

### External assets

_None declared upstream._

## Running one problem

OpenEvalz is problem-level: the atomic unit is a single sample, not the whole eval.

```bash
inspect eval inspect_evals/wildclawbench \
  --sample-id "<sample-id>" \
  --model openai-api/trustedrouter/<model> \
  --token-limit 200000
```

> **Two things that bite here, both verified in Inspect's source.**
>
> 1. **`--cost-limit` does not work on this routing path.** Inspect only records cost when its
>    pricing table resolves the model, and `_model_info.py` strips only `azure|bedrock|vertex`
>    prefixes — so `trustedrouter/<model>` never resolves and the cap silently never binds. The
>    real spend cap is enforced **server-side by TrustedRouter** via the delegated key's
>    `limit_microdollars` and spend window. Use `--token-limit` as the in-process bound.
> 2. **`--sample-id` matches with `fnmatch`.** A glob silently selects many samples and only warns.
>    Always pass a literal id.

## Reproducibility

`bundle.template.json` is the contract. A run that cannot emit a complete bundle does not publish.
Every image is pinned by `sha256` digest and every dataset by revision.

## Licensing

OpenEvalz wrapper code in this repository is **Business Source License 1.1** (see `LICENSE`) —
Licensor Lore Hex Corp, Change Date four years from publication, Change License Apache 2.0, no
Additional Use Grant. Same terms as TrustedRouter. Source-available, not open source: you may read,
modify and make non-production use of it, but production use needs a commercial licence
(licensing@openevalz.com).

**The packaged evaluation is NOT relicensed.** The task code, dataset and container images come from
upstream under their own terms — inspect_evals is MIT (UK AI Security Institute), and individual
datasets and images carry their own, sometimes unstated, licences. BSL covers only the OpenEvalz
packaging around them. See `NOTICE.md`, which must be completed before this repo publishes anything.
