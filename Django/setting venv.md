# My Python Project

This project demonstrates how to use a virtual environment with Python in VS Code.

## Getting Started

1. Clone the repository (if applicable).
2. Create a virtual environment:

```
python3 -m venv .venv
```

Using virtualenv (if you have it installed):

```Bash
virtualenv .venv
```

3. Activate the virtual environment:
   - Windows:

```
.venv\Scripts\activate
```

- macOS/Linux:

```
source .venv/bin/activate
```

4. Install dependencies:

```
pip install -r requirements.txt
```

(if you have a requirements file) or `pip install <package_name>` for individual packages. 5. Run the main script:

```
python my_script.py
```

(replace with your script name).

## Configure VS Code to Use the Virtual Environment:

- VS Code should automatically detect your virtual environment. If it does, you'll see the Python interpreter path in the bottom-left corner of VS Code. It should point to the Python executable inside your .venv folder.
- If VS Code doesn't automatically detect it, or if you need to switch:
  - Open the Command Palette (Ctrl+Shift+P or Cmd+Shift+P).
  - Type "Python: Select Interpreter" and choose the command.
  - Select the Python interpreter from the list that's located within your .venv folder (e.g., .venv\Scripts\python.exe on Windows or .venv/bin/python on macOS/Linux).

## Additional Notes

- Always use virtual environments to manage dependencies.
- Keep your dependencies organized and isolated from other projects.
- Use `requirements.txt` to store your project's dependencies.

## References

- [Python Virtual Environments](https://docs.python.org/3/library/venv.html)
- [Pip Documentation](https://pip.pypa.io/en/stable/)

## Summary

This project demonstrates the use of virtual environments in Python to manage dependencies and isolate project environments.

# My Python Project

This project demonstrates how to use a virtual environment with Python in VS Code.
