### - git status

`git status` 顯示的是**當前分支**的狀態。例如：
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

# 選項 2: 取消刪除檔案的動作，也就是恢復檔案
git restore data/json_analyzer.py

# 選項 3: 查看更詳細的差異
git diff data/json_analyzer.py  # 這個可能沒輸出（因為是刪除）
```

#### - 完整的狀態分類

`git status` 會顯示三種狀態的檔案：

##### 1. Changes to be committed（已暫存，等待 commit）

```bash
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   file1.py
        new file:   file2.py
```

##### 2. Changes not staged for commit（有變更但未暫存）

```bash
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
        modified:   file3.py
        deleted:    file4.py  # ← 你目前的狀況
```

##### 3. Untracked files（未追蹤的新檔案）

```bash
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        file5.py
```

#### - 其他實用的 status 參數

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
### main...origin/main
 M .obsidian/workspace.json
 M "Tools/Git/git 遠端儲存庫 ＆ git push.md"
```

### - git restore

`git restore` **預設**對 **working directory** 做操作，也可以透過參數處理 **staging area**。他的用途是恢復原本的狀態，但他永遠不會動到 commit 歷史。git restore 在處理 merge 產生的衝突是個不錯的選

`git restore` **預設**的恢復來源優先順序：
1. 如果**暫存區有內容** → 從暫存區恢復
2. 如果**暫存區沒有** → 從 HEAD 恢復

`git restore` 的用法：
```bash
# 1. 只處理工作目錄（預設），會丟棄工作目錄的修改
# 也就是說如果檔案已經 add 過然後又再次修改了，這時候對他 restore，只會恢復工作目錄，回到暫存區的狀態（"new"）
git restore <file-name>
git restore --worktree <file-name>

# 2. 只處理暫存區，也就是取消暫存（unstage）
# 檔案回到工作目錄，修改還在
git restore --staged <file-name>

# 3. 兩個都處理
git restore --staged --worktree <file-name>
git restore -SW <file-name> # 簡寫
```

參考：
1. [[git 基礎]]
2. [[git branch ＆ git checkout]]
3. [[git reset 與 git rebase]]

### - 指令快速參考表

| 想做什麼 | 指令 |
|---------|------|
| 只恢復工作目錄的單一檔案 | `git restore file.js` |
| 取消暫存單一檔案 | `git restore --staged file.js` |
| 清空工作目錄+暫存區 | `git restore -SW .` 或 `git reset --hard` |
| 回到上一個 commit（保留修改） | `git reset --soft HEAD~1` |
| 切換分支查看 | `git checkout branch-name` |
| 查看舊版本 | `git checkout commit-hash` |
