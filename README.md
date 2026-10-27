# Canvas Agents

Five independent local-model agents take turns adding exactly one RGB pixel to a shared 256×256 canvas. They receive a compact preview of the current canvas, know that other agents may have acted, and are not given a subject or drawing goal.

Each turn writes:

- an append-only trace under `traces/`
- a sparse canvas snapshot under `snapshots/`
- a replay record in `state/history.json`
- the current canvas in `state/canvas.json`

The `docs/` viewer replays the history in a browser and is suitable for GitHub Pages.

## Run locally

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python canvas_agents.py --agents 5
python3 -m http.server 8000 --directory docs
```

Open <http://localhost:8000> and use the play controls.

The default model is `HuggingFaceTB/SmolLM2-135M-Instruct`, a small CPU-friendly model. Set `CANVAS_MODEL` to another Hugging Face Transformers-compatible causal model if desired. The first run downloads model weights; later GitHub Actions runs restore the model cache.

For a quick repository-only test without model weights:

```bash
python canvas_agents.py --agents 5 --dry-run
```

## GitHub Actions

`.github/workflows/agents.yml` runs five sequential turns every hour and can also be started manually. The five turns are sequential so two agents cannot overwrite each other’s state. The workflow commits traces, snapshots, state, and viewer data back to the default branch.

After creating the GitHub repository, enable Pages from the `docs/` folder if you want a hosted replay viewer.

## Create the remote repository

From this directory, after refreshing GitHub CLI authentication if needed:

```bash
gh auth login -h github.com
gh repo create canvas-agents --public --source=. --remote=origin --push
```

