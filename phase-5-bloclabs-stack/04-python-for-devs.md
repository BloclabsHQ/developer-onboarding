# Python for Developers

> Python is for scripting, data processing, and ML pipelines. Not for production APIs (use Go).

---

## When We Use Python

| Use Case | Why |
|----------|-----|
| Data processing scripts | Fast to write, rich ecosystem (pandas, numpy) |
| ML pipelines | PyTorch, TensorFlow, scikit-learn |
| Quick automation | Fastest path from idea to working script |
| Prototyping | Test ideas before building in Go |
| CLI tools | Click, typer for quick CLIs |

---

## Environment Setup

**Always use virtual environments.** Never install packages globally.

```bash
# Create project
mkdir my-script && cd my-script

# Create virtual environment
python -m venv .venv
source .venv/bin/activate

# Install dependencies
pip install requests pandas

# Save dependencies
pip freeze > requirements.txt

# Recreate environment
pip install -r requirements.txt

# Deactivate when done
deactivate
```

---

## Modern Python Patterns

### Type Hints (Required)
```python
from datetime import datetime
from decimal import Decimal

def calculate_fee(amount: Decimal, chain: str) -> Decimal:
    """Calculate transaction fee for the given chain."""
    rates: dict[str, Decimal] = {
        "ethereum": Decimal("0.003"),
        "solana": Decimal("0.0001"),
    }
    rate = rates.get(chain)
    if rate is None:
        raise ValueError(f"Unsupported chain: {chain}")
    return amount * rate
```

### Dataclasses
```python
from dataclasses import dataclass

@dataclass
class Transaction:
    id: str
    amount: Decimal
    chain: str
    status: str
    created_at: datetime
    
    @property
    def is_confirmed(self) -> bool:
        return self.status == "confirmed"
```

### Error Handling
```python
class AppError(Exception):
    """Base application error."""
    pass

class ChainNotSupported(AppError):
    def __init__(self, chain: str):
        self.chain = chain
        super().__init__(f"Chain not supported: {chain}")

# Usage
try:
    result = process_transaction(data)
except ChainNotSupported as e:
    logger.warning(f"Unsupported chain requested: {e.chain}")
    raise
except ConnectionError:
    logger.error("Database connection failed")
    raise AppError("Service temporarily unavailable")
```

---

## Tooling

```bash
# Linting + formatting (use ruff — replaces black, isort, flake8)
pip install ruff
ruff check .       # Lint
ruff format .      # Format

# Type checking
pip install mypy
mypy src/          # Static type analysis

# Testing
pip install pytest
pytest tests/ -v
```

### pyproject.toml (Modern Config)
```toml
[project]
name = "my-tool"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "requests>=2.31",
    "pandas>=2.0",
]

[tool.ruff]
line-length = 100

[tool.pytest.ini_options]
testpaths = ["tests"]
```

---

## When Python vs Go

| Factor | Python | Go |
|--------|--------|-----|
| Speed | Slow (100x slower than Go for CPU) | Fast |
| Startup | Slow | Instant |
| Typing | Optional (type hints) | Mandatory |
| Best for | Scripts, data, ML, prototypes | APIs, services, production |
| Deployment | Needs Python runtime | Single binary |

**Rule:** Prototype in Python, productionize in Go.

---

*← [JavaScript & TypeScript](03-javascript-typescript.md) · [PostgreSQL →](05-postgresql.md)*
