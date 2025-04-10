# fix_wsl2_windows11_build26100plus
After updating my windows 11 in april 2025 it become hell to get my WSL functioning back. This guide helps you resolve persistent issues when trying to install WSL2 on Windows 11 Version 24H2 (Build 26100.3476) or similar builds.

# Fixing WSL2 Installation on Windows 11 24H2

This guide documents the successful troubleshooting steps I followed to install WSL2 and Ubuntu 24.04 on Windows 11 version 24H2 (OS Build 26100.3476), after experiencing multiple issues and errors.

---

## 🖥️ System Info

- **OS**: Windows 11 Pro 24H2 (Build 26100.3476)
- **Goal**: Install WSL2 and Ubuntu 24.04

## 🛠️ Common Errors Encountered

- `wsl_update_x64.msi` fails with: `This update only applies to machines with the Windows Subsystem for Linux`  
- DISM stuck or returns:
  - `Erro: 50` → "Operation completed but feature was not enabled"
  - `Erro: 14107` → "One or more members of the transaction are not present"
- `Add-AppxPackage` fails with HRESULT `0x80073CF0`
- Ubuntu fails to register with `ERROR_MOD_NOT_FOUND`

---

## ✅ Final Working Steps

### 1. Run System File Check
This step **fixed everything**. It restores missing/corrupt system components required by WSL:

```powershell
sfc /scannow
```
- After the scan, **REBOOT** your machine.

### 2. Enable WSL & Virtualization Features

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
dism.exe /online /enable-feature /featurename:HypervisorPlatform /all /norestart
```

Reboot again.

### 3. Verify Feature Activation

```powershell
dism.exe /online /get-features /format:table | findstr /i "Virtual Hyper"
```
Expected output:
```
VirtualMachinePlatform       | Habilitado
HypervisorPlatform           | Habilitado
```

### 4. Install the Latest WSL Manually (Optional if wsl --install fails)
Download the latest `.msixbundle` from [WSL Releases on GitHub](https://github.com/microsoft/WSL/releases).

```powershell
Add-AppxPackage .\Microsoft.WSL_2.4.13.0_x64_ARM64.msixbundle
```

### 5. Install Ubuntu 24.04

```powershell
wsl --install -d Ubuntu-24.04
```
✅ Success message:
```
Installing: Ubuntu 24.04 LTS
Distribution installed successfully. You can start it using 'wsl.exe -d Ubuntu-24.04'
```

---

## ✨ Tips

- Make sure virtualization is enabled in BIOS.
- `sfc /scannow` may resolve hidden system corruption blocking WSL features.
- You can install WSL from the Microsoft Store *only* after required features are fully functional.

---

## 📁 Repo Info
Feel free to fork or contribute. This repo serves as a log of a successful workaround when installing WSL2 on recent insider builds.

---

## 📬 Contact
Made by [JPbio](https://github.com/JPbio) — drop a star if this helped you!

---

_If you encounter new errors, feel free to open an issue so we can collaborate on expanding this guide._


