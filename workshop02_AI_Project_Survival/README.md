# Workshop 02 — AI Project Survival

## Project

**Repository:** https://github.com/TencentARC/GFPGAN

**Inference task:** Restore facial details in input images using a pretrained GFPGAN model.

## Repo map

**Environment:** Python 3.8, uv virtual environment, PyTorch, torchvision, BasicSR, facexlib, and Real-ESRGAN.

**Entry point:** `inference_gfpgan.py`

**Model:** `GFPGANv1.3.pth`

**Input:** `inputs/whole_imgs/`

**Output:** `results/restored_imgs/`

## Environment setup

Clone the repository and create a fresh Python 3.8 virtual environment.

```bash
git clone https://github.com/TencentARC/GFPGAN.git
cd GFPGAN
uv venv --python 3.8
source .venv/bin/activate
```

Install the required dependencies and the GFPGAN package.

The `-r requirements.txt` option installs the dependencies listed in the repository's requirements file, while `-e .` installs the local GFPGAN package in editable mode. This allows Python to import the package directly from the cloned repository without reinstalling it after source code changes.

```bash
uv pip install -r requirements.txt -e .
uv pip install realesrgan
```

Real-ESRGAN is installed separately because it is required for the inference pipeline.

Fix the BasicSR import compatibility issue.

```bash
sed -i.bak 's/from torchvision.transforms.functional_tensor import rgb_to_grayscale/from torchvision.transforms.functional import rgb_to_grayscale/' .venv/lib/python3.8/site-packages/basicsr/data/degradations.py
```

Download the pretrained GFPGAN model.

```bash
wget https://github.com/TencentARC/GFPGAN/releases/download/v1.3.0/GFPGANv1.3.pth -P experiments/pretrained_models
```

## Inference

Run the following command from the GFPGAN repository directory.

```bash
python inference_gfpgan.py -i inputs/whole_imgs -o results -v 1.3 -s 2
```

The restored images are saved in `results/restored_imgs/`.

One input image and its corresponding restored output are included in the `evidence/` folder.

## One real failure

**Category:** Dependency compatibility

**Root cause:** BasicSR attempted to import `rgb_to_grayscale` from `torchvision.transforms.functional_tensor`, which is not available in the installed torchvision version.

**Error:** `ModuleNotFoundError: No module named 'torchvision.transforms.functional_tensor'`

**Minimal fix:** Change the BasicSR import statement to use `torchvision.transforms.functional` instead.

```bash
sed -i.bak 's/from torchvision.transforms.functional_tensor import rgb_to_grayscale/from torchvision.transforms.functional import rgb_to_grayscale/' .venv/lib/python3.8/site-packages/basicsr/data/degradations.py
```

## AI Agent Check

**Which AI coding agent did you use?**

OpenAI Codex.

**What advice did it give?**

I used Codex to investigate the appropriate Python version, installation commands, and pretrained inference command based on the repository files and README. I also asked it to analyse an inference error without executing commands or modifying files.

**What command or fix did you choose to run yourself?**

I manually installed the dependencies, applied the BasicSR compatibility fix, and ran the inference command. I used ChatGPT for additional troubleshooting after consulting Codex.

**How did you verify the result?**

I reran the inference command and confirmed that the restored images were generated in `results/restored_imgs/`.
