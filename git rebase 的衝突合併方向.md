### - Rebase 的衝突標示

當在 `feature/converter` 執行 `git rebase origin_gitlab/develop` 時：

| 標示                 | 實際上是                                  |
| ------------------ | ------------------------------------- |
| **Current (HEAD)** | `origin_gitlab/develop`（你 rebase 的基準） |
| **Incoming**       | `feature/converter` 的 commit（正在被重播的）  |

### - 為什麼會這樣？

因為 rebase 的運作方式是：

1. 先把 HEAD 切到 `origin_gitlab/develop`
2. 然後把你 `feature/converter` 的 commits **一個一個重播**上去

所以在重播過程中，HEAD 是停在 `develop` 那邊的，你自己的修改反而變成「incoming」要被套用進來

這跟 **merge 剛好相反**：

|操作|Current|Incoming|
|---|---|---|
|`git merge develop`|你的分支|develop|
|`git rebase develop`|develop|你的分支|

### - 小技巧

衝突時如果想確認哪邊是誰的，可以用：

```bash
git log --oneline HEAD
git log --oneline REBASE_HEAD
```

- `HEAD` → 目前基準點（develop 的內容 + 已成功重播的 commits）
- `REBASE_HEAD` → 正在嘗試套用的那個 commit（你的）

參考：[git rebase 遇到問題](git%20rebase%20遇到問題.md)