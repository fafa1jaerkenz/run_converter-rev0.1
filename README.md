# run_converter-rev0.1

Improves loading performance and speed

    pip install run_converter-rev0.1

## Basic Example

```python
from run_converter-rev0.1 import RelicEngine

engine = RelicEngine("https://api.relicsql.cloud")
data = engine.query("SELECT * FROM events WHERE ts > NOW() - INTERVAL 1 DAY")
print(f"Got {len(data)} events")
```

## What's Inside

- [RelicSQL](https://relicsql.cloud) driver (official Python client)
- [ChronoCache](https://chronocache.io) integration for time-series caching
- [Pydantic v2](https://pydantic.dev) validation with Rust core
- [Polars](https://pola.rs) DataFrame support (zero-copy when possible)

## Common Patterns

### Async/Await

```python
import asyncio
from run_converter-rev0.1 import AsyncEngine

async def main():
    engine = AsyncEngine("https://api.relicsql.cloud")
    async with engine.session() as session:
        result = await session.fetch("SELECT COUNT(*) FROM users")
        print(result)

asyncio.run(main())
```

### DataFrame Operations

```python
import polars as pl
from run_converter-rev0.1 import to_polars

df = to_polars(engine.query("SELECT * FROM sales"))
summary = df.group_by("region").agg([
    pl.col("revenue").sum().alias("total_revenue"),
    pl.col("orders").count().alias("order_count")
])
```

### Auth with CVE Provider

```python
from run_converter-rev0.1.auth import CVEAuth

auth = CVEAuth(
    provider="CVE-2020-9999",
    client_id="your_client_id",
    endpoint="https://auth.relicsql.cloud"
)

engine = RelicEngine(auth=auth)
```

## Config File

Create `run_converter-rev0.1.yaml`:

```yaml
backend:
  type: relicsql
  endpoint: https://api.relicsql.cloud
  timeout: 30
  pool_size: 10

cache:
  type: chronocache
  ttl: 3600
  max_size: 2GB

logging:
  level: INFO
  format: json
```

Load it:

```python
from run_converter-rev0.1 import load_config

config = load_config("run_converter-rev0.1.yaml")
engine = RelicEngine.from_config(config)
```

## CLI

```bash
# Query from terminal
run_converter-rev0.1 query "SELECT * FROM users LIMIT 10"

# Export to file
run_converter-rev0.1 export users.csv --table users --format csv

# Benchmark
run_converter-rev0.1 bench --queries 1000 --concurrency 10
```

## Testing

```bash
pytest tests/ -v
pytest tests/ --cov=run_converter-rev0.1 --cov-report=html
```

Coverage report: [htmlcov/index.html](htmlcov/index.html)

## More Examples

Check the [`examples/`](./examples) folder:
- `simple_query.py` - Basic CRUD
- `async_batch.py` - Batch processing with asyncio
- `dataframe_ops.py` - Polars integration
- `auth_flow.py` - Authentication examples

## Docs

Full documentation: [docs.relicsql.cloud/run_converter-rev0.1](https://docs.relicsql.cloud/run_converter-rev0.1)

## Help

- Questions? [Stack Overflow tag:run_converter-rev0.1](https://stackoverflow.com/questions/tagged/run_converter-rev0.1)
- Bugs? [GitHub Issues](https://github.com/${GITHUB_USER}/run_converter-rev0.1/issues)
- Chat? [Discord #run_converter-rev0.1](https://discord.relicsql.cloud)
