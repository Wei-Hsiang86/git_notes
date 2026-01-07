submodule 是 git 原生提供的功能，用於將一個 Git 儲存庫作為另一個 Git 儲存庫的子目錄。這可以在專案中引用外部儲存庫，同時保持各自的提交歷史分離

### - 什麼是 Submodule

- 將外部儲存庫嵌入到主專案的特定路徑
- 追蹤外部儲存庫的特定 commit（非常靜態）
- 保持主專案和子專案的提交歷史分離
- 實現程式重用和依賴管理

#### 非常靜態

非常靜態指的是，**Submodule 只會指向一個固定的 commit hash**，不會自動跟隨分支或標籤的移動

具體來說：
```bash
# 當添加 submodule 時
git submodule add https://github.com/user/library.git

# Git 會記錄當下那個 commit 的 hash，比如：
# commit: a1b2c3d4e5f6...
```
在主專案中，Git 儲存的是這個確切的 commit hash，**不是分支名稱**

```bash
# 如果外部儲存庫的 main 分支繼續發展：
外部庫 main: a1b2c3d → e2f3g4h → i5j6k7l (最新)
你的 submodule: 還是指向 a1b2c3d （靜態的）
```

為什麼說「非常靜態」？
1. **不會自動更新**：即使外部儲存庫有新 commit，你的 submodule 還是指向舊的
2. **需要手動更新**：要執行 `git submodule update --remote` 才會更新
3. **版本鎖定**：這確保了構建的一致性和穩定性

就像是：
- **Submodule**：指向圖書館的「特定版本書籍」（比如第3版）
- **一般依賴**：指向圖書館的「最新版書籍」（可能隨時換成第4版、第5版）

實際範例：
```bash
# 主專案的 .gitmodules
[submodule "lib"]
    path = lib
    url = https://github.com/user/library.git

# 但實際上 Git 追蹤的是：
# lib/ -> commit abc123def456 (固定不變)
```

這個「靜態」特性既是優點（穩定、可重複構建）也是缺點（需要手動管理更新）

### - Submodule vs Subtree 比較

| 特性    | Submodule | Subtree    |
| ----- | --------- | ---------- |
| 學習成本  | 較高，需要特殊命令 | 較低，對團隊透明   |
| 儲存庫大小 | 較小（只儲存指標） | 較大（包含完整歷史） |
| 網路依賴  | 需要存取遠端儲存庫 | 不需要        |
| 管理複雜度 | 較複雜       | 較簡單        |
| 適用場景  | 元件化開發     | 系統化開發      |

參考：[[git subtree]]

### - 基本操作

#### 添加 submodule

```bash
# 基本語法
git submodule add <repository-url> [path]

# 範例：添加到預設路徑（與儲存庫同名）
git submodule add https://github.com/username/awesome-lib.git

# 範例：添加到指定路徑
git submodule add https://github.com/username/awesome-lib.git lib/awesome

# 添加特定分支
git submodule add -b develop https://github.com/username/awesome-lib.git
```

執行後會：
1. 建立 `.gitmodules` 檔案（如果不存在）
2. 將 submodule 資訊加入 `.gitmodules`
3. clone 外部儲存庫到指定路徑
4. 將 submodule 的當前 commit hash 記錄到主專案

#### 初始化和更新 submodule

```bash
# 初始化本地設定檔
git submodule init

# 抓取 submodule 的內容
git submodule update

# 一步完成初始化和更新
git submodule update --init

# 遞迴處理巢狀 submodule
git submodule update --init --recursive
```

#### clone 包含 submodule 的專案

```bash
# 方式一：clone 後手動初始化
git clone <main-repo-url>
cd <main-repo>
git submodule update --init --recursive

# 方式二：clone 時自動處理 submodule
git clone --recurse-submodules <main-repo-url>
```

### - 日常工作流程

#### 更新 submodule 到最新版本

```bash
# 進入 submodule 目錄
cd path/to/submodule

# 切換到想要的分支/commit
git checkout main
git pull origin main

# 回到主專案目錄
cd ..

# 提交 submodule 的更新
git add path/to/submodule
git commit -m "update submodule to latest version"
```

