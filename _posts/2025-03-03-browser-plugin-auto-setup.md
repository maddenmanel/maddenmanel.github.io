---
title: "Browser Plugin Auto Setup"
mathjax: true
toc: true
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - Broser Plugin
  - tool
---

---

## **📌 Introduction**

If your Chrome extension is **not published on the Chrome Web Store**, users must manually install it. However, Chrome restricts direct `.crx` installations, making the process cumbersome.

This guide shows how to create an **executable (.exe) installer** using **Python and Windows Registry**, allowing users to **install a Chrome extension with a single click**.

---

## **🚀 Solution Overview**

1. **Extract CRX extension files** to a local folder
2. **Modify Windows Registry** to register the extension
3. **Restart Chrome** to enable the extension automatically
4. **Package the script into an EXE** for user-friendly installation

---

## **📌 Python Implementation**

The following Python script performs all the necessary steps to install the Chrome extension.

### **1️⃣ Python Script (**``**)**

```python
import os
import zipfile
import shutil
import winreg
import subprocess

# Plugin ID (Obtain from Chrome after loading it manually once)
EXTENSION_ID = "abcdefghijklmnoabcdefghijklmno"

# Get current script directory
BASE_DIR = os.path.dirname(os.path.abspath(__file__))

# CRX file path
CRX_PATH = os.path.join(BASE_DIR, "my_extension.crx")

# Extracted extension directory
EXTENSION_PATH = os.path.join(BASE_DIR, "chrome_extension")

# **📌 Step 1: Extract CRX File**
def extract_crx():
    if not os.path.exists(EXTENSION_PATH):
        os.makedirs(EXTENSION_PATH)
    with zipfile.ZipFile(CRX_PATH, 'r') as zip_ref:
        zip_ref.extractall(EXTENSION_PATH)
    print(f"Extension extracted to: {EXTENSION_PATH}")

# **📌 Step 2: Modify Windows Registry to Install Extension**
def add_extension_to_registry():
    key_path = fr"Software\\Google\\Chrome\\Extensions\\{EXTENSION_ID}"
    try:
        key = winreg.CreateKey(winreg.HKEY_LOCAL_MACHINE, key_path)
        winreg.SetValueEx(key, "path", 0, winreg.REG_SZ, EXTENSION_PATH)
        winreg.SetValueEx(key, "version", 0, winreg.REG_SZ, "1.0")
        winreg.CloseKey(key)
        print(f"Registry updated: {key_path}")
    except Exception as e:
        print(f"Failed to modify registry: {e}")

# **📌 Step 3: Restart Chrome**
def restart_chrome():
    try:
        print("Closing Chrome...")
        subprocess.call("taskkill /IM chrome.exe /F", shell=True)
        print("Chrome closed.")
    except Exception as e:
        print(f"Failed to close Chrome: {e}")

# **📌 Step 4: Reopen Chrome**
def start_chrome():
    chrome_path = r"C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe"
    if os.path.exists(chrome_path):
        subprocess.Popen([chrome_path], shell=True)
        print("Chrome restarted.")
    else:
        print("Chrome not found. Please open it manually.")

# **🚀 Execute Installation Process**
if __name__ == "__main__":
    extract_crx()
    add_extension_to_registry()
    restart_chrome()
    start_chrome()
    print("Extension installed successfully!")
```

---

## **📌 2️⃣ Convert Python Script to EXE**

To distribute the installer as an `.exe` file, use `PyInstaller`:

```sh
pyinstaller --onefile --noconsole --uac-admin install_plugin.py
```

### **Explanation of Arguments**

- `--onefile` → Creates a single `.exe` file
- `--noconsole` → Hides the console window
- `--uac-admin` → Requests **administrator privileges** (needed for registry modification)

After running the command, the final executable file will be available in the `dist/` directory as `install_plugin.exe`.

---

## **📌 3️⃣ How It Works**

1. The user **runs **``
2. The script **extracts** the CRX to a local directory
3. The **Windows Registry** is updated to register the extension
4. Chrome is **restarted automatically**
5. The extension is **installed and enabled**

✅ **No manual installation required!** ✅ **No need to drag and drop the CRX file!** ✅ **Ideal for enterprise or internal plugin deployment!**

---

## **📌 4️⃣ Final Thoughts**

This method provides an **automated way** to install Chrome extensions without requiring user interaction. By combining **Python, Windows Registry, and PyInstaller**, we achieve a seamless experience for users.

This solution is perfect for **internal tools, enterprise deployments, and custom Chrome extensions** that are not available in the Chrome Web Store.

🚀 **Now, users can install your extension with a single click!** 🚀

---



<script type="module">
  import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
  mermaid.initialize({ startOnLoad: true });
</script>
