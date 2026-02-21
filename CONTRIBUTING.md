# Contributing to ML-Contracts

Thank you for considering contributing to ML-Contracts! This document provides guidelines and instructions for contributing.

## 🤝 How to Contribute

### Reporting Bugs

Before creating a bug report, please check the issue list to ensure it hasn't already been reported.

When you create a bug report, include:
- **Title**: Clear, descriptive title
- **Description**: What you expected to happen vs. what actually happened
- **Steps to Reproduce**: Minimal code example to reproduce the issue
- **Python Version**: Your Python version
- **Environment**: OS and any relevant package versions

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion, include:
- **Title**: Clear, descriptive title
- **Description**: Detailed description of the enhancement
- **Examples**: Concrete examples showing how the feature would work
- **Rationale**: Why this enhancement would be useful

### Pull Requests

1. **Fork the repository** and create a new branch:
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes**:
   - Follow the coding style (PEP 8)
   - Add tests for new functionality
   - Update documentation as needed

3. **Run tests**:
   ```bash
   pytest tests/
   ```

4. **Commit with clear messages**:
   ```bash
   git commit -m "feat: Add your feature description"
   ```

5. **Push to your fork**:
   ```bash
   git push origin feature/your-feature-name
   ```

6. **Open a Pull Request** with a clear description

## 📋 Development Setup

1. **Clone the repository**:
   ```bash
   git clone https://github.com/RajkumarBR9789/ml-contracts.git
   cd ml-contracts
   ```

2. **Create a virtual environment**:
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install in development mode**:
   ```bash
   pip install -e .
   pip install pytest pytest-cov
   ```

4. **Run tests**:
   ```bash
   pytest tests/ -v
   ```

## 🎯 Code Standards

- **Style**: Follow PEP 8
- **Type Hints**: Use type hints for all function signatures
- **Docstrings**: Write clear docstrings for all functions and classes
- **Tests**: Write tests for all new functionality
- **Comments**: Add comments for complex logic

### Example:

```python
def validate(self, df: pd.DataFrame) -> bool:
    """Validate DataFrame against the contract.
    
    Args:
        df: DataFrame to validate
        
    Returns:
        True if validation passes
        
    Raises:
        ContractViolation: If validation fails
    """
    # Your implementation here
    pass
```

## 📝 Commit Message Format

Use the following format for commits:

- `feat:` A new feature
- `fix:` A bug fix
- `docs:` Documentation changes
- `refactor:` Code refactoring
- `test:` Adding or updating tests
- `style:` Code style changes (formatting, missing semicolons, etc.)
- `chore:` Dependency updates, build process changes

Example:
```
feat: Add support for categorical data validation

- Add CategoricalContract class
- Add tests for categorical validation
- Update documentation with examples
```

## 🧪 Testing

- Write tests for all new features
- Ensure all tests pass before submitting PR
- Aim for high code coverage

Run tests with coverage:
```bash
pytest tests/ --cov=src/ml_contracts --cov-report=html
```

## 📚 Documentation

- Update README.md if adding new features
- Add docstrings to all public functions
- Update API reference if changing public API
- Include examples in docstrings

## 🔄 Review Process

1. **Initial Review**: Maintainer reviews your PR
2. **Feedback**: Address any requested changes
3. **Approval**: PR is approved once all comments are resolved
4. **Merge**: Maintainer merges to main branch

## ✅ Checklist Before Submitting

- [ ] Fork and branch from `main`
- [ ] Add tests for new features
- [ ] All tests pass locally
- [ ] Code follows PEP 8 style
- [ ] Docstrings are complete
- [ ] README/docs are updated if needed
- [ ] Commit messages follow format
- [ ] No unrelated changes in PR

## 📞 Questions?

Feel free to open an issue with the `question` label or reach out to the maintainers.

## 📄 License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

Thank you for contributing to ML-Contracts! 🎉