#### 批次更新所有 submodule

```bash
# 更新所有 submodule 到遠端的最新版本
git submodule update --remote

# 更新特定 submodule
git submodule update --remote path/to/submodule
```

#### 檢查 submodule 狀態

```bash
# 查看 submodule 狀態
git submodule status

# 查看詳細資訊
git submodule foreach git status

# 查看 submodule 的變更
git diff --submodule
```

### - 進階操作

#### 移除 submodule

移除 submodule 需要幾個步驟：

```bash
# 1. 取消註冊 submodule
git submodule deinit path/to/submodule

# 2. 從 git 中移除路徑
git rm path/to/submodule

# 3. 提交變更
git commit -m "remove submodule"

# 4. 清理 .git 目錄（可選）
rm -rf .git/modules/path/to/submodule
```

#### 修改 submodule URL

```bash
# 方式一：編輯 .gitmodules 檔案後同步
git submodule set-url path/to/submodule <new-url>
git submodule sync

# 方式二：手動編輯 .gitmodules 檔案
# 然後執行
git submodule sync
```

#### 在 submodule 中開發

```bash
# 進入 submodule 目錄
cd path/to/submodule

# 確保在正確的分支上（submodule 預設是 detached HEAD）
git checkout main

# 進行開發工作
echo "new feature" > feature.txt
git add feature.txt
git commit -m "add new feature"

# 推送到 submodule 的遠端儲存庫
git push origin main

# 回到主專案目錄並更新記錄
cd ..
git add path/to/submodule
git commit -m "update submodule with new feature"
```

### - .gitmodules 檔案

`.gitmodules` 檔案範例：

```ini
[submodule "lib/awesome"]
    path = lib/awesome
    url = https://github.com/username/awesome-lib.git
    branch = main

[submodule "vendor/utils"]
    path = vendor/utils
    url = https://github.com/company/utils.git
    branch = develop
```

重要特性：
- 版本控制的設定檔（會被提交）
- 記錄 submodule 的路徑、URL 和分支資訊
- 團隊成員共享的 submodule 設定

### - 常見問題與解決方案

#### 問題：submodule 目錄為空

```bash
# 確保已初始化和更新
git submodule update --init --recursive
```

#### 問題：submodule 顯示為 "dirty"

這通常是因為 submodule 處於 detached HEAD 狀態：

```bash
cd path/to/submodule
git checkout main
cd ..
git add path/to/submodule
git commit -m "fix submodule HEAD"
```

#### 問題：多人協作中的 submodule 同步

建議工作流程：
1. 團隊約定 submodule 更新策略
2. 定期執行 `git submodule update --remote`
3. 在 pull 主專案後檢查 submodule 是否需要更新

### - 最佳實踐

####  適用場景
- ✅ 需要追蹤特定版本的外部依賴
- ✅ 多個專案共享同一個函式庫
- ✅ 需要對外部程式碼進行客製化修改
- ✅ 元件化開發架構

#### 注意事項
- 🚨 submodule 預設處於 detached HEAD 狀態
- 🚨 需要額外的命令來初始化和更新
- 🚨 團隊成員需要理解 submodule 的運作方式
- 🚨 在 submodule 中開發需要特別小心分支管理

#### 替代方案考慮
在以下情況建議考慮其他方案：
- 如果語言有內建的套件管理系統（如 npm、pip、gem）
- 如果不需要追蹤特定版本
- 如果團隊對 Git 不夠熟悉
- 如果需要頻繁修改外部依賴

### - 實用指令速查

```bash
# 添加 submodule
git submodule add <url> [path]

# 初始化並更新
git submodule update --init --recursive

# 克隆包含 submodule 的專案
git clone --recurse-submodules <url>

# 更新所有 submodule
git submodule update --remote

# 查看狀態
git submodule status

# 移除 submodule
git submodule deinit <path>
git rm <path>

# 在所有 submodule 中執行命令
git submodule foreach '<command>'
```

---

**總結**：Submodule 是強大的依賴管理工具，適合需要精確版本控制的場景。雖然學習成本較高，但在合適的使用場景下能提供很好的專案組織和依賴管理能力