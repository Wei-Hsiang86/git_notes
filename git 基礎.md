### - Git 的三個區域

在操作 Git 時，理解 Git 的三個主要區域很重要：

1. **工作目錄 (Working Directory)**：實際編輯檔案的地方，包含所有未使用 `git add` 加入暫存區的變更

2. **暫存區 (Staging Area/Index)**：使用 `git add` 後，變更會從工作目錄移到暫存區，準備提交

3. **儲存庫 (Repository)**：使用 `git commit` 後，暫存區的變更會被永久保存到儲存庫中

在 Git 的工作流程中，`git add`、`git commit` 和 `git push` 這三個指令處理的檔案狀態可以這樣形容：

1. **git add** - **"staged"** (暫存)

   - 這個指令將檔案從工作目錄（working directory）加入到暫存區（staging area）
   - 檔案狀態從 "untracked" 或 "modified" 變成 "staged"

2. **git commit** - **"committed"** (已提交)

   - 這個指令將暫存區的檔案提交到本地儲存庫（local repository）
   - 檔案狀態從 "staged" 變成 "committed"
   - git commit 沒有 -m 時進入編輯器模式

3. **git push** - **"pushed"** 或 **"published"** (已推送/已發布)
   - 這個指令將本地儲存庫的提交推送到遠端儲存庫（remote repository）
   - 檔案狀態從本地的 "committed" 變成遠端的 "pushed" 或 "published"

這三個狀態代表了 Git 工作流程中檔案的不同階段，從本地的變更到最終發布到遠端儲存庫

![WFg26IS.png](pictures/WFg26IS.png)

額外提到， Git 的內部儲存機制中：

- **每個 commit 都記錄著它的父 commit**
- **但父 commit 不知道誰是它的子 commit**

### - git 指令結構

```bash
git <command> [options] [arguments]
```

- **command**：`merge`, `log`, `checkout` 等
- **options**：`--oneline`, `-m`, `--graph` 等（通常有 `-` 或 `--`）
- **arguments**：分支名稱、檔案名稱、commit hash 等

```bash
git log --oneline --graph origin/main
    │     │         │        │
    │     │         │        └─ argument (分支名稱)
    │     │         └─────────── option (圖形顯示)
    │     └───────────────────── option (單行顯示)
    └─────────────────────────── command
```

### - 環境變數

可以用以下指令檢查目前的 git 設定：

```bash
git config user.name    # 查看目前的名字
git config user.email   # 查看目前的 email
```

如果想看完整的 git 設定，可以用：

```bash
git config --list
```

git 的設定分成三個層級：

- 系統層級（system）
- 全域層級（global）
- 專案層級（local）

所以更精確的查看方式是：

```bash
git config --global user.name    # 查看全域設定的名字
git config --local user.name     # 查看當前專案設定的名字
```

如果需要修改名字，可以用：

```bash
git config --global user.name "新的名字"    # 修改全域設定
git config --local user.name "新的名字"     # 修改當前專案設定
```

### - 在 Windows 中文輸出會有亂碼的問題

```bash
git config --global core.quotepath false
```

參考：

1. [git log](git%20log.md)
2. [git stash](git%20stash.md)
3. [git 分支](git%20分支.md)
4. [git ignore](git%20ignore.md)
5. [Git Commit Message 這樣寫會更好，替專案引入規範與範例](https://wadehuanglearning.blogspot.com/2019/05/commit-commit-commit-why-what-commit.html)
6. [如何解決在命令提示字元下使用 Git for Windows 顯示中文亂碼的問題](https://blog.miniasp.com/post/2017/09/17/Git-for-Windows-Command-Prompt-Display-Chinese-Issues)
