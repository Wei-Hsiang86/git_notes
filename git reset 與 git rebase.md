Git 有三個重要區域：
- **工作目錄 Working Directory**：實際編輯檔案的地方
- **暫存區 Staging Area**：用 `git add` 後檔案存放的地方
- **儲存庫 Repository**：用 `git commit` 後檔案存放的地方

### - Git Reset

`git reset` 主要用於撤銷或重設當前 branch 狀態到指定的 commit，影響的是==這個 commit (不含他自己) 之後的所有 commit==。指定的時候可以用 hash 值，也可以用 HEAD 的相對位置表示

```hash
# 時間流
... → 7c7ab31 → 83bd9a0 → ad8d3f7 → 15f9306 (HEAD)
      HEAD~3     HEAD~2    HEAD~1      HEAD~0
```

`HEAD~1` 跟 `HEAD^` 同義，`HEAD~2` 跟 `HEAD^^` 同義，但數字的方式更為直觀。這裡有個小觀念可以注意，在 Git 的內部儲存機制中：
- **每個 commit 都記錄著它的父 commit**
- **但父 commit 不知道誰是它的子 commit**
所以才會用「HEAD^：HEAD 的父」，這樣的方式來表達
#### ▸ 三種模式
##### 1. `git reset --soft <commit>`：只移動 HEAD 指標到指定的 commit
- ✓ 移動 HEAD 指標到指定的 commit   
- ✓ 暫存區保持不變（檔案還在 staging）
- ✓ 工作目錄保持不變
- 📝 **結果**：就像剛執行完 `git add .` 準備要 commit
**使用時機**：想要**重新整理 commit 訊息**，或合併多個 commit
##### 2. `git reset --mixed <commit>` (預設)：移動 HEAD 指標，且撤回 commit
- ✓ 移動 HEAD 指標到指定的 commit
- △ 清空暫存區（撤銷 `git add`，==但保留在工作目錄中==）
- ✓ 工作目錄保持不變
- 📝 **結果**：就像剛修改完檔案，還沒執行 `git add`
**使用時機**：想要**重新選擇哪些檔案**要加入下次 commit
##### 3. `git reset --hard <commit>`：移動 HEAD 指標，且心血全部蛋雕
- ✓ 移動 HEAD 指標到指定的 commit
- △ 清空暫存區
- △ 工作目錄恢復到指定 commit 的狀態（==在這之後新增的所有東西都消失==）
- 📝 **結果**：就像時光倒流回，指定 commit 剛完成的時候
**使用時機**：確定想要完全放棄之後的所有變更
⚠️ **危險！** 會永久刪除檔案變更，使用前請確認
##### 4. 重要提醒
- 所有 `git reset` 都不會造成 detached HEAD（HEAD 仍指向當前分支）
- 被重置的 commit 會變成 unreachable objects，最終被 git 的回收機制清除
- 使用 `git reflog` 可以找回被 reset 的 commit
- 重要變更前建議先建立分支備份：`git branch backup`
##### 5. 記憶口訣
- **soft**：「溫柔地」只移動 HEAD，什麼都不刪
- **mixed**：「混合地」移動 HEAD + 清暫存區
- **hard**：「強硬地」全部重置，什麼都不留

- `--hard` 會真正刪除工作成果且無法復原，使用前請三思
- `--soft` 和 `--mixed` 都會保留檔案變更

參考：
1. [[git 基礎]]
2. [[關於 detached HEAD (斷頭) 狀態]]

### - Git Rebase

`git rebase` 主要用於重新應用一系列 commit 到==另一個基礎點上==，重寫 commit 歷史。rebase 不是單純的複製貼上，因為 HASH 值是根據父檔 SHA-1 算出來的，所以當參照點改變，全部都會重算

在 git 中可以注意執行動作的方向，這樣會讓 git 的動作理解更清楚。像是 push 就是推上遠端儲存庫，但 rebase 就是以指定分支為基準，rebase 我自己
#### ▸ 常見情境
##### 1. 整合不同分支
```bash
# 一般 rebase
git rebase <base-commit>
git rebase <branch name>
```
將**當前分支**的 commit 重新應用到指定的基礎點上，只是更常直接指定 branch 去做分支的合併

假設目前在 feature1 分支，當執行 `git rebase dev`，代表的意思為我是 feature1，我想要以 dev 為基礎對自己進行 rebase，也就是 rebase on dev。實際的動作是，找到當前分支 (feature1) 和目標分支 (dev) 最近的共同 commit (共同祖先)，然後收集從共同祖先的**下一個 commit** 開始到當前分支 (feature1) 的 HEAD 為止，一個個放到目標分支 (dev) 現在的 HEAD 後

