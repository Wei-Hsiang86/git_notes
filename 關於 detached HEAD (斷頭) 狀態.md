### - detached HEAD 說明

**Detached HEAD 的定義**：HEAD 直接指向某個 commit，**而不是透過分支名稱指向**。當 checkout 到 commit、tag，或是遠端分支時會發生。這裡要特別留意的是，為什麼說**遠端分支**會是 detached HEAD？因為遠端分支 (如 origin/main) 是唯讀的本地鏡像，代表遠端狀態的快照，不能直接在上面進行開發工作

```
Normal: HEAD -> branch -> commit
Detached: HEAD -> commit
```

> 斷頭狀態講求的是 HEAD 指標指的那個 commit，也就是當下的 commit 是沒有任何入口點 (branch name) 可以造訪的

detached HEAD 是一種**狀態**，不是 commit 本身的屬性。它代表的是這個或者這群 commit (下方小提醒) 是沒有歸屬的，也就是==沒有入口點 (branch name) ==可以造訪。如果這時候貿然切換到其他 branch，git 會跳出通知提醒，如下：
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
2. [git branch ＆ git checkout](git%20branch%20＆%20git%20checkout.md)
3. [git reset 與 git rebase](git%20reset%20與%20git%20rebase.md)
4. [git status ＆ git restore](git%20status%20＆%20git%20restore.md)
5. [git 清除機制](git%20清除機制.md)

#### - 需要 detached HEAD 的狀況

- 檢查歷史狀態
- 實驗性修改
- 緊急修復（Hotfix）
- Cherry-pick 前的預覽

#### - 如何退出 detached HEAD 狀態

**方法 1：切換回原本的分支**
```bash
git checkout main
```

**方法 2：如果想保留在 detached HEAD 做的修改**
```bash
git checkout -b new-branch-name
```

#### - detached HEAD 的實用案例

```bash
# 情境：想確認某個舊版本是否有 bug
git checkout abc123          # 進入 detached HEAD

# 測試、編譯、執行...
git checkout -               # 回到原本的分支（不保留任何修改）

# 或是想保留測試時的修改
git checkout -b test-fix     # 給個名字，避免被清除
```

### - 值得玩味的地方

`git reset` 或是 `git rebase` 不會導致「斷頭」(detached HEAD) 狀態，而是會出現 **「不可達」(unreachable object)**

因為當使用 reset 時，當前的 HEAD **仍然指向當前分支**（例如 main），分支指標 (HEAD) 被移動到指定的 commit 上。被丟棄的 commit 會變成 **dangling commits**（懸空 commit），也就是**不可達 (unreachable)** 的狀態，最終會被 Git 的垃圾回收機制清除
註：dangling commits 和 unreachable objects 是同義詞，都是指沒有任何 reference（branch、tag、HEAD）指向的 commit

rebase 也是一樣當前的 HEAD **仍然指向當前分支**，且因為 git 的執行不可能把原本的 commit，以及他的 HASH 值這樣塗改掉當作不存在，而是有點像嫁接這樣，`rebase on <branch name>` 以某個 branch 為基準來修改。所以舊的那些 commit 一樣會變成**不可達**的狀態

但是呢，我們可以透過 `git reflog` 去找到這些 commit 的 SHA-1 (HASH) 值，並且透過 `git checkout` 造訪，這時候就可以正是說我們處在 **detached HEAD** 的狀態了 (雖然可能有點沒有意義)

參考：
1. [git reflog 與 ORIG_HEAD](git%20reflog%20與%20ORIG_HEAD.md)
2. [](git%20清除機制.md#-%20術語說明|懸空與不可達的%20commits)