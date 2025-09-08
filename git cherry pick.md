### - 基本概念

- **功能**：從其他分支挑選特定 commit，複製到當前分支
- **特點**：原始分支不受影響，只是複製 commit
- **適用**：需要選擇性合併部分功能時

另外**一般情況下不需要自己 commit**，cherry-pick 會自動幫你 commit！只有特殊情況才需要手動處理

### - 基本語法

#### 挑選單個 commit

```bash
git cherry-pick <commit-hash>
```

#### 挑選多個 commit

```bash
# 挑選特定幾個
git cherry-pick <commit1> <commit2> <commit3>

# 挑選範圍（不包含 start）
git cherry-pick <start-commit>..<end-commit>

# 挑選範圍（包含 start），^ 代表那個 commit 的父 commit
git cherry-pick <start-commit>^..<end-commit>
```

### - 常用選項

```bash
# 只套用變更，不自動 commit
git cherry-pick -n <commit-hash>

# 保留原作者資訊
git cherry-pick -x <commit-hash>

# 編輯 commit message
git cherry-pick --edit <commit-hash>
```

### - 處理衝突

```bash
# 1. 執行 cherry-pick（可能遇到衝突）
git cherry-pick <commit-hash>

# 2. 解決衝突後，添加檔案
git add .

# 3. 繼續 cherry-pick
git cherry-pick --continue

# 4. 如果要放棄
git cherry-pick --abort
```

### - 實用場景

#### 🔧 Bug 修復移植

```bash
# 在 develop 分支修復 bug
git commit -m "fix: 修復登入驗證問題"

# 切換到 main 分支套用修復
git checkout main
git cherry-pick <bug-fix-commit>
```

#### 🚀 功能選擇性合併

```bash
# 從 feature 分支只要特定功能
git checkout main
git cherry-pick <feature-commit-1> <feature-commit-3>
# 跳過 feature-commit-2
```

#### 📦 版本間移植

```bash
# 從 v2.0 分支移植功能到 v1.x 維護版本
git checkout v1.x-maintenance
git cherry-pick <v2.0-feature-commit>
```

### - 最佳實踐

#### ✅ 適合使用的情況

- 每個 commit 都是獨立功能
- 需要精確控制合併內容
- 原始分支還要繼續開發
- 跨版本移植修復

#### ❌ 不適合的情況

- commit 包含太多混合變更
- 需要整個分支的完整歷史
- commit 之間有強依賴關係

#### 💡 建議

- 寫清楚的 commit message
- 一個 commit 做一件事
- 測試 cherry-pick 後的結果
- 注意 commit hash 會改變

### 注意事項

⚠️ **Cherry-pick 後的 commit hash 會不同**

```bash
# 原始 commit: abc123
# Cherry-pick 後: def456（內容相同，但 hash 不同）
```

⚠️ **可能產生重複的變更**

- 如果後續 merge 整個分支，可能會有重複 commit
- 使用 `git log --oneline --graph` 檢查歷史

⚠️ **依賴關係**

- 確保 cherry-pick 的 commit 不依賴其他未包含的變更

⚠️ **與 rebase 不同**

- rebase 會處理整條 branch，但 cherry pick 不會

參考：[git 協作](git%20reset%20與%20git%20rebase.md#-%20Git%20Rebase|git%20rebash)
