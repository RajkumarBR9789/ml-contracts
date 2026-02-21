# ML-Contracts Examples

This document provides detailed examples of how to use ML-Contracts in various scenarios.

## Table of Contents

1. [Data Validation](#data-validation)
2. [Feature Engineering](#feature-engineering)
3. [Model Contracts](#model-contracts)
4. [Error Handling](#error-handling)
5. [Production Pipeline](#production-pipeline)

## Data Validation

### Example 1: Basic Data Contract

Validate that incoming data matches your expected schema and ranges.

```python
from ml_contracts import DataContract, ContractViolation
import pandas as pd

# Define the contract
customer_contract = DataContract(
    name="customer-data",
    schema={
        "customer_id": str,
        "age": int,
        "annual_income": float,
        "credit_score": int,
        "account_status": str
    },
    ranges={
        "age": (18, 120),
        "annual_income": (0, 10000000),
        "credit_score": (300, 850)
    },
    nullable=False
)

# Load and validate data
df = pd.read_csv("customers.csv")

try:
    customer_contract.validate(df)
    print("✓ Data validation passed!")
    process_customers(df)
except ContractViolation as e:
    print(f"✗ Validation failed: {e}")
    # Handle invalid data
```

### Example 2: Distribution Validation

Ensure data follows expected distribution patterns.

```python
sales_contract = DataContract(
    name="sales-data",
    schema={
        "transaction_amount": float,
        "quantity": int
    },
    distribution={
        "transaction_amount": "norm"  # Expect normal distribution
    }
)

# Validate
sales_df = pd.read_csv("sales.csv")
sales_contract.validate(sales_df)
```

### Example 3: Nullable Columns

Handle optional/nullable columns properly.

```python
user_contract = DataContract(
    name="user-profile",
    schema={
        "user_id": str,
        "email": str,
        "phone": str,  # Optional
        "address": str  # Optional
    },
    nullable=True  # Allow nulls
)

users = pd.DataFrame({
    "user_id": ["U1", "U2", "U3"],
    "email": ["u1@test.com", "u2@test.com", "u3@test.com"],
    "phone": ["123-456", None, "789-012"],
    "address": [None, "123 Main St", "456 Oak Ave"]
})

user_contract.validate(users)
```

## Feature Engineering

### Example 1: Feature Normalization

Validate features are normalized properly.

```python
from ml_contracts import feature_contract
import pandas as pd
import numpy as np

@feature_contract(
    input_schema={"raw_value": float},
    output_schema={"normalized_value": float},
    ranges={"normalized_value": (0, 1)}
)
def normalize_minmax(df):
    """Min-max normalization of raw values."""
    col = df["raw_value"]
    df["normalized_value"] = (col - col.min()) / (col.max() - col.min())
    return df

# Use the decorated function
raw_data = pd.DataFrame({
    "raw_value": [10, 20, 30, 40, 50]
})

normalized = normalize_minmax(raw_data)
print(normalized)
# Output: normalized_value values will be between 0 and 1
```

### Example 2: Feature Binning

Create discrete features with validation.

```python
@feature_contract(
    input_schema={"age": int},
    output_schema={"age_group": str},
)
def create_age_groups(df):
    """Bin age into groups."""
    df["age_group"] = pd.cut(
        df["age"],
        bins=[0, 18, 35, 50, 65, 100],
        labels=["child", "young", "adult", "senior", "elderly"]
    )
    return df

data = pd.DataFrame({"age": [5, 22, 40, 65, 85]})
result = create_age_groups(data)
```

### Example 3: Feature Engineering Pipeline

Chain multiple feature contracts together.

```python
@feature_contract(
    input_schema={"salary": float, "bonus": float},
    output_schema={"total_comp": float},
)
def calculate_total_comp(df):
    df["total_comp"] = df["salary"] + df["bonus"]
    return df

@feature_contract(
    input_schema={"total_comp": float},
    output_schema={"comp_category": str},
)
def categorize_comp(df):
    df["comp_category"] = pd.cut(
        df["total_comp"],
        bins=[0, 50000, 100000, 200000, np.inf],
        labels=["entry", "mid", "senior", "executive"]
    )
    return df

# Pipeline
df = pd.DataFrame({
    "salary": [60000, 90000, 150000],
    "bonus": [5000, 10000, 50000]
})

df = calculate_total_comp(df)
df = categorize_comp(df)
print(df)
```

## Model Contracts

### Example 1: Classification Model

Validate a classification model's inputs and outputs.

```python
from ml_contracts import ModelContract
import pandas as pd
import numpy as np
from sklearn.ensemble import RandomForestClassifier

# Define model contract
churn_model_contract = ModelContract(
    name="churn-classifier",
    input_schema={
        "monthly_charges": float,
        "tenure_months": int,
        "customer_age": int,
        "monthly_usage_gb": float
    },
    output_schema={
        "churn_prediction": int,
        "churn_probability": float
    },
    output_ranges={
        "churn_prediction": (0, 1),
        "churn_probability": (0.0, 1.0)
    },
    description="Predicts customer churn probability"
)

# Prepare data
X = pd.DataFrame({
    "monthly_charges": [50.0, 75.0, 100.0],
    "tenure_months": [12, 24, 36],
    "customer_age": [30, 45, 50],
    "monthly_usage_gb": [10.5, 20.0, 30.5]
})

# Train model (simplified)
model = RandomForestClassifier()
y_dummy = [0, 1, 0]
model.fit(X, y_dummy)

# Validate inputs before prediction
churn_model_contract.validate_input(X)

# Make predictions
y_pred = model.predict(X)
y_proba = model.predict_proba(X)[:, 1]

predictions = pd.DataFrame({
    "churn_prediction": y_pred,
    "churn_probability": y_proba
})

# Validate outputs
churn_model_contract.validate_output(predictions)
```

### Example 2: Regression Model

Validate regression model outputs.

```python
from ml_contracts import ModelContract
from sklearn.linear_model import LinearRegression

# Define contract for price prediction
price_contract = ModelContract(
    name="house-price-predictor",
    input_schema={
        "square_feet": float,
        "bedrooms": int,
        "bathrooms": float,
        "year_built": int
    },
    output_schema={
        "predicted_price": float,
        "prediction_interval": str
    },
    output_ranges={
        "predicted_price": (10000, 10000000)
    }
)

# Prepare test data
X = pd.DataFrame({
    "square_feet": [2000, 3000, 2500],
    "bedrooms": [3, 4, 3],
    "bathrooms": [2.0, 2.5, 2.0],
    "year_built": [2010, 2015, 2012]
})

# Validate
price_contract.validate_input(X)
print("✓ Input data is valid for house price model")
```

## Error Handling

### Example 1: Graceful Error Handling

Handle validation errors gracefully.

```python
from ml_contracts import DataContract, ContractViolation
import pandas as pd

contract = DataContract(
    name="test",
    schema={"value": int},
    ranges={"value": (0, 100)}
)

df = pd.DataFrame({"value": [10, 150, 30]})  # 150 is out of range

try:
    contract.validate(df)
except ContractViolation as e:
    print(f"Contract violation: {e}")
    # Log the error
    logger.error(f"Data validation failed: {e}")
    # Implement fallback logic
    # Could: alert, retry, use cached data, etc.
```

### Example 2: Validation in Try-Except Block

```python
from ml_contracts import DataContract, ContractViolation

def process_data(df, contract):
    """Process data with validation."""
    try:
        contract.validate(df)
        return process_valid_data(df)
    except ContractViolation as e:
        logger.warning(f"Validation failed: {e}")
        # Alternative handling
        return handle_invalid_data(df)

# Usage
contract = DataContract(name="test", schema={"col": int})
result = process_data(df, contract)
```

## Production Pipeline

### Example: Complete ML Pipeline with Contracts

```python
from ml_contracts import DataContract, ModelContract, feature_contract, ContractViolation
import pandas as pd
from sklearn.ensemble import RandomForestClassifier

# Step 1: Define input contract
raw_contract = DataContract(
    name="raw-input",
    schema={
        "customer_id": str,
        "raw_feature_1": float,
        "raw_feature_2": float,
        "target": int
    },
    ranges={
        "raw_feature_1": (-100, 100),
        "raw_feature_2": (0, 1000),
        "target": (0, 1)
    }
)

# Step 2: Define feature engineering
@feature_contract(
    input_schema={"raw_feature_1": float, "raw_feature_2": float},
    output_schema={"feature_1_scaled": float, "feature_2_scaled": float},
    ranges={
        "feature_1_scaled": (-3, 3),
        "feature_2_scaled": (-3, 3)
    }
)
def engineer_features(df):
    """Scale and normalize features."""
    from sklearn.preprocessing import StandardScaler
    scaler = StandardScaler()
    df[["feature_1_scaled", "feature_2_scaled"]] = scaler.fit_transform(
        df[["raw_feature_1", "raw_feature_2"]]
    )
    return df

# Step 3: Define model contract
model_contract = ModelContract(
    name="production-classifier",
    input_schema={
        "feature_1_scaled": float,
        "feature_2_scaled": float
    },
    output_schema={
        "prediction": int,
        "probability": float
    },
    output_ranges={
        "prediction": (0, 1),
        "probability": (0.0, 1.0)
    }
)

# Step 4: Pipeline execution
def ml_pipeline(raw_data_path):
    """Complete ML pipeline with validation."""
    
    # Load and validate raw data
    df = pd.read_csv(raw_data_path)
    try:
        raw_contract.validate(df)
        print("✓ Raw data validation passed")
    except ContractViolation as e:
        print(f"✗ Raw data validation failed: {e}")
        return None
    
    # Engineer features
    try:
        df = engineer_features(df)
        print("✓ Feature engineering passed")
    except ContractViolation as e:
        print(f"✗ Feature engineering failed: {e}")
        return None
    
    # Prepare for model
    X = df[["feature_1_scaled", "feature_2_scaled"]]
    y = df["target"]
    
    # Train model
    model = RandomForestClassifier()
    model.fit(X, y)
    
    # Make predictions and validate
    y_pred = model.predict(X)
    y_proba = model.predict_proba(X)[:, 1]
    
    predictions = pd.DataFrame({
        "prediction": y_pred,
        "probability": y_proba
    })
    
    try:
        model_contract.validate_output(predictions)
        print("✓ Model predictions valid")
    except ContractViolation as e:
        print(f"✗ Model validation failed: {e}")
        return None
    
    return model, predictions

# Run pipeline
if __name__ == "__main__":
    result = ml_pipeline("data.csv")
    if result:
        model, predictions = result
        print("✓ Pipeline completed successfully!")
```

---

For more examples and use cases, check the tests directory: `tests/test_contracts.py`
