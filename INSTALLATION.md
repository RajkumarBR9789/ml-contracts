# Installation Guide

Complete installation guide for ML-Contracts.

## Table of Contents

- [PyPI Installation](#pypi-installation)
- [GitHub Installation](#github-installation)
- [Development Installation](#development-installation)
- [Troubleshooting](#troubleshooting)
- [Requirements](#requirements)

## PyPI Installation

The easiest way to install ML-Contracts is from PyPI.

### Prerequisites

- Python 3.9 or higher
- pip (Python package installer)

### Install from PyPI

```bash
pip install rajkumar-ml-contracts
```

### Verify Installation

```python
import ml_contracts
print(ml_contracts.__version__)

# Try importing the main classes
from ml_contracts import DataContract, ModelContract, feature_contract
print("✓ Installation successful!")
```

## GitHub Installation

Install directly from the GitHub repository for the latest development version.

### Using pip with Git

```bash
pip install git+https://github.com/RajkumarBR9789/ml-contracts.git
```

### Or Clone and Install

```bash
# Clone the repository
git clone https://github.com/RajkumarBR9789/ml-contracts.git
cd ml-contracts

# Install in editable mode
pip install -e .
```

## Development Installation

Follow these steps if you want to contribute or modify the code.

### 1. Clone the Repository

```bash
git clone https://github.com/RajkumarBR9789/ml-contracts.git
cd ml-contracts
```

### 2. Create a Virtual Environment

**Using venv (recommended):**

```bash
# Linux/macOS
python3 -m venv venv
source venv/bin/activate

# Windows (PowerShell)
python -m venv venv
venv\Scripts\Activate.ps1

# Windows (Command Prompt)
python -m venv venv
venv\Scripts\activate.bat
```

**Using conda:**

```bash
conda create -n ml-contracts python=3.9
conda activate ml-contracts
```

### 3. Install Dependencies

```bash
# Install the package in editable mode
pip install -e .

# Install additional development dependencies
pip install pytest pytest-cov black flake8 mypy
```

### 4. Verify Installation

```bash
# Run tests
pytest tests/

# Check code quality
flake8 src/ml_contracts/
black --check src/ml_contracts/
```

## Requirements

### Core Dependencies

- **pandas**: >= 1.5
  - For DataFrame operations and data validation
  
- **numpy**: Latest
  - For numerical operations
  
- **scipy**: >= 1.10
  - For statistical distribution checks

### Optional Dependencies

For development:

- **pytest**: >= 7.0 - Testing framework
- **pytest-cov**: Code coverage reporting
- **black**: Code formatter
- **flake8**: Linter
- **mypy**: Type checker

View the complete dependency list in `pyproject.toml`:

```toml
[project]
dependencies = [
    "pandas>=1.5",
    "scipy>=1.10",
    "numpy"
]
```

## Troubleshooting

### Issue: "ModuleNotFoundError: No module named 'ml_contracts'"

**Solution**: Make sure the package is installed:

```bash
pip install rajkumar-ml-contracts
# or
pip install -e .  # if installing from source
```

### Issue: "ImportError: cannot import name 'DataContract'"

**Solution**: Ensure you're using the correct import:

```python
# Correct
from ml_contracts import DataContract

# Incorrect
from ml_contracts.contracts import DataContract  # Don't do this
```

### Issue: Version conflict with pandas/scipy

**Solution**: Install compatible versions:

```bash
pip install --upgrade pandas scipy numpy
```

Or use the exact versions:

```bash
pip install "pandas>=1.5" "scipy>=1.10" numpy
```

### Issue: "pip: command not found" (Linux/macOS)

**Solution**: Use python3 -m pip:

```bash
python3 -m pip install rajkumar-ml-contracts
```

### Issue: Conda environment activation not working

**Solution**: Use conda to install:

```bash
conda install -c conda-forge ml-contracts
# Note: May not be available on conda-forge initially
```

### Issue: "Permission denied" during installation

**Solution**: Use pip with --user flag:

```bash
pip install --user rajkumar-ml-contracts
```

Or use a virtual environment (recommended).

## Platform-Specific Installation

### Linux/macOS

```bash
# Python 3.9+
python3 -m venv venv
source venv/bin/activate
pip install rajkumar-ml-contracts
```

### Windows (PowerShell)

```powershell
# Create virtual environment
python -m venv venv
venv\Scripts\Activate.ps1

# Install package
pip install rajkumar-ml-contracts
```

### Windows (Command Prompt)

```cmd
python -m venv venv
venv\Scripts\activate.bat
pip install rajkumar-ml-contracts
```

## Jupyter Notebook Installation

If using in Jupyter notebooks:

```python
# Install from notebook cell
!pip install rajkumar-ml-contracts
```

Then use it:

```python
from ml_contracts import DataContract
import pandas as pd

# Your code here
```

## Docker Installation

If you want to use ML-Contracts in Docker:

```dockerfile
FROM python:3.9-slim

WORKDIR /app

# Install the package
RUN pip install rajkumar-ml-contracts

# Copy your code
COPY . .

# Run your application
CMD ["python", "main.py"]
```

Build and run:

```bash
docker build -t ml-contracts-app .
docker run ml-contracts-app
```

## Upgrade Installation

To upgrade to the latest version:

```bash
pip install --upgrade rajkumar-ml-contracts
```

## Uninstall

To remove the package:

```bash
pip uninstall rajkumar-ml-contracts
```

## Getting Help

If you encounter issues during installation:

1. **Check Python version**: `python --version` (needs 3.9+)
2. **Check pip version**: `pip --version`
3. **Clear pip cache**: `pip cache purge`
4. **Try installing with verbose output**: `pip install -v rajkumar-ml-contracts`
5. **Open an issue**: https://github.com/RajkumarBR9789/ml-contracts/issues

---

For more information, see:
- [README.md](README.md) - Main documentation
- [EXAMPLES.md](EXAMPLES.md) - Usage examples
- [CONTRIBUTING.md](CONTRIBUTING.md) - Contribution guidelines
