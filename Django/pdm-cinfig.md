Since pdm is still not recognized, follow these steps to fix the issue:

1️⃣ Install PDM Properly
Run the following command in PowerShell:

```powershell
pip install --user pdm
or (if you are using pipx):
```

```powershell
pipx install pdm
```

2️⃣ Add PDM to System PATH (If Required)
If pdm is still not recognized, it might not be in your system PATH. To fix this:

Find the installation path:
Run:

```powershell
python -m site --user-base
```

This will output something like:

```makefile
C:\Users\user\AppData\Roaming\Python\PythonXX
```

Now, add the Scripts directory inside this path to your PATH. Run:

```powershell
$env:Path += ";C:\Users\aakas\AppData\Roaming\Python\PythonXX\Scripts"
```

Replace PythonXX with your actual Python version.

To permanently add it to your PATH:

Open System Properties (Win + R, type sysdm.cpl, press Enter).
```
Go to Advanced → Environment Variables.
Under User Variables, find Path and edit it.
Click New and add:
C:\Users\aakas\AppData\Roaming\Python\PythonXX\Scripts
Click OK and restart PowerShell.
```
3️⃣ Verify PDM Installation
Close PowerShell and open it again, then run:

```powershell
pdm --version
```
If it returns a version number, PDM is correctly installed.

4️⃣ Now Run pdm install
Once pdm is recognized, go to your project directory and run:

```powershell
pdm install
```
# 🚀
