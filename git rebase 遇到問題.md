### - 合併特定 Commit 的案例

如果要合併兩個特定的 commit（如 66bcc45 和 d446349），互動式 rebase 是比較精確的工具：

```bash
5e7e6ea (HEAD -> main, origin/main) feat: add python venv notes and update folder structure
2058165 feat: add new notes about git and update git log notes
67a5e32 feat: update git notes about some command
52342f8 feat: update web dev folder arrange
d446349 feat: add new notes: Dockerfile, docker build
66bcc45 feat:add new docker notes about dockerfile
ff00e7a feat: add validate notes
64a0801 feat: add new postgresql notes
```

```bash
# 從要修改的範圍的前一個 commit 開始
git rebase -i 66bcc45^

# 在編輯器中將要合併的 commit 改為 squash 或 s
pick d446349 feat: add new notes: Dockerfile, docker build
squash 66bcc45 feat:add new docker notes about dockerfile
```

這裡的 `^` 表示 66bcc45 的父 commit 也就是 ff00e7a，然後起點不算，所以就是從 66bcc45 編輯到終點 HEAD (通常就是目前分支最新的進度)

#### 強制推送的盲區

另外有一個重點，因為 rebase 過後 HASH 值會變，所以說要 push 回去遠端 repo 的時候要小心，當然可以使用 `--force` 直接強推。但如果強制 push 前，團隊其他成員有 push 了，那就是滿嚴重的問題了

建議使用下面這個方法：
```bash
git push --force-with-lease origin main
```

- 因為改變了歷史，需要使用強制推送
- `--force-with-lease` 比純粹的 `--force` 更安全，它會檢查遠端分支在你 pull 之後，是否有其他人推了新的 commit

這時候就可以安心通知團隊其他人員：「我對 main 分支進行了 rebase，你們需要重新拉取」
```bash
git fetch
git reset --hard origin/main
```

參考：
1. [[git 基礎]]
2. [[git 協作]]

### - 遇到的狀況

```powershell
PS C:\Users\Frank\OneDrive\桌面\工作\fhir\fhir_explorer> git rebase origin_gitlab/develop
Auto-merging app/config.py
CONFLICT (content): Merge conflict in app/config.py
Auto-merging app/config.py
CONFLICT (content): Merge conflict in app/config.py
CONFLICT (content): Merge conflict in app/config.py
error: could not apply 25bbb35... feat: 更新顯示文字
hint: Resolve all conflicts manually, mark them as resolved with
hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
hint: You can instead skip this commit: run "git rebase --skip".
hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
hint: Disable this message with "git config advice.mergeConflict false"
Could not apply 25bbb35... feat: 更新顯示文字
```

本地工作目錄中有未提交的變更，而這些變更會被 rebase 過程覆蓋，所以 Git 中止了操作以防止本地變更被覆蓋。根據 Git 提供的提示，有幾個選項可以處理這個情況：

#### 1. **提交目前的本地變更**：

```bash
git add .obsidian/workspace.json
git commit -m "更新 workspace 設定"
```
   
   然後再繼續 rebase 操作

#### 2. **暫存目前的變更**：

```bash
git stash
```
   
   這會將未提交變更暫時儲存起來。完成 rebase 後，可以用 `git stash pop` 恢復這些變更

#### 3. **放棄目前的變更**（如果 `.obsidian/workspace.json` 的變更不重要）：

```bash
git checkout -- .obsidian/workspace.json
```

一旦處理完未提交的變更，便可以繼續 rebase 操作：

1. 如果想要編輯 rebase 的指令：
```bash
git rebase --edit-todo
```
   
   這會打開編輯器讓你修改 rebase 的任務清單

2. 然後繼續 rebase：
```bash
git rebase --continue
```

如果想完全取消這次 rebase 操作，可以使用：
```bash
git rebase --abort
```

這會回到執行 rebase 前的狀態，你可以重新開始

參考：[[git rebase 的衝突合併方向]]