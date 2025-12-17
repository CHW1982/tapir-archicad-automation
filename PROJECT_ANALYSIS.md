# Tapir ArchiCAD Automation - 專案深入分析

## 專案概覽

**專案**: ENZYME-APD/tapir-archicad-automation  
**License**: MIT License (Copyright 2024 Enzyme APD)  
**本地路徑**: `d:\00-BIM\99-Python\tapir-archicad-automation`  
**主要語言**: C++ (Add-On), C# (Grasshopper Plugin)

---

## 核心組件分析

### 1. ArchiCAD Add-On (`archicad-addon/`)

#### 專案特點
- 基於 Tibor Lorantfy 的 [archicad-additional-json-commands](https://github.com/tlorantfy/archicad-additional-json-commands)
- 使用 **CMake** 構建系統
- 跨平台支持：Windows 和 macOS
- 支持 ArchiCAD 25-29

#### 命令類別（Command Categories）

發現的命令模組（共 15 個類別）：

```
Sources/
├── ApplicationCommands.cpp/hpp      # 應用程式相關命令
├── AttributeCommands.cpp/hpp        # 屬性管理命令（51KB - 最大）
├── ClassificationCommands.cpp/hpp   # 分類系統命令
├── ElementCommands.cpp/hpp          # 元素操作命令（81KB - 核心）
├── ElementCreationCommands.cpp/hpp  # 元素創建命令（39KB）
├── ElementGDLParameterCommands.cpp/hpp  # GDL 參數命令
├── FavoritesCommands.cpp/hpp        # 收藏夾命令
├── IssueCommands.cpp/hpp            # 問題管理命令
├── LibraryCommands.cpp/hpp          # 圖庫管理命令
├── NavigatorCommands.cpp/hpp        # 導航器命令
├── ProjectCommands.cpp/hpp          # 專案管理命令（28KB）
├── PropertyCommands.cpp/hpp         # 屬性命令（57KB）
├── RevisionCommands.cpp/hpp         # 版本管理命令
├── TeamworkCommands.cpp/hpp         # 團隊協作命令
└── CommandBase.cpp/hpp              # 命令基類（24KB）
```

#### 代碼規模統計

| 命令類別 | 代碼大小 | 重要性 |
|---------|---------|--------|
| ElementCommands | 81KB | 🔴 核心 |
| PropertyCommands | 57KB | 🔴 核心 |
| AttributeCommands | 51KB | 🔴 核心 |
| ElementCreationCommands | 39KB | 🟡 重要 |
| TapirPalette (UI) | 32KB | 🟡 重要 |
| ProjectCommands | 28KB | 🟡 重要 |
| IssueCommands | 27KB | 🟢 擴展 |
| CommandBase | 24KB | 🔴 基礎 |

#### 關鍵文件說明

**AddOnMain.cpp** (23KB)
- Add-On 的入口點
- 註冊所有 Tapir 命令到 ArchiCAD
- 處理生命週期管理

**CommandBase.hpp/cpp**
- 所有命令的基類
- 提供通用的 JSON 序列化/反序列化
- 錯誤處理框架

**TapirPalette.cpp/hpp** (32KB)
- 提供圖形用戶介面
- 命令面板和工具列
- 與 ArchiCAD UI 集成

#### 輔助組件

- **Config.cpp/hpp** - 配置管理
- **VersionChecker.cpp/hpp** - 版本檢查
- **MigrationHelper.hpp** - 版本遷移輔助
- **SchemaDefinitions.cpp/hpp** - JSON Schema 定義
- **UvManager.cpp/hpp** - UV 映射管理

---

### 2. Grasshopper Plugin (`grasshopper-plugin/`)

為 Rhino/Grasshopper 用戶提供視覺化節點介面。

**目標用戶**: 建築師、設計師（非程式設計師）

---

### 3. 內置腳本 (`builtin-scripts/`)

預定義的自動化腳本範例。

---

### 4. 開發工具 (`tools/`)

構建和開發輔助工具。

---

## 技術架構

### 命令註冊機制

```cpp
// 在 AddOnMain.cpp 中
// 每個命令註冊到 TapirCommand 命名空間
ExecuteAddOnCommand(
    AddOnCommandId("TapirCommand", "GetAllElements"),
    parameters
)
```

### JSON 通訊協議

所有命令使用 JSON 格式：
```json
{
  "command": "CommandName",
  "parameters": { ... },
  "result": { ... }
}
```

### 命令繼承結構

```
CommandBase (基類)
    ├── ApplicationCommands
    ├── ElementCommands
    ├── PropertyCommands
    ├── ...
    └── TeamworkCommands
```

---

## 與 tapir-archicad-MCP 的集成

### 數據流

```
AI Agent (Claude/Gemini)
    ↓ MCP Protocol
tapir-archicad-MCP Server
    ↓ Python API (multiconn-archicad)
Tapir Add-On (本專案 C++)
    ↓ JSON Commands
ArchiCAD Application
```

### tapir-archicad-MCP 如何使用 Tapir

1. **工具生成器** (`scripts/generate_tools.py`)
   - 從 `multiconn-archicad` 獲取命令 schema
   - 自動生成 Python 包裝函數
   
2. **命令調用**
   ```python
   # multiconn-archicad 調用 Tapir 命令
   conn.core.post_tapir_command('GetAllElements', params)
   ```

3. **MCP 工具**
   - 每個 Tapir 命令 → 一個 MCP 工具
   - 語義搜索幫助 AI 發現正確的命令

---

## 重要發現

### ✅ 優勢

1. **模組化設計** - 命令按功能分類，易於維護
2. **完整的 API 覆蓋** - 15 個命令類別涵蓋 ArchiCAD 主要功能
3. **類型安全** - 使用 C++ 強類型系統
4. **跨平台** - Windows 和 macOS 支持
5. **開源社群** - 活躍的社群和文檔

### 📊 代碼品質指標

- **總文件數**: 50+ 源文件
- **命令類別**: 15 個
- **核心代碼**: ~400KB C++ 源代碼
- **測試覆蓋**: 有專門的 `Test/` 目錄
- **範例代碼**: `Examples/` 提供使用範例

### 🔧 構建系統

- **CMake** 3.x+
- **編譯器要求**:
  - Windows: Visual Studio 2019+
  - macOS: Xcode
- **依賴**: ArchiCAD API DevKit

---

## 開發工作流程

### 查看命令實現

```bash
cd d:\00-BIM\99-Python\tapir-archicad-automation\archicad-addon\Sources

# 查看元素相關命令
code ElementCommands.cpp

# 查看屬性相關命令
code PropertyCommands.cpp
```

### 測試和範例

```bash
# 查看測試
cd Test/

# 查看使用範例
cd Examples/
```

### 構建 Add-On（進階）

```bash
cd archicad-addon
mkdir build && cd build
cmake ..
cmake --build . --config Release
```

---

## 與 tapir-archicad-MCP 的對應關係

### MCP 工具來源映射

| Tapir 源文件 | MCP 工具類別 | 數量估算 |
|-------------|------------|---------|
| ElementCommands.cpp | elements_* | ~20 個工具 |
| PropertyCommands.cpp | properties_* | ~15 個工具 |
| AttributeCommands.cpp | attributes_* | ~12 個工具 |
| ProjectCommands.cpp | project_* | ~8 個工具 |
| NavigatorCommands.cpp | navigator_* | ~6 個工具 |
| ... | ... | ... |
| **總計** | **all tools** | **80+ 工具** |

### 關鍵命令範例

從源代碼可以看到的重要命令：

**ElementCommands.cpp**:
- `GetAllElements` - 獲取所有元素
- `GetElementsByType` - 按類型獲取元素
- `GetSelectedElements` - 獲取選中的元素
- `HighlightElements` - 高亮顯示元素

**PropertyCommands.cpp**:
- `GetPropertyValuesOfElements` - 獲取元素屬性值
- `SetPropertyValuesOfElements` - 設置元素屬性值
- `GetAllProperties` - 獲取所有屬性

**ProjectCommands.cpp**:
- `GetProjectInfo` - 獲取專案資訊（MCP 必需）
- `GetArchicadLocation` - 獲取 ArchiCAD 位置（MCP 必需）

---

## 學習建議

### 初學者路徑

1. **閱讀 README**
   - `README.md` - 總覽
   - `archicad-addon/README.md` - Add-On 開發

2. **瀏覽範例**
   - `archicad-addon/Examples/` - 實際使用範例

3. **理解命令結構**
   - `CommandBase.hpp` - 了解基礎架構
   - 選一個簡單的命令類（如 `ApplicationCommands.cpp`）

### 進階開發者路徑

1. **深入核心命令**
   - `ElementCommands.cpp` - 最複雜的命令實現
   - `PropertyCommands.cpp` - 屬性系統

2. **UI 集成**
   - `TapirPalette.cpp` - 學習如何創建 ArchiCAD 面板

3. **構建和測試**
   - 設置完整的開發環境
   - 嘗試修改和編譯

---

## 相關文檔連結

- **開發者 Wiki**: https://github.com/ENZYME-APD/tapir-archicad-automation/wiki/Archicad-Add-On-Development
- **API 文檔**: https://enzyme-apd.github.io/tapir-archicad-automation/archicad-addon
- **Discord**: https://discord.gg/NAnSennmpY
- **原始靈感**: https://github.com/tlorantfy/archicad-additional-json-commands

---

## 下一步建議

### 對於 tapir-archicad-MCP 開發

1. ✅ **已完成**: 理解 Tapir Add-On 架構
2. 🔄 **進行中**: 查看 MCP 專案的 ISSUES_AND_FIXES.md
3. 📋 **待辦**: 開始實施修復計劃
   - 優先修復 Issue #10 (README 說明 Tapir 依賴)
   - 建立測試基礎設施 (Issue #1)
   - 創建開發者文檔 (Issue #7)

### 對於深入學習 Tapir

1. 📖 閱讀核心命令實現
2. 🔧 嘗試修改和擴展命令
3. 🤝 參與社群討論和貢獻

---

**文檔創建時間**: 2025-12-09  
**專案版本**: Latest from main branch
