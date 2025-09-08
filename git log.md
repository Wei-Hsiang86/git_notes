Git log 可以加上許多實用的參數來自定義輸出格式和過濾結果。以下是一些常用的 git log 參數：
### - 基本格式參數

1. `--oneline` - 每個提交只顯示一行（簡化的 commit ID 和提交訊息）
2. `--pretty=format:"..."` - 自定義輸出格式
3. `--graph` - 以 ASCII 圖形顯示分支和合併歷史
4. `--decorate` - 顯示分支和標籤名稱
5. `--abbrev-commit` - 顯示縮短的 commit ID
6. `--all` - 顯示所有分支的 log
### - 過濾和範圍參數

1. `-n <number>` 或 `--max-count=<number>` - 限制顯示的提交數量
2. `--since`, `--after` - 顯示特定日期之後的提交
3. `--until`, `--before` - 顯示特定日期之前的提交
4. `--author` - 過濾特定作者的提交
5. `--grep` - 搜尋提交訊息中含有特定文字的提交
6. `--committer` - 過濾特定提交者的提交
7. `-p` 或 `--patch` - 顯示每個提交的差異
8. `--stat` - 顯示每個提交中更改的檔案統計
9. `--name-only` - 只顯示更改的檔案名稱
10. `--name-status` - 顯示更改的檔案名稱及其狀態
### - 路徑過濾

`-- <path>` - 僅顯示指定路徑中的變更歷史
### - 常用組合範例

```bash
# 漂亮的圖形化顯示
git log --oneline --graph --decorate --all

# 自定義格式顯示
git log --pretty=format:"%h - %an, %ar : %s"

# 顯示最近 5 個提交的詳細差異
git log -n 5 -p

# 顯示過去兩週的提交
git log --since=2.weeks

# 查看特定檔案的歷史記錄
git log -- filename.js
```

如果需要查看特定參數的更詳細描述，可以使用 `git log --help` 或參考 Git 官方文檔
### - 比較兩個分支的 git log，有幾種常用方法

可以這麼想，`A..B` = 從 A 走到 B 需要經過的 commit，也就是 B 比 A 多出來的部分，也可以想成 A 缺少了什麼才會變成 B

1. 最基本的比較方式：
```bash
git log branch1..branch2
```
這會顯示在 branch2 中但不在 branch1 中的所有 commit

2. 如果你想看兩個分支間的差異（雙向比較）：
```bash
git log branch1...branch2
```
這會顯示從兩個分支的共同祖先以來所有的不同 commit

3. 加上 `--left-right` 選項可以清楚標示哪些 commit 來自哪個分支：
```bash
git log --left-right branch1...branch2
```
會在每個 commit 前面加上 < 或 > 箭頭標示來源分支

4. 如果只想看摘要，可以加上 `--oneline`：
```bash
git log --oneline --left-right branch1...branch2
```

5. 想看圖形化的 commit 歷史：
```bash
# 代表其他變化輸出的裝飾 optional
git log --graph --oneline --decorate branch1 branch2
```

如果是線性，會看到：
```bash
* 7effbb2 feat: 更新一些 Jupyter 的筆記
* 942c6de feat: 新增 Jupyter 使用筆記
* 79fbac2 feat: 新增 distross 與 distroless 的筆記
```

如果有分支，會看到：
```bash
*   7effbb2 Merge branch 'feature'
|\  
| * 942c6de feat: 新增功能
* | 79fbac2 feat: 修復 bug
|/
```

這些指令在比較特性分支（feature branch）和主分支（main/master）時特別有用，可以幫助你了解哪些變更尚未合併

參考：
1. [git 協作](git%20協作.md)
2. [git reset 與 git rebase](git%20reset%20與%20git%20rebase.md)