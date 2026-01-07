### - Orphan Branch（孤立分支）說明

Orphan branch 是在同一個 Git repository 中建立**完全獨立的歷史起點**，與現有的所有分支沒有共同祖先

```
一般分支（有共同祖先）:
main:    A → B → C
feature: A → B → C → D → E
         └─────共同祖先─────┘

Orphan 分支（無共同祖先）:
main:     A → B → C
gh-pages: X → Y → Z
          (完全獨立，無連接點)
```

### - 建立方式

```bash
# 建立 orphan branch
git checkout --orphan new-branch

# 此時：
# - 處於新分支，但還沒有任何 commit
# - 工作目錄會保留之前的檔案（在 staging 狀態）
# - 通常會清空工作目錄重新開始

# 清空工作目錄（常見做法）
git rm -rf .

# 加入新檔案
echo "new content" > file.txt
git add file.txt
git commit -m "Initial commit"

# Push 到遠端（與一般分支相同）
git push origin new-branch
```

### - 特徵

1. **完全獨立的歷史**：與其他分支沒有任何共同祖先
2. **有分支名稱**：不會被垃圾回收機制清除
3. **正常 push**：可以像一般分支一樣推送到遠端
4. **遠端呈現**：在遠端會看到多個獨立的分支樹

### - 實際應用場景

#### 1. GitHub Pages（最常見）

```bash
# 主分支：原始碼
main/
├── src/
├── public/
├── package.json
└── README.md

# 部署流程
npm run build  # 產生編譯後的檔案

# 建立 gh-pages 分支
git checkout --orphan gh-pages
git rm -rf .

# 只放編譯結果
cp -r build/* .
git add .
git commit -m "Deploy site"
git push origin gh-pages

# gh-pages 分支：靜態網頁檔案
gh-pages/
├── index.html
├── static/
│   ├── css/
│   └── js/
└── asset-manifest.json
```

**為什麼用 orphan？**

- 原始碼和編譯結果不需要共存在同一個歷史
- 保持部署分支乾淨，只追蹤網頁檔案變更
- GitHub Pages 只需要靜態檔案，不需要 source code

#### 2. 文件專用分支

```bash
# 主分支：程式碼
main: 程式碼的 commit 歷史

# docs 分支：獨立的文件系統
docs: 文件的 commit 歷史（與程式碼完全分離）
```

#### 3. 專案重寫

```bash
# 舊專案的歷史
main: A → B → C → D (舊架構)

# 全新重寫，不想保留舊歷史
v2: X → Y → Z (新架構，從零開始)
```

### - 與其他概念的差異

|概念|定義|有分支名|會被清除|
|---|---|---|---|
|**Orphan Branch**|獨立的歷史起點|✓|✗|
|**Dangling Commits**|沒有 reference 指向的 commit|✗|✓|
|**Detached HEAD**|HEAD 直接指向 commit（狀態）|✗|✗|

```
Orphan Branch:
  有名字 → 安全 → 不會被清除
  * X (gh-pages)
  * Y
  * Z

Dangling Commits:
  沒有名字 → 危險 → 會被清除
  * C (無 reference)
  * B (無 reference)

Detached HEAD:
  臨時狀態 → 離開後可能變 dangling
  * C (HEAD) ← 只是你在這裡
```

### - 與 Release 分支的差異

**Release 分支**（正常開發流程）：

- 內容：原始碼
- 關係：有共同祖先
- 用途：準備發布、最後測試
- 歷史：需要追溯原始碼變更

**Orphan Branch**（特殊用途）：

- 內容：通常是編譯產物或獨立內容
- 關係：完全獨立
- 用途：託管靜態網站或獨立文件
- 歷史：只需要追溯部署/文件記錄

```bash
# 正常發布流程（用 release 分支）
main → develop → release/v1.0 → tag v1.0.0

# GitHub Pages 部署（用 orphan branch）
main: 原始碼開發
gh-pages: 網頁檔案（與 main 獨立）
```

### - 何時使用 Orphan Branch？

**需要使用的情境**：

1. 部署到 GitHub Pages，且不想混合原始碼和編譯結果
2. 建立完全獨立的文件系統
3. 專案重寫，不想保留舊歷史

**不需要使用的情境**（大部分情況）：

- 正常的版本發布 → 用 release 分支
- 部署到 Vercel/Netlify → 平台自動處理
- 一般的功能開發 → 用 feature 分支

### - 注意事項

1. **不是必須功能**：大部分專案不需要用到 orphan branch
2. **清空工作目錄**：建立 orphan 後通常會 `git rm -rf .` 重新開始
3. **Push 方式相同**：與一般分支完全一樣，Git 會自動處理
4. **歷史獨立**：無法從其他分支 merge 或 rebase

參考：

1. [[git 清除機制]]
2. [[關於 detached HEAD (斷頭) 狀態]]