### Fast-Forward（預設）

**行為：** 如果可能，直接移動指標（不產生 merge commit）

**條件：** 目標分支沒有新的 commit

**圖示：**

```
合併前：
dev:    A --- B
             \
feature:      C --- D

合併後：
dev:    A --- B --- C --- D  (完全線性)
```

**指令：**

```bash
git merge feature/新功能
```

**特點：**

- ✅ 歷史簡潔、線性
- ✅ 保留 feature 的所有 commit
- ❌ 看不出「這是一個功能的邊界」

---

### --no-ff（Non-Fast-Forward）

**行為：** 強制產生 merge commit，保留分支結構

**圖示：**

```
dev:    A --- B ----------- M (merge commit)
             \             /
feature:      C ------- D
```

**指令：**

```bash
git merge --no-ff feature/新功能
```

**特點：**

- ✅ 保留 feature 的所有 commit
- ✅ 明確標記「這是一個功能」
- ✅ 圖形上顯示分支結構
- ⚠️ 會產生額外的 merge commit

**適用：** 想明確看出功能開發的完整歷史

---

### --squash（壓縮合併）

**行為：** 把 feature 的所有 commit 壓成一個新 commit

**圖示：**

```
合併前：
dev:    A --- B
             \
feature:      C --- D --- E

合併後：
dev:    A --- B --- S (squashed commit，包含 C+D+E 的改動)
```

**指令：**

```bash
git merge --squash feature/新功能
git commit -m "feat: 完成新功能"
```

**特點：**

- ✅ 歷史簡潔（只有 1 個 commit）
- ❌ 失去 feature 的詳細開發過程
- ✅ 適合清理雜亂的實驗性 commit

**適用：** Feature 有很多無意義的中間 commit，想要乾淨的歷史

---

### - 比較表

|方法|Merge Commit|Feature Commit|歷史|適用情境|
|---|---|---|---|---|
|**Fast-Forward**|❌|✅ 全部保留|線性|預設，簡單合併|
|**--no-ff**|✅|✅ 全部保留|有分支結構|想明確標記功能|
|**--squash**|❌|❌ 壓成 1 個|線性|清理雜亂 commit|

---

### - 實用指令速查

#### 查看分支追蹤狀態

```bash
git branch -vv

# [] 中括號內就代表有設定上游，會偵測目前的 branch 自動 push 到哪個遠端 repo
(.venv) PS C:\Users\Frank\OneDrive\桌面\工作\fhir\fhir_mapping> git branch -vv
  dev                  8e7cc3c [origin/dev] feat: 更新模板
* feature/update_error 8e7cc3c feat: 更新模板
  main                 8e7cc3c [origin/main] feat: 更新模板
```

#### 查看 Git 圖形歷史

```bash
git log --oneline --graph --all
```

#### 刪除本地分支

```bash
git branch -d feature/分支名
```

#### 刪除遠端分支

```bash
git push origin --delete feature/分支名
```

#### 查看 Git 設定

```bash
git config --list
```

#### 合併出現衝突，放棄當前 merge

```bash
git merge --abort
```

參考：[](git%20基礎.md#-%20環境變數|%20git%20config)

---

### - 最佳實作建議

1. **開發前先 pull**：`git pull origin dev`
2. **Feature branch 命名清楚**：`feature/功能名稱`、`bugfix/問題描述`
3. **Commit 訊息規範**：`feat:`、`fix:`、`docs:` 等
4. **定期清理已合併的分支**
5. **重要功能使用 --no-ff**，保留開發歷史
6. **雜亂的實驗性 commit 使用 --squash** 清理

參考：
1. [不同環境開發：遠端連線設定](不同環境開發：遠端連線設定.md)
2. [不同環境開發：單人開發 vs 多人開發工作流程](不同環境開發：單人開發%20vs%20多人開發工作流程.md)