`git stash` 是一個非常實用的 git 命令，它可以暫時儲存你尚未提交的修改，讓你的工作目錄回到乾淨的狀態。stash 是**全域的**，不會因為你切換 branch 就消失，但雖然是全域的，但套用時要小心：
- 如果 stash 的變更與目前 branch 的檔案有衝突，會**產生 merge conflict**
- 分支間的差異越大，產生衝突的機率越高
- 建議套用前先檢查變更內容，評估衝突風險

Stash 會自動處理不同狀態的檔案
```bash
# 工作目錄中的檔案狀態：
未追蹤檔案 (untracked)     → 預設不會被 stash
已修改檔案 (modified)      → 會被 stash
已暫存檔案 (staged)        → 會被 stash
```

以下是 `git stash` 的主要用法和相關命令
### - 基本用法

1. 儲存修改
```bash
git stash

# 加上訊息
git stash save "你的描述訊息"                 # 舊版語法
git stash push -m "修復登入功能的臨時變更"     # 新版語法
```
這會將未提交的修改（包括暫存和未暫存的已追蹤檔案）保存起來，並將已追蹤檔案恢復到上次提交的狀態。**注意：如同上面說的，未追蹤檔案會保留在工作目錄中**，如需一併處理請使用 `git stash -u`，或是先 add 再 stash

已經加入 stash list 的名稱無法改變，但可以透過取出 stash 再次命名加入來調整，但有點多此一舉 XD

2. 查看 stash 列表
```bash
git stash list
```
顯示所有已儲存的 stash，格式為 `stash@{n}: ...`

3. 應用 stash
```bash
# 應用最近的 stash，但不從 stash 列表中移除
git stash apply

# 應用特定的 stash
git stash apply stash@{2}
```

4. 應用並刪除 stash，但當有衝突的時候一樣不會刪除 stash
```bash
# 應用最近的 stash 並將其從列表中移除
git stash pop

# 應用特定的 stash 並移除
git stash pop stash@{2}
```

5. 刪除 stash
```bash
# 刪除特定的 stash
git stash drop stash@{1}

# 刪除所有 stash
git stash clear
```
### - 進階用法

1. 只儲存特定檔案
```bash
git stash push -m "訊息" path/to/file1 path/to/file2
```

2. 儲存==未追蹤==的檔案
```bash
git stash -u
# 或
git stash --include-untracked
```

3. 查看 stash 的內容
```bash
# 顯示 stash 的差異
git stash show

# 查看檔案列表
git stash show --name-only

# 顯示完整的差異
git stash show -p

# 顯示特定 stash 的差異
git stash show -p stash@{1}
```

4. 從 stash 創建分支
```bash
git stash branch new-branch-name stash@{1}
```
這會基於你進行 stash 時的提交創建一個新分支，應用 stash 的變更，並在成功時刪除該 stash
### - 重要注意事項

1. 當 `git stash pop` 遇到衝突時：
- stash 不會被刪除（與 apply 行為相同）
- 需要手動解決衝突後，再手動刪除 stash
- 建議先用 `git stash apply` 測試，確認無衝突再用 `pop`

2. 在 PowerShell 中使用 stash 編號時需要加引號：
```bash
# 正確語法
git stash show "stash@{1}"
git stash apply "stash@{1}"

# 錯誤語法（會產生解析錯誤）
git stash show stash@{1}
```

3. Stash 保存機制
- Stash **不會自動過期**，會永久保存直到手動刪除，因為 Stash 被儲存為特殊的 Git references（`refs/stash`），Git 認為它們是「有用的」，不會自動清除，所以不受 Git 的自動垃圾回收影響
- Stash 算是一種記錄了，所以還原的時候 gitignore 會失效
- 跨分支全域可用，但套用時注意檔案衝突

4. Stash 編號系統
- stash@{n} 是相對順序
- 編號會動態調整
```bash
stash@{0}  # 最新的 stash
stash@{1}  # 第二新的 stash
stash@{2}  # 第三新的 stash

# 初始狀態
stash@{0}: WIP on main: abc123 latest work
stash@{1}: WIP on feature: def456 older work

# 刪除 stash@{0} 後
git stash drop "stash@{0}"

# 原本的 stash@{1} 自動變成 stash@{0}
stash@{0}: WIP on feature: def456 older work
```
### - 常見使用場景

1. 快速切換任務：當你正在進行一項工作，突然需要切換到另一項緊急任務時
2. 暫存修改以拉取更新：當你有本地修改，但需要先 `git pull` 更新時
3. 保存實驗性修改：當你想嘗試一些可能不會保留的修改時
4. 解決合併衝突前保存工作：在處理複雜的合併操作前保存當前工作

`git stash` 是維持工作流程靈活性的重要工具，尤其在需要頻繁切換分支或任務的開發環境中。使用情境舉例：
```
1. 檢查 stash 內容：`git stash show -p "stash@{n}"`
2. 先用 apply 測試：`git stash apply "stash@{n}"`  
3. 確認無問題後刪除：`git stash drop "stash@{n}"`
4. 避免直接用 pop，除非確定不會有衝突
```

