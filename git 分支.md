HEAD 是指「現在目前分支的最新 commit 端點」，但可能不會是實際上最新的，因為遠端已經更新或是其他原因等等。git branch 的本質：

當你執行 `git branch new-branch-name` 時：
1. **Git 會創建一個新的分支指標**，指向當前 HEAD 所在的 commit
2. **不是真的「複製」**，而是創建一個新的**參考點**（reference）
3. **賦予名字**：`new-branch-name` 就是這個新分支的名稱

更精確的說法：
- Git 分支實際上就是一個**指向特定 commit 的指標**
- 當你創建新分支時，Git 只是創建了一個新的指標，指向相同的 commit
- 所有的檔案、歷史記錄都是共享的，直到在新分支上做出修改

所以 `new-branch-name` 就是這個新分支的識別名稱，就像是「賦予名字」這般。與其說是「複製」，不如說是創建了一個新的**命名指標**，指向當前的 commit 位置。這就是為什麼 Git 分支操作如此快速和輕量的原因！

`git branch` 和 `git checkout` 是 Git 工作流程中管理分支的重要命令，了解這些命令對於有效管理 Git 專案中的分支和工作流程非常重要，下面是說明還有常用的指令

參考：[git 清除機制](git%20清除機制.md)
### - git branch 命令

`git branch` 主要用於建立、列出和刪除分支。另外要記得，因為 working-directory 跟 staging-area 都被 git 歸類為全域變數，所以說切換分之前最好要確保已經 commit，不然遇到同檔案的衝突就無法切換成功
#### 1. 列出分支

```bash
# 列出本地分支
git branch

# 列出遠端分支
git branch -r

# 列出所有分支（本地和遠端）
git branch -a

# 顯示更多詳細資訊
git branch -v
```
#### 2. 建立分支

```bash
# 基於當前 HEAD 建立新分支（不會切換到新分支）
git branch new-branch-name

# 基於特定 commit 建立分支
git branch new-branch-name commit-hash
```
#### 3. 刪除分支

```bash
# 刪除已合併的分支
git branch -d branch-name

# 強制刪除未合併的分支
git branch -D branch-name
```
#### 4. 重命名分支

```bash
# 重命名當前分支
git branch -m new-name

# 重命名指定分支
git branch -m old-name new-name
```
### - git checkout 命令

`git checkout` 主要用於切換分支、建立新分支，以及==恢復檔案==
#### 1. 切換分支

```bash
# 切換到已存在的分支
git checkout branch-name

# 切換到上一個分支
git checkout -
```

#### 2. 建立並切換到新分支

```bash
# 基於當前 HEAD 建立新分支並切換
git checkout -b new-branch-name

# 基於特定 commit 建立新分支並切換
git checkout -b new-branch-name commit-hash

# 基於遠端分支建立本地分支並切換
git checkout -b local-branch origin/remote-branch
```

參考：[git 基礎](git%20基礎.md)
#### 3. 檢出檔案（恢復檔案到特定狀態）

```bash
# 還原工作目錄中的檔案（放棄未 commit 的修改）
git checkout -- file-name
# 還原全部工作目錄檔案
git checkout -- .

# 從特定 commit 檢出檔案（不會進入 detached HEAD）
git checkout commit-hash -- file-name
```

⚠️ 注意：檔案檢出（有 `--`）不會造成 detached HEAD！！

參考：
1. [與 Claude 的對話：Git HEAD、detached HEAD](https://claude.ai/chat/e9c428c2-7040-47bb-96f0-8fb82702b324)
2. [關於 detached HEAD (斷頭) 狀態](關於%20detached%20HEAD%20(斷頭)%20狀態.md)
3. [git reset 與 git rebase](git%20reset%20與%20git%20rebase.md)
#### 4. `git checkout` 的注意事項

1. 自 Git 2.23 版開始，`git checkout` 的功能被分拆到兩個新命令：
- `git switch` - 用於切換分支
- `git restore` - 用於恢復檔案
- 不過 `git checkout` 依然可以完全正常使用，沒有被棄用

1. git switch 的基本用法：
```bash
# 切換分支
git switch branch-name

# 建立並切換分支
git switch -c new-branch-name
```

2. git restore 的基本用法：
```bash
# 恢復工作目錄中的檔案
git restore file-name

# 從暫存區取消暫存
git restore --staged file-name
```
#### 5. 常見組合使用場景

1. 建立功能分支並立即切換：
```bash
git checkout -b feature/new-feature
```

2. 快速查看其他分支上的檔案然後返回：
```bash
git checkout other-branch -- path/to/file
```

3. 建立基於遠端分支的本地開發分支：
```bash
git checkout -b feature/fix-bug origin/develop
```