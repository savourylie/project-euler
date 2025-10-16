# Project Euler — Multi-Language + LLM Solutions

Practice Project Euler problems across multiple languages. Each problem folder keeps:
- **My solution(s)**
- **LLM variants** (with prompt + model metadata)
- **Tests/benchmarks**
- **Notes** (approach, complexity, pitfalls)

> ⚠️ **Respect Project Euler:** Don’t publish problem statements. Keep numeric answers out of READMEs; store them privately (see **Answers & tests**). Follow their terms of use.

---

## Goals
- Deliberate practice in multiple languages
- Compare approaches and trade-offs
- Preserve LLM attempts with full provenance
- Verify via tests + lightweight benchmarks
- Re-run solutions as languages/toolchains evolve

---

## Repo structure

```
.
├─ problems/
│  ├─ 001-multiples-of-3-or-5/
│  │  ├─ README.md                 # approach notes (no problem text)
│  │  ├─ my/
│  │  │  ├─ python/solution.py
│  │  │  ├─ js/solution.mjs
│  │  │  └─ rust/src/main.rs
│  │  ├─ llm/
│  │  │  ├─ gpt-5-thinking/python/solution.py
│  │  │  ├─ gpt-5-thinking/metadata.json
│  │  │  └─ gemini-2.0/…
│  │  ├─ tests/test_001.py
│  │  └─ bench/notes.md
│  └─ 002-…/
├─ prompts/
│  └─ 001-multiples-of-3-or-5/prompt.md
├─ answers/               # private (gitignored) expected answers
│  └─ answers.yaml
├─ scripts/
│  ├─ run_python.sh
│  ├─ run_js.sh
│  └─ run_rust.sh
├─ .gitignore
└─ LICENSE
```

**Naming:** `NNN-kebab-title` where `NNN` is zero-padded problem number.

---

## Problem template

Create from this skeleton:

```
problems/NNN-title/
├─ README.md
├─ my/<lang>/solution.<ext>
├─ llm/<model>/<lang>/solution.<ext>
├─ llm/<model>/metadata.json
├─ tests/test_NNN.<ext>
└─ bench/notes.md
```

**`README.md` template**
```md
# NNN — Short Title

## Approach
- Brief idea and key steps

## Complexity
- Time: O(...)
- Space: O(...)

## Alternatives
- …

## Gotchas
- …
```

**`llm/<model>/metadata.json` example**
```json
{
  "model": "gpt-5-thinking",
  "date": "2025-10-16",
  "language": "python",
  "prompt_path": "../../../prompts/001-multiples-of-3-or-5/prompt.md",
  "params": {"temperature": 0.2, "top_p": 0.9},
  "notes": "First pass; optimized loop later."
}
```

---

## Answers & tests (keep answers private)

Add `answers/answers.yaml` and **do not commit it**.

**`.gitignore`**
```
answers/
```

**`answers/answers.yaml` (local only)**
```yaml
# Map of problem number to expected numeric answer
"001": 233168
"002": 4613732
```

**Example `tests/test_001.py`**
```python
import os, yaml, importlib.util, pathlib

def load_answer(n):
    p = pathlib.Path(__file__).parents[2] / "answers" / "answers.yaml"
    if not p.exists():
        return None
    return yaml.safe_load(p.read_text()).get(f"{n:03d}")

def import_solution(path):
    spec = importlib.util.spec_from_file_location("solution", path)
    mod = importlib.util.module_from_spec(spec); spec.loader.exec_module(mod)
    return mod

def test_python_my_solution_matches_answer():
    ans = load_answer(1)
    if ans is None:
        return  # skip when answers are private/not present
    sol = import_solution(str(pathlib.Path(__file__).parents[1] / "my" / "python" / "solution.py"))
    assert sol.solve() == ans
```
> Tip: mirror this pattern for JS/TS (Node + vitest) and Rust (cargo test).

---

## Quick start

1) Pick a problem:  
```bash
mkdir -p problems/001-multiples-of-3-or-5/my/python
```

2) Write `solution.py` with a `solve()` function returning the numeric answer.

3) (Optional) Add an LLM attempt under `llm/<model>/<lang>/`.

4) Put your local answers in `answers/answers.yaml`.

5) Run tests (example Python):  
```bash
pytest problems/001-multiples-of-3-or-5/tests/test_001.py -q
```

---

## Benchmarks (optional)

Keep simple, human-readable timings in `bench/notes.md`:
```
Python (3.12):  2.1 ms (p95 over 100 runs)
Rust (1.82):    0.3 ms
JS/Node (22):   1.5 ms
```

---

## LLM guidelines

- Store the **exact prompt** in `prompts/NNN-…/prompt.md`.  
- Record **model id, date, params** in `metadata.json`.  
- Do not paste problem text or numeric answers into prompts.  
- Prefer deterministic params (low temperature) for reproducibility.  
- Add a short note on how you verified the output.

---

## CI suggestions

- **Python:** `pytest`  
- **JS/TS:** `vitest`  
- **Rust:** `cargo test`  
- Gate PRs on tests; never rely on LLM output without verification.

---

## Languages

Start with what you use most; expand later:
- Python, JS/TS (Node), Rust, Go, Java, C++

---

## License

- Code: MIT (or your choice).  
- Do **not** include Project Euler problem text. Link to their site for problem statements.

---

## FAQ

**Why keep answers private?**  
To respect the spirit of Project Euler while still enabling automated verification.

**Why multiple languages?**  
Broaden problem-solving skills, compare idioms and performance, and practice tooling.
