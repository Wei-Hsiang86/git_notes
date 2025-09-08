**Detached HEAD 的定義**：HEAD 直接指向某個 commit，而不是透過分支名稱指向。當直接 checkout 到 commit、tag，或是遠端分支時就會發生

特別留意的是，為什麼說遠端分支會是 detached HEAD？因為遠端分支 (如 origin/main) 是唯讀的本地鏡像，代表遠端狀態的快照，不能直接在上面進行開發工作

```
Normal: HEAD -> branch -> commit
Detached: HEAD -> commit
```

> 斷頭狀態講求的是 HEAD 指標指的那個 commit，也就是當下的 commit 是沒有任何入口點 (branch name) 可以造訪的

detached HEAD 是一種**狀態**，不是 commit 本身的屬性。它代表的是這個或者這群 commit (下方小提醒) 是沒有歸屬的，也就是沒有入口點 (branch name) 可以造訪。如果這時候貿然切換到其他 branch，git 會跳出通知提醒，如下：
```bash
Warning: you are leaving 3 commits behind, not connected to any of your branches:

  yyy commit message for yyy
  xxx commit message for xxx  
  abc ccc_改

If you want to keep them by creating a new branch, this may be a good time
to do so with:

  git branch <new-branch-name> yyy
```
小提醒：即使在 detached HEAD 狀態，commit 之間依然會自動串接成鏈條。只是沒有名字，所以強制離開，會找不到回來的路，除非透過 `git reflog` 找到並給予 branch name

當出現斷頭的情況，就代表那是個/條沒有名字的 branch，或者是其他沒有任何可達路徑的情況，那就會被 git 內建的機制清除。如何避免 detached HEAD：
1. 移動到 branch 再 checkout 某個 commit
2. 給他一個 branch 名字
```bash
git checkout -b branch-name
```

參考：
1. [與 Claude 的對話：Git HEAD、detached HEAD](https://claude.ai/chat/e9c428c2-7040-47bb-96f0-8fb82702b324)
2. [git 分支](git%20分支.md)
3. [git 清除機制](git%20清除機制.md)
### - 需要 detached HEAD 的狀況

- 檢查歷史狀態
- 實驗性修改
- 緊急修復（Hotfix）
- Cherry-pick 前的預覽
### - 值得玩味的地方

`git reset` 或是 `git rebase` 不會導致「斷頭」(detached HEAD) 狀態，而是會出現「不可達」(unreachable object)

因為當使用 reset 時，當前的 HEAD **仍然指向當前分支**，他有自己的分支名稱，有自己的歸屬，只是分支指標 (也就是 HEAD) 被移動到指定的 commit 上。只是說另外被丟棄的 commit，這些原始 commit 會變成==不可達==，最終會被 Git 的垃圾回收機制清除

rebase 也是一樣當前的 HEAD **仍然指向當前分支**，且因為 git 的執行原理不可能就把原本的 commit，以及他的 HASH 值這樣塗抹改掉不當一回事，真的要說的話會有點像是嫁接這樣，`rebase on <branch name>` 以某個 branch 為基準來修改。所以舊的那些 commit 一樣會變成==不可達==的狀態

但是呢，我們可以透過 `git reflog` 去找到這些 commit 的 SHA-1 (HASH) 值，並且透過 `git checkout` 造訪，這時候就可以正是說我們處在 ==detached HEAD== 的狀態了 (雖然可能有點沒有意義)

參考：[git reflog](git%20reflog.md)