# Repository Guidelines

## Project Structure & Module Organization
`starvector/` contains the Python package. Key subpackages are `data/` for dataset loaders and preprocessing, `model/` for architecture code, `train/` for training entrypoints, `validation/` for SVG-Bench evaluation, `serve/` for Gradio and worker processes, and `metrics/` for scoring utilities. Keep reusable code inside `starvector/`, not `scripts/`.

`configs/` stores YAML configs for training, generation, metrics, and Accelerate/DeepSpeed setups. `scripts/` contains quickstarts and shell wrappers. `assets/` holds example images and paper figures. `docker/` contains the container build and notebook examples.

## Build, Test, and Development Commands
Install the package in editable mode:

```bash
uv sync --locked
uv sync --locked --extra train
```

`uv` is the preferred workflow here; the project pins PyTorch `2.11.0` / TorchVision `0.26.0` and routes Linux/Windows installs to the official CUDA 13 wheel index.

Run a local inference smoke test:

```bash
python scripts/quickstart.py
```

Run benchmark validation with an explicit config override:

```bash
python starvector/validation/validate.py config=configs/generation/hf/starvector-1b/im2svg.yaml dataset.dataset_name=starvector/svg-stack
```

Launch training from a checked-in config:

```bash
accelerate launch --config_file configs/accelerate/deepspeed-8-gpu.yaml starvector/train/train.py config=configs/models/starvector-1b/im2svg-stack.yaml
```

## Coding Style & Naming Conventions
Target Python 3.12 and follow existing PEP 8 style: 4-space indentation, snake_case for modules/functions/variables, and PascalCase for classes such as `StarVectorForCausalLM`. Match the current import style and avoid broad refactors when touching a focused area. Name configs by task and dataset, for example `im2svg-stack.yaml` or `text2svg-figr.yaml`.

## Testing Guidelines
There is no dedicated `tests/` suite yet, so every PR should include a targeted runtime check. Use `scripts/quickstart*.py` for inference changes, `starvector/validation/validate.py` for model or metric changes, and the relevant `python -m starvector.serve...` path for demo changes. If you add tests, place them under a new `tests/` directory and use `test_<module>.py`.

## Commit & Pull Request Guidelines
Recent commits use short, imperative subjects such as `Fix 8b validation config` and `Update README.md`. Keep commit titles brief, scoped, and descriptive. PRs should summarize the behavior change, list touched configs or checkpoints, include the exact validation command used, and attach screenshots when modifying the Gradio UI.

## Configuration Tips
Do not commit secrets or local paths. Training scripts expect environment variables such as `HF_HOME`, `HF_TOKEN`, `WANDB_API_KEY`, and `OUTPUT_DIR`. Keep large datasets, checkpoints, and generated artifacts out of the repository unless they are intentionally versioned assets.
