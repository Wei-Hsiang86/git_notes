subtree 是 git 原生提供的功能，不需要安裝任何額外的外掛或擴充。它是 git 的一個內建子命令，主要用於管理多個專案之間的關係，特別適合你這種需要在一個倉庫中管理另一個倉庫內容的情況

git subtree 大約在 git 1.7.11 版本就被加入了，所以在現代的 git 版本中都可以直接使用。它的完整命令格式是：
```bash
git subtree add|merge|pull|push [--prefix=<prefix>] [<repository> [<ref>]]
```

subtree 相比 submodule 的優點是：
- 不需要團隊其他成員學習新的命令，對他們來說仍然是普通的 git 操作
- 子專案的歷史記錄直接整合到主專案中
- 不會產生 .gitmodules 文件及額外的管理負擔
- 即使沒有網路連接，也能查看和修改子專案的內容

可以通過以下命令來確認你的 git 是否支援 subtree：
```bash
git subtree -h
```
### - 實際操作 (split 方法)

```
主倉庫    →  split 分離   →  推送到遠端 repo
↓               ↓             ↓
完整歷史  →  子資料夾歷史  →   團隊 repo
```

你需要在主資料夾（已經使用 git init 初始化的那個筆記資料夾）中操作，而不是在子資料夾中。git subtree 命令是從主倉庫的角度來管理子資料夾的。具體步驟如下：

1. 確保在主筆記資料夾（已經 git init 的那個）的根目錄：
```bash
cd 主筆記資料夾路徑
```

2. 使用 subtree split 將子資料夾的歷史分離出來：
```bash
git subtree split --prefix=子資料夾名稱 -b split-branch
```

這會 (split) 建立一個新的分支，只包含該子資料夾的歷史記錄。這個主要是在做本地分支的推進（更新），他的作用是：
- 分析主分支的歷史記錄，找出所有與指定子資料夾相關的變更
- 創建一個新的提交序列，只包含該子資料夾的變更歷史
- 將這個乾淨的提交序列儲存到 `split-branch` 分支上
- 如果分支**已存在則會更新它**，這就是「本地分支的推進」

這個操作完全在本地進行，不與任何遠端儲存庫互動。它會準備好一個乾淨的分支，而這個分支只包含子資料夾相關的歷史，方便後續推送到遠端

3. 添加 GitLab 的遠端倉庫：
```bash
git remote add gitlab https://gitlab.com/team/project.git
```

4. 將分離出來的分支推送到 GitLab：
```bash
git push gitlab split-branch-name:main
```

所以整個工作流程是：
1. 使用 `git subtree split` 更新或創建 split-branch
2. 使用 `git push` 將這個分支推送到遠端

這樣就可以：
- 正常維護個人的筆記
- 只將特定子資料夾同步到團隊的 GitLab

🪄 再次提醒所有操作都是在主資料夾中進行的，**不需要**到子資料夾中操作。另外可以透過這個指令查看所有分支目前的推送情況：
```bash
PS C:\Users\Frank\文件\CS_SE_vault> git branch -vv
* main         64a0801 [origin/main] feat: add new postgresql notes
  split-branch c052671 feat: update fhir validator
```

常見問題：
Q：如果子資料夾在主倉庫中有修改，如何同步到遠端？
A：重新執行 `git subtree split` 和 `git push` 即可，Git 會自動處理增量更新

#### 輸出
```powershell
PS C:\Users\Frank\Documents\CS_SE_vault> git subtree split --prefix=FHIR -b fhirtogitlab
66/89 (65) [0]

66/89 (65) [0]
│  │   │   └── 錯誤或衝突計數 (可能是)
│  │   └────── 當前處理的 commit 編號
│  └────────── 總共需要檢查的 commit 數
└───────────── 已檢查的 commit 數

# 驗證總 commit 數
git rev-list --count HEAD

# 查看所有分支狀況
git branch -vv

# 檢查遠端同步狀態
git ls-remote gitlab main
```
- **歷史遍歷**：Git 正在檢查主分支的 89 個 commit
- **路徑過濾**：判斷每個 commit 是否包含對 `FHIR` 資料夾的變更
- **分支構建**：將相關的 commit 重新組織成新的提交序列
- **增量判斷**：比較與現有 `fhirtogitlab` 分支的差異

#### 進階用法：--rejoin 參數
```bash
git subtree split --prefix=FHIR --rejoin -b fhir-split
```
注意：使用 --rejoin 會在主分支產生額外的 merge commit

`--rejoin` 的作用：
- 在主分支中創建一個 merge commit，記錄這次 split 的操作
- 幫助 Git 更好地追蹤 subtree 的歷史關係
- 提升後續 split 操作的效能（Git 知道從哪裡開始增量處理）
- 適合需要頻繁同步的情況
### - 直接 push 的方法

也可以直接使用 `git subtree push` 一步完成，跳過 split-branch 的創建和更新：
```bash
git subtree push --prefix=子資料夾名稱 <local 端的 remote name> <remote 端的支線名稱>
```

`git subtree push`會在內部創建暫時分支，當執行 `git subtree push --prefix=fhir gitlab main` 時：

1. **Git 會檢查上次的 split 記錄**
   - Git 會在 `.git/subtree-cache` 中尋找之前的 split 記錄
   - 如果找到匹配的 subtree 配置，會嘗試重用之前的 commit hash

2. **增量處理**
   - 只會為「新增的」變更創建新的 commit
   - 不會重新生成整個歷史線

3. **暫時分支自動清理**
   - 推送完成後自動刪除暫時分支
   - 不會在本地留下多餘的分支