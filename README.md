# Windows SID 修改工具自动化

本项目旨在自动化获取 Windows SID 修改工具的试用密钥，并生成相应的批处理脚本。
> 主要解决问题：在企业批量部署Windows系统时，克隆镜像或Ghost装机常导致客户端安全标识符（SID）重复，进而引发域控制器（DC）认证冲突、权限紊乱、组策略应用异常等严重安全问题。

## 工作原理

项目核心是一个 Python 脚本 (`ocr.py`)，它会执行以下步骤：

1.  **下载图片**：从 `https://www.stratesave.com/html/images/sidchgtrial.png` 下载包含试用密钥的图片。
2.  **OCR 识别**：使用光学字符识别（OCR）技术从图片中提取密钥。
3.  **生成批处理脚本**：生成一个名为 `getsid.bat` 的批处理脚本，该脚本使用提取到的密钥来运行 SID 修改工具 (`sidchg64-3.0k.exe`)。

## 使用 GitHub Actions 实现自动化更新使用秘钥

本项目通过 GitHub Actions 实现 `getsid.bat` 脚本的自动更新。工作流程定义在 `.github/workflows/main.yml` 文件中，并执行以下操作：

*   **定时执行**：工作流程每天北京时间上午 8 点自动运行。
*   **环境设置**：设置一个包含 Python 和 Tesseract OCR 的虚拟环境。
*   **安装依赖**：从 `requirements.txt` 文件中安装所需的 Python 库。
*   **执行脚本**：运行 `ocr.py` 脚本以生成新的 `getsid.bat` 文件。
*   **提交并推送**：将更新后的 `getsid.bat` 文件提交到 `main` 分支。

## 如何使用

您只需从本仓库下载 `getsid.bat` 脚本和sidchg64-3.0k.exe即可使用。该脚本每天都会自动更新，以确保您获取到最新的试用密钥。

### 一键运行 (PowerShell)

您可以使用以下 PowerShell 命令来自动下载并运行 SID 修改脚本。请以管理员身份运行 PowerShell：

```powershell
# 创建一个新的目录用于存放所需文件
$dir = "C:\sidchanger"
if (-not (Test-Path $dir)) {
    New-Item -ItemType Directory -Path $dir
}
cd $dir

# 下载 sidchg64-3.0k.exe 和 getsid.bat
Invoke-WebRequest -Uri "https://ghproxy.net/https://raw.githubusercontent.com/zzxym/sidchg/main/sidchg64-3.0k.exe" -OutFile "sidchg64-3.0k.exe"
Invoke-WebRequest -Uri "https://ghproxy.net/https://raw.githubusercontent.com/zzxym/sidchg/main/getsid.bat" -OutFile "getsid.bat"

# 运行批处理脚本
./getsid.bat
```

**注意**：为了使 `getsid.bat` 脚本正常工作，您需要将 `sidchg64-3.0k.exe` 工具与 `getsid.bat` 脚本放置在同一目录下。
