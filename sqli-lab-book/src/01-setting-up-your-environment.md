# Setting Up Your Environment

We are going to create a safe, isolated space for this project using a *virtual environment*

## 1. Create your project folder
Pick a safe location to store your work.

Inside of this folder, create an `sqli-lab` folder.

## 2. Install Python (if you have not already)
Open your web browser and go to [python.org/downloads](https://www.python.org/downloads/) and download the latest stable version of Python for your OS.

> [!IMPORTANT]
> **Windows Users:** When running the installer, make sure to check the box that says `Add python.exe to PATH` before clicking install. This is the most common cause of issues for beginners!


## 3. Verify Python is installed
Open your terminal and type the following command. Press `Enter` after typing it.

=== "Windows (PowerShell)"
```powershell
python --version
```

=== "macOS/Linux (Terminal)"
```bash
python3 --version
```

You should see something along the lines of `Python 3.XX.X`. If you get an error, see [Troubleshooting](./appendix-troubleshooting.md).

## 4. Create and open your project with VS Code (Recommended)
1. Open Visual Studio Code
2. Go to `File > Open Folder`
3. Open the `sqli-lab` folder that you just created.
4. Open a new terminal inside of VS Code (`Terminal > New Terminal`)

Your terminal should now be opened inside of your `sqli-lab` folder.

## Checkpoint 1
- [ ] I can run `python --version` (Windows) or `python3 --version` (macOS/Linux) and see a version number.
- [ ] I have opened my `sqli-lab` folder in VS Code.
- [ ] I have a terminal open inside of it.

> [!NOTE]
> What is a virtual environment? It's like a clean room for this project. Any packages that we install (like Flask) will only exist for *this* lab, and won't interfere with any other Python projects on your computer!