參考：[另一種合併方式（使用 rebase）](https://gitbook.tw/chapters/branch/merge-with-rebase)
##### 2. 整理同分支的 commit 歷史
```bash
# 互動式 rebase
git rebase -i <起點>
```
打開互動式編輯器，可以選擇如何處理每個 commit：重新排序、合併、修改或刪除

- 起點本身**不會**被編輯
- 起點**之後**的所有 commit，也就是子 commit 才會進入編輯模式
#### ▸ 功能與特點

- 可以保留修改但重組 commit 歷史
- 可以編輯、合併、刪除或重新排序 commit
- 產生更乾淨、線性的歷史記錄
- 比 reset 更靈活，可以選擇性地處理特定的 commit
- ==會產生孤兒 commit==，通常 git 會有回收機制
#### ▸ 關於 git rebase 和工作目錄的關係

1. **工作目錄狀態要求**：
   - 在開始 rebase 之前，Git 通常要求你的工作目錄是「乾淨的」，也就是沒有未提交的修改
   - 如果你有未暫存或未提交的變更，Git 會拒絕執行 rebase，以防止你的未提交變更被覆蓋或丟失

2. **為什麼需要乾淨的工作目錄**：
   - Rebase 本質上是重新應用一系列 commit，這個過程可能會修改相同的檔案
   - 如果你的工作目錄有未提交的變更，這些變更可能會與 rebase 過程中的變更衝突

3. **處理未提交變更的選項**：
   - 將變更提交：`git commit -m "暫時提交"`
   - 將變更暫存：`git stash`（rebase 完後可用 `git stash pop` 恢復）
   - 放棄變更：`git reset --hard`（謹慎使用，會丟失所有未提交的變更）

4. **Rebase 對工作目錄的影響**：
   - 成功的 rebase 完成後，你的工作目錄會反映最終 rebase 後的狀態
   - 如果 rebase 過程中發生衝突，Git 會暫停 rebase，讓你解決衝突

如果你有未提交的變更但仍然想執行 rebase，最安全的方法是使用 `git stash` 暫時保存這些變更，完成 rebase 後再用 `git stash pop` 恢復

總結來說，為了安全起見，執行 rebase 前最好確保工作目錄是乾淨的，或者將變更暫時保存起來

參考：
1. [[git rebase 遇到問題]]
2. [[git 基礎]]
#### ▸ git rebase -i 互動操作的動作

互動式 rebase 中，時間順序是由遠到近的 (log 是最近的當第一，後續越來越久遠)，其中每個指令動作的意義：

1. **pick (p)**：
   - 保留這個 commit，不做任何修改
   - 這是默認動作

2. **reword (r)**：
   - 保留這個 commit，但修改其 commit 訊息
   - 執行時會打開編輯器讓你修改訊息

3. **edit (e)**：
   - 暫停在這個 commit，讓你可以修改 commit 的內容
   - 可以用 `git commit --amend` 修改當前 commit
   - 修改完成後，使用 `git rebase --continue` 繼續

4. **squash (s)**：
   - 將該 commit 合併到前一個(父) commit 中
   - 會打開編輯器讓你編輯合併後的 commit 訊息
   - 合併後的訊息預設包含兩個 commit 的訊息

5. **fixup (f)**：
   - 類似 squash，但直接使用前一個 commit 的訊息
   - 不會打開編輯器讓你編輯訊息
   - 適合合併小修改或修正前一個 commit 的內容

6. **drop (d)**：
   - 完全刪除該 commit
   - 注意：若此 commit 包含重要變更，可能會影響後續 commit

7. **exec (x)**：
   - 執行一個 shell 命令
   - 用於在 rebase 過程中執行測試或檢查

8. **break (b)**：
   - 在此處暫停 rebase 過程
   - 可讓你檢查中間狀態，然後手動繼續

9. **label (l)**：
   - 為當前 HEAD 添加一個標籤
   - 主要用於複雜的 rebase 操作

10. **reset (t)**：
  - 重置 HEAD 到指定標籤
  - 通常與 label 命令一起使用

在實際使用中，最常用的指令是 `pick`、`reword`、`squash`、`fixup` 和 `drop`。其中 `squash` 和 `fixup` 特別實用於整理和合併相關的 commit

如果你想合併兩個 commit 並保留完整的 commit 訊息歷史，使用 `squash`；如果只想保留第一個 commit 的訊息，使用 `fixup` 會更方便

### - Git Reset 與 Rebase 的主要差異

| 特性     | Git Reset         | Git Rebase            |
| ------ | ----------------- | --------------------- |
| 主要目的   | 撤銷提交，重置狀態         | 重構歷史記錄，保持清潔的提交線       |
| 對歷史的影響 | 直接丟棄某個點之後的所有提交    | 重新應用提交，保留變更但可能改變順序和內容 |
| 操作複雜度  | 相對簡單              | 相對複雜，尤其是互動式 rebase    |
| 適用場景   | 撤銷本地未推送的變更        | 整理提交歷史、合併分支前的準備工作     |
| 風險程度   | 較高（尤其是 --hard 模式） | 中等（可能需要解決衝突）          |

### - 注意事項

- 使用 `git reset --hard` 或 `git rebase` 前，最好先備份或確保你不需要被刪除的變更
- 修改已推送的 commit 歷史後，需要使用 `git push --force` 來更新遠端儲存庫
- 在共享分支上使用這些指令時需特別小心，可能影響其他協作者