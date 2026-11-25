# Example

## Input

Review this before I merge (Python, written by an AI):
```python
import requests
from fastapi import FastAPI
app = FastAPI()
@app.get('/user')
def user(id: str):
    q = f"SELECT * FROM users WHERE id = {id}"
    return db.execute(q).fetchone()
@app.get('/fetch')
def fetch(url: str):
    return requests.get(url, verify=False).text
```

## Output (abridged)

## Verdict: BLOCK
SQL injection and SSRF on public endpoints; `db` is undefined.

## Findings
| # | Severity | Category | Location | Problem | Fix |
|---|---|---|---|---|---|
| 1 | Critical | Injection | `q = f"SELECT ... {id}"` | User input interpolated into SQL | Use a parameterised query |
| 2 | High | SSRF | `requests.get(url, ...)` | Fetches any user-supplied URL | Allow-list hosts, block private IP ranges |
| 3 | Medium | Security | `verify=False` | TLS verification disabled | Remove `verify=False` |
| 4 | High | Correctness | `db.execute` | `db` never defined/imported | Inject a DB session dependency |

## Missing Tests
- [ ] `/user` rejects `1 OR 1=1`
- [ ] `/fetch` rejects `http://169.254.169.254/`
