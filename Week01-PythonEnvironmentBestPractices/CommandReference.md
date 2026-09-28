# Advanced Data Analysis with Python — Command Reference

A quick reference for the commands and functions introduced in Session 1
(Python Environment & Best Practices) and the prerequisite check.

---

## 1. Virtual Environments

| Command | What it does |
|---|---|
| `python -m venv .venv` | Creates a new virtual environment in a folder named `.venv` |
| `source .venv/bin/activate` | Activates the environment (macOS/Linux) |
| `.venv\Scripts\activate` | Activates the environment (Windows) |
| `which python` | Checks which Python interpreter is currently active — use this to confirm your virtual environment is active |
| `deactivate` | Exits the virtual environment, returning to the system Python |

**Why it matters:** a virtual environment is a self-contained Python install with
its own packages, so one project's dependencies never conflict with another's.
Create one per project, activate it before you work, and never install project
packages into your system-wide Python.

---

## 2. Package Management with pip

| Command | What it does |
|---|---|
| `pip install <package>` | Installs a package into the active environment (e.g. `pip install pandas`) |
| `pip install <pkg1> <pkg2>` | Installs multiple packages at once |
| `pip freeze > requirements.txt` | Saves the exact versions of every installed package to a file |
| `pip install -r requirements.txt` | Reinstalls everything listed in `requirements.txt` — used to recreate an environment elsewhere |
| `pip list` | Shows all packages currently installed in the active environment |
| `pip show <package>` | Shows details (version, location, dependencies) for one installed package |

**Why it matters:** `requirements.txt` is what lets a collaborator — or your
future self — recreate your exact setup with one command.

---

## 3. Python Core Concepts — Quick Reference

| Concept | Example | Notes |
|---|---|---|
| Floor division | `7 // 2` → `3` (an `int`) | Always returns an `int` when both operands are `int` |
| `range()` | `range(2, 8, 2)` → `2, 4, 6` | `range(start, stop, step)` — stops *before* reaching `stop` |
| Implicit return | A function with no `return` statement returns `None` | Not `0`, not an empty string, not an error |
| Mutable vs. immutable | `list` is mutable; `tuple`, `str`, `frozenset` are not | Mutable objects can be changed in place after creation |

---

## 4. Pandas & NumPy — Quick Reference

| Command | What it does |
|---|---|
| `pd.read_csv("file.csv")` | Loads a CSV file into a DataFrame |
| `df.dropna()` | Removes rows (or columns) containing missing values (`NaN`) |
| `df.fillna(value)` | Fills missing values instead of removing them |
| `df[df['col'] > value]` | Filters rows using boolean indexing — keeps only rows where the condition is `True` |
| `df.rename(columns={'old': 'new'})` | Renames one or more columns |
| `np.array([1, 2, 3, 4]).shape` | Returns `(4,)` — the shape of a 1D array |

---

## 5. Basic Statistics — Quick Reference

*(Concepts rather than commands, included here since they're prerequisite material.)*

| Concept | Meaning |
|---|---|
| Mean vs. median | The median is robust to outliers; the mean is not |
| Standard deviation | Measures how spread out the values in a dataset are |
| Linear regression `y = a + bx` | `a` is the intercept, `b` is the slope |
| R² (R-squared) | The proportion of variance in `y` explained by the model |
| Correlation coefficient near 0 | Indicates little to no *linear* relationship between two variables |

---

## Before Session 2

- [ ] Create a virtual environment for your project directory
- [ ] Install one AI coding tool (Codex, Copilot, JetBrains AI, or Claude) and try it on a small script
- [ ] Make sure `pip freeze` and `requirements.txt` work end to end on your machine
