# Issues Found

| Issue | Type | Line(s) | Description | Fix Approach |
|-------|------|---------|-------------|--------------|
| Shell command injection (B605) | Security (High) | 71 | `os.system("scp table.json " + server + ...)` builds a shell command from the `server` string, so a crafted value could run arbitrary commands | Replace `os.system` with `subprocess.run([...], check=True)` using an argument list and no shell |
| Insecure temp file (B306) | Security (Medium) | 66 | `tempfile.mktemp()` only returns a name, so another process can create that file first (race condition) | Use `tempfile.NamedTemporaryFile(delete=False, suffix=".json")` or `mkstemp()` |
| Hardcoded password (B105) | Security (Low) | 6 | `REFEREE_PASSWORD = "referee-demo"` stores a secret in source code | Read it from an environment variable with `os.environ.get("REFEREE_PASSWORD")` |
| Weak random generator (B311) | Security (Low) | 61 | `random.choice` is not cryptographically secure | Use `secrets.choice` |
| Mutable default argument (W0102) | Bug | 11 | `players=[]` is shared by every team created without a list, so teams would share one players list | Default to `None` and create a new list inside the function |
| Function/argument names not snake_case (C0103) | Style | 11-69 | Names like `addTeam`, `teamName`, `homeGoals` use camelCase | Rename to `add_team`, `team_name`, `home_goals`, etc., and update every call |
| Missing docstrings (C0114, C0116) | Style | 1, 11-74 | The module and all functions have no docstrings | Add a short docstring to the module and each function |
| Unnecessary elif after return (R1705) | Style | 17 | `elif` after `return` is redundant | Change `elif` to `if` |
| Merge comparisons (R1714) | Style | 58 | `teamName == table[0] or teamName == table[1]` | Use `team_name in table[:2]` |
| E203 whitespace before ':' | Style (Flake8) | 5 | `"win" : 3` has a space before the colon | Change to `"win": 3` |
| E302 expected 2 blank lines | Style (Flake8) | 60 | Only one blank line before `tossCoin` | Add a second blank line |
| E231 missing whitespace after ',' | Style (Flake8) | 79 | `2,1` in a `record_match` call | Change to `2, 1` |
| E261 two spaces before inline comment | Style (Flake8) | 87 | One space before `# one row per team` | Use two spaces |