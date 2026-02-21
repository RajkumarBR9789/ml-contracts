# ML-Contracts

[![PyPI Version](https://img.shields.io/pypi/v/rajkumar-ml-contracts.svg)](https://pypi.org/project/rajkumar-ml-contracts/)
[![GitHub](https://img.shields.io/badge/github-RajkumarBR9789-blue)](https://github.com/RajkumarBR9789/ml-contracts)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**Design-by-Contract for Machine Learning Pipelines**: Enforce data quality, feature validation, and model contracts pre-deployment with minimal overhead.

A lightweight, production-ready library that ensures your ML pipelines maintain data integrity and quality across all stages.

## 🎯 Features

- **Data Contracts**: Validate input data schema, ranges, and distributions
- **Feature Contracts**: Enforce feature engineering validation rules
- **Model Contracts**: Define model input/output expectations
- **Fail-Fast Validation**: Catch data quality issues early in the pipeline
- **Production-Ready**: Lightweight, zero external dependencies (except pandas, numpy, scipy)
- **Easy Integration**: Works seamlessly with existing ML workflows

## 📦 Installation

Install from PyPI:

```bash
pip install rajkumar-ml-contracts
```

Or install from GitHub:

```bash
pip install git+https://github.com/RajkumarBR9789/ml-contracts.git
```

## 🚀 Quick Start

### 1. Data Contract Validation

```python
from ml_contracts import DataContract
import pandas as pd

# Define a data contract
contract = DataContract(
    name="input-data",
    schema={"age": int, "income": float, "score": int},
    ranges={"age": (18, 75), "income": (0, 1000000), "score": (0, 100)},
    distribution={"age": "norm"}
)

# Validate your data
df = pd.DataFrame({
    "age": [25, 30, 45],
    "income": [50000, 75000, 120000],
    "score": [85, 90, 88]
})

try:
    contract.validate(df)
    print("✓ Data contract passed!")
except ContractViolation as e:
    print(f"✗ Contract violation: {e}")
```

### 2. Feature Contract Validation

```python
from ml_contracts import feature_contract

@feature_contract(
    input_schema={"raw_value": float},
    output_schema={"normalized_value": float},
    ranges={"normalized_value": (0, 1)}
)
def normalize_feature(df):
    df["normalized_value"] = (df["raw_value"] - df["raw_value"].min()) / (df["raw_value"].max() - df["raw_value"].min())
    return df

df = pd.DataFrame({"raw_value": [10, 20, 30]})
result = normalize_feature(df)
print(result)
```

### 3. Model Contract Validation

```python
from ml_contracts import ModelContract

model_contract = ModelContract(
    name="credit-model",
    input_schema={"age": int, "income": float},
    output_schema={"prediction": float, "probability": float},
    output_ranges={"prediction": (0, 1), "probability": (0, 1)}
)

# Validate model inputs/outputs
X_test = pd.DataFrame({"age": [30, 45], "income": [50000, 100000]})
y_pred = pd.DataFrame({"prediction": [0.7, 0.8], "probability": [0.85, 0.92]})

try:
    model_contract.validate(X_test, y_pred)
    print("✓ Model contract passed!")
except ContractViolation as e:
    print(f"✗ Contract violation: {e}")
```

## 📚 API Reference

### DataContract

```python
DataContract(
    name: str,                    # Contract name
    schema: dict,                 # {column: dtype} mapping
    ranges: dict = None,          # {column: (min, max)} validation ranges
    distribution: dict = None,    # {column: "norm"|"uniform"|...} expected distributions
    nullable: bool = True         # Allow null values
)
```

**Methods:**
- `validate(df)` - Validate a DataFrame against the contract
- `get_schema()` - Get the expected schema
- `get_ranges()` - Get the expected ranges

### ModelContract

```python
ModelContract(
    name: str,                    # Contract name
    input_schema: dict,           # Expected input columns and types
    output_schema: dict,          # Expected output columns and types
    output_ranges: dict = None,   # Output value ranges
    description: str = None       # Contract description
)
```

**Methods:**
- `validate(X, y)` - Validate model inputs and outputs
- `validate_input(X)` - Validate only inputs
- `validate_output(y)` - Validate only outputs

### feature_contract (Decorator)

```python
@feature_contract(
    input_schema: dict,           # Input DataFrame schema
    output_schema: dict,          # Output DataFrame schema
    ranges: dict = None,          # Output ranges to validate
    nullable: bool = True
)
def your_feature_function(df):
    # Your feature engineering logic
    return df
```

## 🛠️ Use Cases

- **Data Pipeline Validation**: Ensure data quality at every stage
- **Feature Engineering**: Guarantee features meet specification
- **Model Deployment**: Validate model inputs match training expectations
- **Data Quality Monitoring**: Catch schema/distribution drift
- **ML Pipeline Testing**: Automated validation in CI/CD

## 📋 Package Structure

```
ml-contracts/
├── src/ml_contracts/
│   ├── __init__.py           # Public API exports
│   ├── contracts.py          # DataContract & ModelContract classes
│   ├── exceptions.py         # ContractViolation exception
│   └── features.py           # feature_contract decorator
├── tests/
│   └── test_contracts.py     # Unit tests
├── pyproject.toml            # Project configuration
├── LICENSE                   # MIT License
└── README.md                 # Documentation
```

## 🧪 Testing

Run tests:

```bash
pytest tests/
```

## 📝 Examples

### Example 1: Data Quality Validation in ETL

```python
from ml_contracts import DataContract
import pandas as pd

# Define contract for raw data
raw_data_contract = DataContract(
    name="raw-transactions",
    schema={
        "transaction_id": str,
        "amount": float,
        "timestamp": str,
        "status": str
    },
    ranges={"amount": (0, 100000)},
    nullable=False
)

# Validate incoming data
df = pd.read_csv("transactions.csv")
raw_data_contract.validate(df)
```

### Example 2: Feature Engineering Pipeline

```python
from ml_contracts import feature_contract
import pandas as pd

@feature_contract(
    input_schema={"user_age": int, "account_age_days": int},
    output_schema={"age_group": str, "account_tenure": float},
    ranges={"account_tenure": (0, 100)}
)
def engineer_user_features(df):
    df["age_group"] = pd.cut(df["user_age"], bins=[0, 25, 40, 60, 100])
    df["account_tenure"] = df["account_age_days"] / 365.25
    return df

df = pd.DataFrame({
    "user_age": [23, 35, 50],
    "account_age_days": [365, 730, 1095]
})

result = engineer_user_features(df)
print(result)
```

### Example 3: Model Deployment Validation

```python
from ml_contracts import ModelContract
import pandas as pd

# Define what your model expects
model_contract = ModelContract(
    name="customer-churn",
    input_schema={
        "monthly_charges": float,
        "tenure_months": int,
        "total_charges": float
    },
    output_schema={
        "churn_prediction": int,
        "churn_probability": float
    },
    output_ranges={
        "churn_prediction": (0, 1),
        "churn_probability": (0.0, 1.0)
    }
)

# Validate before making predictions
X = pd.DataFrame({
    "monthly_charges": [65.5, 89.2],
    "tenure_months": [24, 48],
    "total_charges": [1573.0, 4272.0]
})

model_contract.validate_input(X)
print("✓ Input data matches model expectations!")
```

## 🤝 Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👤 Author

**Rajkumar BR**
- GitHub: [@RajkumarBR9789](https://github.com/RajkumarBR9789)
- PyPI: [rajkumar-ml-contracts](https://pypi.org/project/rajkumar-ml-contracts/)

## 🙏 Acknowledgments

- Designed for production ML pipelines
- Inspired by design-by-contract principles
- Built with Python's type hints and runtime validation

## 📞 Support

For issues and questions:
- **GitHub Issues**: https://github.com/RajkumarBR9789/ml-contracts/issues
- **PyPI Project**: https://pypi.org/project/rajkumar-ml-contracts/

---

Made with ❤️ by [Rajkumar BR](https://github.com/RajkumarBR9789)
