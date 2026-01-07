**reference (ref)** 在 Git 中就是**指向 commit 的指標**

### - Reference 的本質

Reference 其實就是一個**存著 commit SHA 的檔案**，非常簡單：

```bash
# 看看 HEAD 檔案內容
cat .git/HEAD
# 可能是：ref: refs/heads/main

# 看看 main 分支的內容
cat .git/refs/heads/main
# 內容：a1b2c3d4e5f6... (就是一個 commit SHA)
```

### - Reference 的種類

```
.git/
├── HEAD              # 指向當前分支
├── ORIG_HEAD         # 指向操作前的位置
├── FETCH_HEAD        # 指向最後 fetch 的內容
├── MERGE_HEAD        # merge 時才存在
└── refs/
    ├── heads/        # 本地分支 (branch)
    │   ├── main
    │   └── feature
    ├── remotes/      # 遠端分支
    │   └── origin/
    │       └── main
    └── tags/         # 標籤
        └── v1.0.0
```

#### 常見用法

```bash
# Git 指令中也常用 refs 這個詞
git show-ref              # 列出所有 refs
git update-ref            # 更新 ref
git for-each-ref          # 遍歷所有 refs

# 完整路徑時會用 refs
git show refs/heads/main       # 指定分支
git push origin refs/tags/v1.0 # 推送 tag
```

### - 用類比來理解

把 Git 的 commit 歷史想像成一個**連結串列 (linked list)**：

```
commit物件A ← commit物件B ← commit物件C
```

那 reference 就像是 **pointer 變數**：

```javascript
// 類似這樣的概念
let main = commitC;        // 分支是指標
let HEAD = main;           // HEAD 指向分支
let ORIG_HEAD = commitB;   // ORIG_HEAD 也是指標
let v1_0_0 = commitA;      // tag 也是指標
```

### - 分支也是 Reference！

這就是為什麼 Git 的分支這麼輕量，因為**分支就只是一個 ref**：

```bash
# 創建分支 = 創建一個新的 reference
git branch new-feature
# 實際上就是在 .git/refs/heads/ 下創建一個檔案

# 切換分支 = 改變 HEAD 指向
git checkout new-feature
# 就是把 HEAD 的內容改成 ref: refs/heads/new-feature
```

### - 小結

- **Reference = 指標**，指向某個 commit
- 分支、tag、HEAD、ORIG_HEAD 都是 reference
- 它們的差異在於**如何管理**和**用途**，但本質都是指標

參考：[git reflog 與 ORIG_HEAD](git%20reflog%20與%20ORIG_HEAD.md)