多人協作的基本工作流程是：
- 將你的專案更新推送到遠端分支
- 告知你的協作伙伴
- 協作伙伴將遠端分支上的更新拉取到本地
- 進行合併，可能要解決合併衝突

另外，多人協作的時候要注意：
1. 撰寫格式要一致
2. 換行字元 (end of line / eol) 也要統一

> ==`git pull` = `git fetch` + `git merge`==

### - git tag

**Tag（標籤）**：像是「固定的里程碑」，一旦建立就永久指向那個 commit，不會移動

#### 實際用途

Tag 通常用來標記重要的版本發布點：

```bash
# 建立輕量標籤（lightweight tag）
git tag v1.0.0

# 建立附註標籤（annotated tag），包含額外資訊
git tag -a v1.0.0 -m "Release version 1.0.0"

# 查看所有標籤
git tag

# 查看特定標籤的詳細資訊
git show v1.0.0

# 推送標籤到遠端
git push origin v1.0.0
```

#### 視覺化理解

```
commit A --- commit B --- commit C --- commit D
              ↑           ↑           ↑
            v1.0.0      v1.1.0     main (branch)
           (tag)        (tag)
```

當 main 繼續有新 commit 時：

- `main` 會移動到新的 commit
- `v1.0.0` 和 `v1.1.0` 依然固定在原本的 commit

所以標籤就是「commit 的固定別名」，讓你可以輕鬆回到特定版本，而不需要記住 commit hash

### - git push

```bash
# 推送到遠端的預設分支
git push

# 指定遠端名稱和分支
git push origin main

# 第一次推送並設定上游分支
git push -u origin main
```

常用參數
- **`-u` 或 `--set-upstream`**：設定上游分支，之後就可以直接用 `git push` 而不用指定遠端和分支名稱
- **`-f` 或 `--force`**：強制推送（會覆蓋遠端的 commit，需謹慎使用）
- **`--all`**：推送所有分支
- **`--tags`**：推送所有標籤

### - git clone

將要 clone 的東西從 git repository 拉下來。另外如果是先 fork 他人的專案，接著從自己的倉庫 clone 下來，clone 會自動幫我們設定好 upstream，所以接下來要 push 回去，只要執行 `git push` 指令而不需另外指定參數

### - git fetch

讓本地端知道遠端有更新，此時可以用 `git fetch origin`，叫 Git 去檢查 `origin` 主機裡有沒有任何更新。本地端抓取了最新的遠端資訊後，就會更新線圖，把最新資訊反應出來：

![Pasted image 20240426170132.png](Pasted%20image%2020240426170132.png)

![Pasted image 20240426170029.png](Pasted%20image%2020240426170029.png)

==如果 github 端 (遠端) 有做任何的更新，或是更改 origin 的 url (即便網址一樣)，都一定要先 fetch，這樣本地端才會抓到新的資訊。如果沒有 fetch 不管怎麼 merge 都不會合併==
### - git merge

抓取到遠端分支後，就可以使用 `git merge` 來合併 origin/master (現在通常是用 main) 這條遠端分支，新的專案進度就會被合併到當前所在的分支上。另外，merge 後面一定要帶上合併的遠端分支，不然報錯

```bash
fatal: No remote for the current branch.
```

![Pasted image 20240426170143.png](Pasted%20image%2020240426170143.png)

![Pasted image 20240426170149.png](Pasted%20image%2020240426170149.png)

> 那到底平常要用比較快的 `git pull`，還是要用兩段式的 `git fetch` + `git merge` 呢？

一般來說，如果你不知道 merge 後會發生什麼事，建議使用兩段式分開處理

`git fetch` 本身只是更新遠端資訊，它不會影響到程式碼。可以隨時沒事就發出 `git fetch` 指令看一看其他人負責的分支是不是有新進度

如果看了以後想要把 master 進度先拉回來，就可以再 merge。也可以單純看看，暫時不想要自己的程式碼被干擾。因此，用 `git fetch` + `git merge` 兩段分開會讓你有比較高的掌控力

當然也會有一些求快速的情況想直接用 pull，或許今天大家約出來實體衝進度，在討論中你已經蠻清楚有什麼更動了，這時候就直接用 pull 一口氣拉回來

參考：[git stash](git%20stash.md)

### - fork

Fork 這個字在這邊翻譯成「複製」並不是這個字的原意，Fork 的中文翻譯是「分叉」、「叉子」，在技術圈來說這個詞使用的情境是「原作的做的不夠好，其它人覺得可以做得更好或是想加入一些個人喜好的功能而修改出另外的版本」，大概就是漫畫與同人誌差不多的概念吧

當我們在 github "fork" 他人的 repository 的時候，如果要將其全部的分支都一起複製，務必要將只複製 main branch 的這個選項取消

### - pull request (PR)

在 GitHub 上有個有趣的機制：
1. 先複製（Fork）一份原作的專案到你自己的 GitHub 帳號底下
2. 因為這個複製回來的專案已經在你自己的 GitHub 帳號下，所以你就有完整的權限，想怎麼改就怎麼改
3. 改完後，先推回（Push）你自己帳號的專案
4. 然後發個通知，讓原作者知道你有幫忙做了一些事情，請他看一下
5. 原作者看完後說「我覺得可以」，然後就決定把你做的這些修改合併（Merge）到他的專案裡

其中，第 4 步的那個「通知」，就是發一個請原作來拉回去（Pull）的請求（Request），稱之 Pull Request，簡稱 PR

也就是說，我不需要原作者開權限給我，讓我去對他的專案修改。而是我透過 fork 的方式，將其專案複製一分到我的倉庫，並且進行修改，改完之後發 PR 讓原作者知道，並決定是否要採用

參考：
1. [與其它開發者的互動 - 使用 Pull Request（PR）](https://gitbook.tw/chapters/github/pull-request)
2. [git 基礎](git%20基礎.md)
3. [git 遠端儲存庫 ＆ git push](git%20遠端儲存庫%20＆%20git%20push.md)
4. [](git%20reset%20與%20git%20rebase.md#-%20合併特定%20Commit%20的案例|使用%20rebase%20需要注意什麼)