### - 單人開發（本地 merge）

**情境：** 目前只有自己開發，但為未來協作做準備

**分支策略：**

```
main (穩定版本)
 └── dev (開發主線)
      ├── feature/功能A
      ├── feature/功能B
      └── feature/功能C
```

**工作流程：**

```bash
# 1. 從 dev 開新功能分支
git checkout dev
git checkout -b feature/新功能

# 2. 開發、commit（不 push feature branch）
git add .
git commit -m "feat: 新功能描述"

# 3. 完成後切回 dev，先同步遠端
git checkout dev
git pull origin dev

# 4. 本地 merge
git merge feature/新功能

# 5. 只 push dev（feature branch 不出現在 GitLab）
git push origin dev

# 6. 清理本地 feature branch
git branch -d feature/新功能
```

**優點：**

- 簡單快速
- GitLab 上只看到 dev 和 main，不會有一堆 feature branch

**缺點：**

- 沒有 code review
- 沒有 CI/CD 自動測試

---

### - 多人開發（Merge Request）

**情境：** 團隊協作，需要 code review 和 CI/CD

**工作流程：**

```bash
# 1. 從 dev 開新功能分支
git checkout dev
git pull origin dev
git checkout -b feature/新功能

# 2. 開發、commit
git add .
git commit -m "feat: 新功能描述"

# 3. Push feature branch 到遠端
git push -u origin feature/新功能

# 4. 到 GitLab 發起 Merge Request (MR)
# - Source: feature/新功能
# - Target: dev
# - 指定 Reviewer

# 5. 等待審查、討論、修改

# 6. 審查通過後，在 GitLab 上點擊「Merge」按鈕

# 7. Merge 完成後，刪除 feature branch
git checkout dev
git pull origin dev
git branch -d feature/新功能
git push origin --delete feature/新功能
```

**優點：**

- Code review 提升程式碼品質
- CI/CD 自動測試
- 團隊可見性高
- 清楚的討論記錄

**缺點：**

- 流程較長

---

### - 對比表

|特性|單人開發|多人開發|
|---|---|---|
|**Merge 位置**|本地|GitLab|
|**Feature branch**|不 push|Push 到遠端|
|**Code Review**|❌|✅|
|**CI/CD**|❌|✅|
|**流程複雜度**|簡單|較複雜|
|**適用情境**|獨立開發|團隊協作|

參考：
1. [不同環境開發：遠端連線設定](不同環境開發：遠端連線設定.md)
2. [不同環境開發：Git Merge 方法比較](不同環境開發：Git%20Merge%20方法比較.md)