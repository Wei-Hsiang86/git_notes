`git reflog` 的主要用途是記錄你在本地儲存庫中的所有操作歷史，包括那些可能不會出現在正常 `git log` 中的操作。它是一個非常強大的救援工具，特別適合以下情況
### - `git reflog` 的主要用途

1. **恢復已刪除的分支或提交**
   - 如果你不小心刪除了一個分支或重設（reset）了一個提交，可以使用 reflog 找回那些「消失」的提交
   - 如果不小心 rebase 摧毀了一段歷史，也可以透過 reflog 找回

2. **追蹤 HEAD 的移動歷史**
   - 記錄 HEAD 指針的所有移動，包括切換分支、重設、合併等操作

3. **找回丟失的工作**
   - 如果你使用了 `git reset --hard` 或其他可能導致工作丟失的命令，可以通過 reflog 找回

4. **了解儲存庫的操作歷史**
   - 查看儲存庫中所有的操作，包括合併、重置、切換分支等
#### 使用範例

```bash
# 查看基本 reflog
git reflog

# 查看特定引用的 reflog（如某個分支）
git reflog show branch-name

# 使用 reflog 恢復已刪除的分支
git checkout -b recovered-branch HEAD@{2}

# 恢復到某個特定操作之前的狀態
git reset --hard HEAD@{5}
```
#### 重要特性

- reflog 是**本地**的，不會被推送到遠端儲存庫
- reflog 有**時限**，預設保留 90 天（可配置）
- reflog 是 Git 的安全網，能幫助你從幾乎任何意外操作中恢復

對於新手和有經驗的開發者來說，了解和使用 `git reflog` 都是非常有價值的，它可以在你犯錯或需要查找特定操作歷史時提供很大幫助

### - ORIG_HEAD

`ORIG_HEAD` 是 Git 用來記錄**某些危險操作執行前的 HEAD 位置**的特殊參考

#### 什麼時候會產生 ORIG_HEAD？

當你執行以下這些會大幅改變 commit 歷史的操作時，Git 會自動儲存當前 HEAD 到 `ORIG_HEAD`：

- `git reset`
- `git merge`
- `git rebase`
- `git pull` (因為包含 merge 或 rebase)
- `git am` (套用 patch)

#### 用途：後悔藥機制

`ORIG_HEAD` 就像是一個「操作前的快照」，讓你在誤操作後能夠回到之前的狀態

##### 實際例子

假設你不小心 reset 太多：

```bash
# 原本在 commit A
git reset --hard HEAD~3  # 退回 3 個 commit

# 糟糕！reset 太多了，想回去
git reset --hard ORIG_HEAD  # 回到 reset 前的位置
```

或是 merge 後發現不對勁：

```bash
git merge feature-branch  # merge 完發現有問題

# 取消這次 merge
git reset --hard ORIG_HEAD
```

#### 注意事項

- `ORIG_HEAD` 會被**下一次危險操作覆蓋**，所以只能用來救援「最近一次」的失誤
- 如果需要更完整的歷史記錄，可以用 `git reflog` 查看

簡單來說，`ORIG_HEAD` 就是 Git 給你的一個「上一步」按鈕，專門用於那些會大改歷史的操作

參考：[[reference (git refs)]]