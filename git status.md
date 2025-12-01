`git status` 顯示的是**當前分支**的狀態

### - `git status` 顯示的資訊

範例：
```bash
On branch fix/csv-error           # ← 當前所在的分支
Changes not staged for commit:    # ← 有變更但還沒加到暫存區
        deleted:    data/json_analyzer.py  # ← 刪除了這個檔案
```

刪除了 `data/json_analyzer.py`，Git 偵測到這個變更，但還沒有暫存（staged）。那接下來可以做什麼？
```bash
# 選項 1: 確認要刪除，暫存這個變更
git add data/json_analyzer.py
# 或
git rm data/json_analyzer.py

# 選項 2: 取消刪除，恢復檔案
git restore data/json_analyzer.py

# 選項 3: 查看更詳細的差異
git diff data/json_analyzer.py  # 這個可能沒輸出（因為是刪除）
```

### - 完整的狀態分類

`git status` 會顯示三種狀態的檔案：

#### 1. Changes to be committed（已暫存，等待 commit）

```bash
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   file1.py
        new file:   file2.py
```

#### 2. Changes not staged for commit（有變更但未暫存）

```bash
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
        modified:   file3.py
        deleted:    file4.py  # ← 你目前的狀況
```

#### 3. Untracked files（未追蹤的新檔案）

```bash
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        file5.py
```

### - 其他實用的 status 參數

簡短版本：
```bash
git status -s
```

輸出：
```powershell
PS C:\Users\Frank\Documents\CS_SE_vault> git status -s
 M .obsidian/workspace.json
 M "Tools/Git/git 遠端儲存庫 ＆ git push.md"
```

顯示分支資訊：
```bash
git status -sb
```

輸出：
```powershell
PS C:\Users\Frank\Documents\CS_SE_vault> git status -sb
## main...origin/main
 M .obsidian/workspace.json
 M "Tools/Git/git 遠端儲存庫 ＆ git push.md"
```
