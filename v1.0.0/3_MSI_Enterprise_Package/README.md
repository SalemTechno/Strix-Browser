# 🏢 Strix Browser v1.0.0 — Enterprise Windows Installer MSI (x64)

- **File:** `StrixBrowser-1.0.0.msi`
- **Type:** Native 64-bit Windows Installer Package (WiX Toolset — Per-Machine Enterprise Deployment)
- **Direct 1-Click Download (GitHub Release v1.0.0):**  
  👉 [Download `StrixBrowser-1.0.0.msi`](https://github.com/SalemTechno/Strix-Browser/releases/download/v1.0.0/StrixBrowser-1.0.0.msi)

### النشر المؤسسي عبر Active Directory GPO / SCCM / Intune
- **تثبيت صامت كامل (Silent GPO / CLI Install):**
  ```cmd
  msiexec /i StrixBrowser-1.0.0.msi /qn /norestart
  ```
- **إلغاء التثبيت الصامت (Silent Uninstall):**
  ```cmd
  msiexec /x StrixBrowser-1.0.0.msi /qn
  ```
