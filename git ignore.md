### - git ignore

如果有些檔案已經被 git 追蹤（tracked），但現在想要把它加入 .gitignore，需要先將它從 git 的追蹤清單中移除。步驟如下：

1. 先把檔案從 git 追蹤清單中移除，但保留檔案：
```bash
git rm --cached filename    # 移除單一檔案
git rm --cached -r folder/    # 移除整個資料夾
```

2. 在 .gitignore 檔案中加入要忽略的檔案或資料夾：
```bash
echo "filename" >> .gitignore    # 加入單一檔案
echo "folder/" >> .gitignore    # 加入整個資料夾
```

3. commit 這些更改：
```bash
git add .gitignore
git commit -m "update gitignore rules"
```

實際例子：
```bash
# 如果要忽略 .env 檔案
git rm --cached .env
echo ".env" >> .gitignore
git commit -m "stop tracking .env file"

# 如果要忽略 node_modules 資料夾
git rm --cached -r node_modules/
echo "node_modules/" >> .gitignore
git commit -m "stop tracking node_modules"
```

注意：這樣做**不會**刪除你本地的檔案，只是讓 git 停止追蹤這些檔案。但是當其他人在不同環境 (電腦) pull 的時候，對 git 來說這些檔案已經不再被 git 追蹤了，所以 pull 下來的時候，git 會自動把那個檔案/資料夾刪除，但還是存在於 github 上的 commit 歷史中!!

```git
# gitignore

檔案名稱.副檔名
*.副檔名
資料夾/
資料夾檔案名稱.副檔名
```

還有另外一點，假設我 main 分支的 ignore 在另外一個 A 分支**沒有記錄到**，這時候切到 A 分支上沒問題，但當要從 A 分支切回 main 的時候就會出現 untracked 的狀況，這時候看是要在 A 分支的 ignore 新增內容，這樣就可以繼續保留在工作目錄中，或者說先 stash 起來，切回 main 分支的時候再恢復

參考：[git stash](git%20stash.md)
### - 萬用字元

`.gitignore` 檔案可以使用萬用字元（wildcard）來處理多個資料夾。以下是一些常用的萬用字元模式：

* `*` - 匹配除了斜線 `/` 之外的任何字元序列
* `**` - 匹配任何字元序列，包括斜線（可跨多個目錄層級）
* `?` - 匹配任何單個字元
* `[abc]` - 匹配方括號中的任何一個字元
* `[a-z]` - 匹配指定範圍內的任何一個字元

以下是一些常見的使用例子：

1. 忽略所有名為 `node_modules` 的資料夾：
```
node_modules/
```

2. 忽略所有以 `temp` 開頭的資料夾：
```
temp*/
```

3. 忽略多層目錄下的所有 `logs` 資料夾：
```
**/logs/
```

4. 忽略所有以 `data` 結尾的資料夾：
```
*data/
```

5. 忽略特定模式的多個資料夾，例如 `data1`、`data2`、`data3` 等：
```
data[1-9]/
```

6. 忽略所有具有特定模式的檔案，無論它們位於哪個資料夾：
```
**/build/
**/dist/
**/.cache/
```

在使用萬用字元時，要確保模式足夠具體，避免不小心忽略了不應該忽略的檔案或資料夾。如果你想忽略特定資料夾但保留其中的某些檔案，你可以使用 `!` 前綴來否定某個模式：

```
# 忽略所有 logs 資料夾
logs/

# 但保留重要的日誌檔案
!logs/important.log
```
### - 問題

```
請問，如果我 git init 後 add 所有檔案，但忘記 node_modules 需要忽略，所以在他跑到一半的時候 ctrl+c 中斷，這樣：
1. add 有全部新增完成？還是中斷就中斷了？
2. 我需要砍掉 .git，重新來一次 git init 嗎？
```

讓我來解答這個 Git 相關的問題：

1. `git add` 被中斷的話，檔案只會部分加入到 staging area。Git 是以檔案為單位來加入的，所以已經處理完的檔案會成功加入，還沒處理到的就不會被加入。你可以用 `git status` 來確認目前有哪些檔案已經被加入 staging area

2. 不需要刪除 .git 重來！你可以：
   ```bash
   # 把所有已經 add 的檔案從 staging area 移除
   git reset

   # 建立 .gitignore 檔案並加入 node_modules
   echo "node_modules/" > .gitignore

   # 重新加入檔案，這次 node_modules 會被忽略
   git add .
   ```

如果想要確認目前的狀態，可以使用：
```bash
# 查看目前 staging area 的狀態
git status

# 查看已經被 track 的檔案列表
git ls-files
```

這樣就能在不刪除 .git 的情況下重新整理要追蹤的檔案了。記得之後如果有其他要忽略的檔案或資料夾，都可以加入到 .gitignore 中

git status 的結果中，如果檔案都還是 untracked 的狀態（顯示在 "Untracked files" 底下），代表先前的 `git add` 確實是被完全中斷了，沒有任何檔案被加入到 staging area

現在你可以直接：

1. 先建立 .gitignore（看起來你已經建立了）
2. 確認 .gitignore 裡有包含：
   ```
   node_modules/
   ```
3. 然後直接執行：
   ```bash
   git add .
   ```

這樣就能把除了 node_modules 以外的檔案都加入 staging area。不需要任何 reset 或重新 init 的動作，因為先前的 add 並沒有成功加入任何檔案

另外建議順便確認一下 .gitignore 是否要加入一些常見的項目，比如：
```
node_modules/
.env
.DS_Store
*.log
```

這樣可以避免一些敏感資訊或不必要的檔案被加入版控。

參考：
1. [靠北 .DS_Store 到底是什麼？該不該加入到版本控制內？](https://israynotarray.com/git/20220724/3916507104/)
2. [【狀況題】有些檔案我不想放在 Git 裡面…](https://gitbook.tw/chapters/using-git/ignore)