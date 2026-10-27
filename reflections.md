# Reflection

## 1. Which issues were the easiest to fix, and which were the hardest?

The easiest were the Flake8 style issues (E203, E231, E261, E302). Each one was a single whitespace change at a line the report named exactly. The Pylint docstring warnings and the `elif` after `return` were also quick.

The hardest was the Bandit High issue (B605), the `os.system` call with string concatenation. Replacing it with `subprocess.run` using an argument list was not enough, because Bandit then raised three Low notes (B404, B603, B607). To clear them properly I validated the server name with a regular expression and found the full path of `scp` with `shutil.which`. Renaming the camelCase functions to snake_case was also fiddly, because every call site had to change with it.

## 2. Did the tools report any false positives?

Partly. B311 flagged `random.choice` in the coin toss, but a coin toss is not a security feature, so there was no real risk. I switched to `secrets.choice` anyway so the report would be clean. B105 flagged `REFEREE_PASSWORD = "referee-demo"`, which was only demo data, but hardcoding credentials is a bad habit, so I moved it to an environment variable. After the `subprocess` fix, B404 and B603 appeared even though I had already validated the input. I marked those two lines with `# nosec` after reviewing them, and I would document why in a real project.

## 3. How would you integrate static analysis into a real development workflow?

I would run Flake8 and Pylint on every save through the editor, and as a pre-commit hook so style problems never reach the repository. I would run all three tools, including Bandit, in a CI pipeline (for example GitHub Actions) on every pull request, and fail the build on High or Medium Bandit findings or a Pylint score under an agreed threshold. The tools should have a shared configuration file so the whole team follows the same rules, and `# nosec` or disabled checks should need a written reason in review.

## 4. What improvements did you see in code quality after the fixes?

The Pylint score went from 4.75/10 to 10.00/10, Flake8 went from 4 warnings to none, and Bandit went from 1 High, 1 Medium and 2 Low issues to none. The program output was identical before and after, so behaviour did not change. The code is now more readable and consistent (snake_case names, docstrings), and it is safer: the mutable default `players=[]` would have made teams share one list, the shell command can no longer be injected into, the temp file cannot be hijacked, and no password sits in the source. Static analysis found bugs I would not have noticed by running the program, because the demo worked even with all of them present.