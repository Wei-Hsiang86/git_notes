git 清除機制是 Git 的核心機制。當沒有賦予名字的 branch 註定會被 git 清除，就算只有一個 commit 的 branch，只要他有入口/造訪點 (branch name) 就不會被清掉

### - 沒有名字的分支會被清除

如果一個 commit 沒有任何**可達路徑**（reachable path），Git 的垃圾回收機制會將它清除。這些可達路徑包括：

- **分支名稱** (`main`, `feature-login`)
- **標籤** (`v1.0`, `release-2023`)
- **HEAD** 指標
- **reflog** (短期內的歷史記錄)

### - 實際例子

```bash
# 創建一個新分支並切換
git checkout -b temp-branch
echo "test" > file.txt
git add file.txt
git commit -m "temp commit"

# 切換回 main 並刪除分支名稱
git checkout main
git branch -D temp-branch  # 強制刪除

# 這個 commit 現在變成「孤兒」，最終會被 gc 清除
```

### - 有名字就安全

```bash
# 即使只有一個 commit 的分支
git checkout -b single-commit-branch
echo "important" > important.txt
git add important.txt
git commit -m "important work"

# 只要 single-commit-branch 這個名稱存在
# 這個 commit 就永遠不會被清除
```

### - Git 的保護機制

- **reflog**：短期內（通常 30-90 天）還可以找回
- **gc (garbage collection)**：定期清理無法到達的物件
- **分支/標籤**：作為「錨點」保護 commits

所以：**名稱就是入口，有入口就安全！**