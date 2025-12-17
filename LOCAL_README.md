# Tapir ArchiCAD Automation - 本地副本說明

## 專案來源

- **GitHub Repository**: [ENZYME-APD/tapir-archicad-automation](https://github.com/ENZYME-APD/tapir-archicad-automation)
- **License**: MIT License
- **本地路徑**: `d:\00-BIM\99-Python\tapir-archicad-automation`
- **複製時間**: 2025-12-09

## 專案結構

```
tapir-archicad-automation/
│
├── archicad-addon/          # ArchiCAD Add-On 源碼（C++）
│   ├── Sources/             # 核心實現代碼
│   ├── Examples/            # 範例程式碼
│   ├── Test/                # 測試代碼
│   ├── Tools/               # 構建工具
│   └── CMakeLists.txt       # CMake 構建配置
│
├── grasshopper-plugin/      # Grasshopper 插件（C#）
│
├── builtin-scripts/         # 內置腳本
│
├── docs/                    # 文檔
│
├── branding/                # 品牌資源（圖片、圖表等）
│   └── diagrams/            # 架構圖等
│
├── sandbox/                 # 沙盒測試環境
│
├── tools/                   # 開發工具
│
├── .github/                 # GitHub Actions 配置
│
├── LICENSE                  # MIT 許可證
└── README.md                # 專案說明
```

## 主要組件

### 1. ArchiCAD Add-On (`archicad-addon/`)

這是核心組件，使用 **C++** 開發的 ArchiCAD Add-On。

**功能**：
- 在 `TapirCommand` 命名空間下註冊額外的 JSON 命令
- 擴展官方 ArchiCAD API 的功能
- 提供社群開發的強化命令

**構建技術**：
- 使用 CMake 構建系統
- 支持 Windows 和 macOS
- 支持 ArchiCAD 25-29 版本

### 2. Grasshopper Plugin (`grasshopper-plugin/`)

為非程式設計師提供的 Grasshopper 視覺化編程插件。

**功能**：
- 視覺化節點介面
- 無需編寫代碼即可使用 Tapir 命令
- 與 Rhino/Grasshopper 整合

### 3. 內置腳本 (`builtin-scripts/`)

預定義的自動化腳本範例。

## 與 tapir-archicad-MCP 的關係

```
tapir-archicad-MCP (Python MCP Server)
    ↓ 使用
multiconn-archicad (Python Library)
    ↓ 調用
Tapir Add-On (本專案 - C++ Add-On) + Official ArchiCAD API
    ↓ 安裝於
ArchiCAD Application
```

**關鍵依賴**：
- `tapir-archicad-MCP` **必須**依賴本專案編譯的 Add-On
- 沒有 Tapir Add-On，MCP 伺服器的大部分功能將無法使用

## 開發文檔

- **API 命令列表**: https://enzyme-apd.github.io/tapir-archicad-automation/archicad-addon
- **GitHub Wiki**: https://github.com/ENZYME-APD/tapir-archicad-automation/wiki
- **Discord 社群**: https://discord.gg/NAnSennmpY

## 如何使用本副本

### 查看源代碼
```bash
cd d:\00-BIM\99-Python\tapir-archicad-automation
```

### 了解 Add-On 實現
主要源代碼位於：
```
archicad-addon/Sources/
```

### 查看範例
```
archicad-addon/Examples/
```

### 構建 Add-On（進階）
需要：
- CMake 3.x+
- Visual Studio 2019+ (Windows) 或 Xcode (macOS)
- ArchiCAD API DevKit

```bash
cd archicad-addon
mkdir build && cd build
cmake ..
cmake --build .
```

## 注意事項

1. **這是一個參考副本** - 用於學習和了解 Tapir Add-On 的實現
2. **不建議直接修改** - 除非您計劃貢獻回上游專案
3. **保持同步** - 定期使用 `git pull` 獲取最新更新
4. **安裝預編譯版本** - 一般用戶應該從 GitHub Releases 下載預編譯的 .apx 文件

## 更新本地副本

```bash
cd d:\00-BIM\99-Python\tapir-archicad-automation
git pull origin main
```

## 相關資源

- **原始專案**: https://github.com/ENZYME-APD/tapir-archicad-automation
- **本地 MCP 專案**: `d:\00-BIM\99-Python\tapir-archicad-MCP`
- **MCP 問題分析**: `d:\00-BIM\99-Python\tapir-archicad-MCP\ISSUES_AND_FIXES.md`
