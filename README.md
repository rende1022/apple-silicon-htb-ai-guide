# apple-silicon-htb-ai-guide
20260828
# Hack The Box AI Course
## Unofficial Apple Silicon (M5) macOS Setup Guide

> **Status:** Community Guide
>
> **Platform:** Apple Silicon (M5)
>
> **Operating System:** macOS (zsh)
>
> **Tested with:** Conda 26.x, Homebrew, Python 3.11

---

# Disclaimer

This repository is an **unofficial installation guide** for running the Hack The Box AI learning environment on **Apple Silicon (M5) macOS**.

This guide **does not reproduce or replace** Hack The Box Academy course materials. It focuses solely on environment setup, compatibility adjustments, and installation notes specific to macOS.

Please use the official Hack The Box Academy course as the primary learning resource.

---

# AI Assistance & Human Verification

This document was initially drafted with AI assistance.

All commands, configuration changes, compatibility notes, and troubleshooting steps have been:
- manually reviewed
- tested on a real Apple Silicon macOS environment
- corrected where necessary
- validated before publication
The final content reflects actual installation results rather than AI-generated output alone.

---

# Test Environment

| Component | Version |
|-----------|---------|
| Hardware | Apple M1 |
| Architecture | Apple Silicon (ARM64) |
| macOS | Tested on Apple Silicon macOS |
| Shell | zsh |
| Homebrew | Latest |
| Conda | 26.x |
| Python (Base) | 3.14 |
| Python (AI Environment) | 3.11 |
| PyTorch | Apple Silicon Build |
| GPU Backend | Apple Metal (MPS) |

---

# Why This Guide Exists

The official Hack The Box AI course is primarily written for Linux and Windows users.

Several steps require adjustment on Apple Silicon:

- macOS uses **zsh** instead of Bash by default.
- Apple GPUs use **Metal Performance Shaders (MPS)** instead of CUDA.
- Conda 26.x introduced configuration changes.
- NumPy 2.x may produce ABI compatibility warnings with some AI packages.

This guide documents those differences.

---

# Prerequisites

- Apple Silicon Mac
- Homebrew installed
- Git installed
- Internet connection

Optional:

- Docker Desktop
- Ollama

---

# Verify Existing Environment

## Verify Shell

```bash
echo $SHELL
```

Expected:

```text
/bin/zsh
```

---

## Verify Homebrew

```bash
which brew
brew --version
```

Expected:

```text
/opt/homebrew/bin/brew
```

---

## Verify Git

```bash
which git
git --version
```

Expected:

```text
/opt/homebrew/bin/git
```

---

# Install Miniconda

```bash
brew install --cask miniconda
```

Restart Terminal.

Verify:

```bash
conda --version
```

---

# Initialize Conda

Instead of:

```bash
conda init
```

Use:

```bash
conda init zsh
```

Reason:

- macOS default shell is zsh
- modifies `~/.zshrc`
- avoids unnecessary Bash configuration

If Bash was initialized previously:

```bash
conda init bash --reverse
```

Then:

```bash
conda init zsh
```

Reload:

```bash
source ~/.zshrc
```

Verify:

```bash
conda info
```

---

# Disable Base Auto Activation

For Conda 26.x:

```bash
conda config --set auto_activate false
```

If you see:

```text
Key auto_activate_base is an alias of auto_activate
```

This is expected.

Verify:

```bash
conda config --show auto_activate
```

Expected:

```text
auto_activate: False
```

---

# Configure Channels

```bash
conda config --add channels defaults

conda config --add channels conda-forge

conda config --add channels pytorch

conda config --set channel_priority strict
```

**Do NOT add**

```bash
conda config --add channels nvidia
```

Apple Silicon does not use CUDA.

---

# Create AI Environment

Even if your Base environment uses Python 3.14:

```text
Python 3.14.x
```

Create a dedicated environment with Python 3.11:

```bash
conda create -n ai python=3.11
```

Conda automatically downloads Python 3.11.

No separate installation is required.

---

# Activate Environment

```bash
conda activate ai
```

Verify:

```bash
python --version
```

---

# Install AI Packages

Recommended:

```bash
conda install -y \
"numpy<2" \
scipy \
pandas \
scikit-learn \
matplotlib \
seaborn \
transformers \
datasets \
tokenizers \
accelerate \
evaluate \
optimum \
huggingface_hub \
nltk \
category_encoders
```

---

# Install PyTorch

Use:

```bash
conda install \
pytorch \
torchvision \
torchaudio \
-c pytorch
```

Do **NOT** install:

```text
pytorch-cuda
```

Apple Silicon uses:

- Metal Performance Shaders (MPS)

instead of CUDA.

---

# Verify Apple GPU

```bash
python -c "import torch; print(torch.backends.mps.is_available())"
```

Expected:

```text
True
```

Additional verification:

```bash
python -c "import torch; x=torch.ones(3, device='mps'); print(x); print(x.device)"
```

Expected:

```text
tensor([1., 1., 1.], device='mps:0')
mps:0
```

---

# Install Additional Packages

```bash
pip install requests requests_toolbelt
```

---

# Update Environment

```bash
conda update --all
```

---

# Leave Environment

```bash
conda deactivate
```

---

# Useful Commands

| Action | Command |
|---------|----------|
| Create Environment | `conda create -n ai python=3.11` |
| Activate | `conda activate ai` |
| Deactivate | `conda deactivate` |
| List Environments | `conda env list` |
| Environment Info | `conda info` |
| Update | `conda update --all` |
| Remove Environment | `conda remove -n ai --all` |

---

# Known Issues

## Conda initializes Bash

Symptom:

```
modified ~/.bash_profile
```

Solution:

```bash
conda init bash --reverse
conda init zsh
```

---

## Base activates automatically

Solution:

```bash
conda config --set auto_activate false
```

---

## NumPy ABI Warning

Example:

```text
A module compiled using NumPy 1.x cannot be run in NumPy 2.x
```

Solution:

```bash
conda install "numpy<2"
```

---

## Apple Silicon CUDA

Do **not** install:

```text
pytorch-cuda
```

Use MPS instead.

---

## Verify GPU

```bash
python -c "import torch; print(torch.backends.mps.is_available())"
```

Expected:

```text
True
```

---

# Tool Responsibilities

| Tool | Purpose |
|------|---------|
| Homebrew | System utilities (Git, curl, Docker CLI, etc.) |
| Conda | Python & AI libraries |
| pip | Python packages unavailable via Conda |
| Docker Desktop | Containers |
| Ollama | Local LLM runtime |

---

# Best Practices

- Initialize Conda with `conda init zsh`
- Keep Base environment clean
- Create one Conda environment per project
- Prefer Python 3.11 for AI projects
- Use Apple Metal (MPS) instead of CUDA
- Keep Homebrew and Conda managing separate responsibilities

---

# Workflow

```text
Terminal

↓

rende_wang %

↓

conda activate ai

↓

(ai) rende_wang %

↓

AI Development

↓

conda deactivate

↓

rende_wang %
```

---

# License

This repository is released under the **MIT License**.

---

# Acknowledgements

- Hack The Box Academy
- Anaconda / Conda
- PyTorch
- Homebrew
- Apple Developer Documentation

This repository is an independent community effort and is not affiliated with or endorsed by Hack The Box.
