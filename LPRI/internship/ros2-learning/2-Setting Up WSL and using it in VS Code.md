# Setting Up WSL and Using it in VS Code

## Install WSL

1. Open **PowerShell as Administrator**

2. Install WSL

`wsl --install`

3. Restart the computer

4. List available Linux distributions

`wsl --list --online`

5. Install Ubuntu 22.04

`wsl --install -d Ubuntu-22.04`

6. Create your **username and password**

7. Verify the WSL version

`wsl -l -v`

You should see **Version 2**.

---

## Install WSL Extension in VS Code

1. Open **VS Code**
2. Go to **Extensions**
3. Search for **WSL**
4. Install **Remote - WSL**

---

## Open WSL in VS Code

1. Open the Ubuntu application

Windows Key → Ubuntu

2. Inside Ubuntu run

`code .`

This opens the current WSL directory in **VS Code**.
