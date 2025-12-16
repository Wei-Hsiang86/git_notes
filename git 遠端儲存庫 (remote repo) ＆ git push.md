### - git remote

一般來說，`origin` 是 clone 時自動設定的預設遠端 repo 名稱，但這個名稱其實可以依照需求更改，例如有些團隊會用 `prod`、`staging` 來區分不同環境的 repo。要查看遠端 repository 的設定有幾種方式：

1. 最基本的指令，列出所有遠端 repo：
```bash
git remote
```

2. 查看更詳細的資訊（包含 URL）：
```bash
# 顯示分支名稱和最後一次提交的簡短資訊
git remote -v

# 顯示本地分支名稱、最後一次提交 (前面的星號表示當前 branch)
# 像是這樣：* main 8e7cc3c feat: 更新模板
git branch -v

# 顯示本地分支名稱、最後一次提交，以及追蹤 (上游) 的遠端分支資訊
# 像是這樣：* main 8e7cc3c [origin/main] feat: 更新模板
git branch -vv (更詳細)
```

3. 查看特定遠端 repo（例如 origin）的詳細資訊：
```bash
git remote show origin
```

如果要新增或修改遠端 repo (下方詳細說明)：
```bash
git remote add <名稱> <URL>            # 新增遠端 repo
git remote rename <舊名稱> <新名稱>    # 重新命名
git remote remove <名稱>               # 移除遠端 repo
```

查看遠端儲存庫的相關資訊：
```bash
git remote show origin
```

實際例子：
```bash
# 新增一個叫做 upstream 的遠端 repo
git remote add upstream https://github.com/original/repo.git

# 把 origin 改名成 production
git remote rename origin production

# 查看 origin_github 這個遠端 repo 的資訊
(.venv) PS C:\Users\Frank\Desktop\FHIR\servers\fhir_converter\fhir> git remote show origin_github
* remote origin_github
  Fetch URL: https://github.com/Wei-Hsiang86/fhir_converter.git
  Push  URL: https://github.com/Wei-Hsiang86/fhir_converter.git
  HEAD branch: main
  Remote branches:
    dev                      tracked
    feature/FHIR結構調整 tracked
    feature/duplicate_id     tracked
    feature/explorer         tracked
    feature/new_xlsx         tracked
    feature/output_change    tracked
    feature/remove_blank     tracked
    main                     tracked
    scmh                     tracked
  Local branches configured for 'git pull':
    feature/explorer merges with remote feature/explorer
    scmh             merges with remote scmh
  Local refs configured for 'git push':
    dev                      pushes to dev                      (up to date)
    feature/FHIR結構調整 pushes to feature/FHIR結構調整 (up to date)
    feature/explorer         pushes to feature/explorer         (up to date)
    main                     pushes to main                     (up to date)
    scmh                     pushes to scmh                     (up to date)
```

#### git remote -v：查看遠端

`git remote` 指令用來查看遠端儲存庫，如果不加上任何副指令，就只會列出儲存庫名稱：
```bash
Frank@?? MINGW64 ~/Desktop/git_repo (main)
$ git remote
origin
```

若加入副指令 `-v` 可以列出更詳細的清單，-v 是 --verbose(詳細) 的縮寫：
```bash
Frank@?? MINGW64 ~/Desktop/git_repo (main)
$ git remote -v
origin  https://github.com/ellenlee/git-demo-remote.git (fetch)
origin  https://github.com/ellenlee/git-demo-remote.git (push)
```

詳細清單中會分開列出 `git fetch` 和 `git push` 的遠端儲存庫位置

#### git remote add：新增遠端儲存庫

`git remote add` 指令可以新增遠端儲存庫，指令後接收兩個參數，第一個參數是名稱，第二個參數是遠端儲存庫的 URL：
```bash
$ git remote add <name> <url>
```

`<name>` 的位置可以隨意由自己定義，而 `<url>` 需要參照 GitHub repository 上提供的網址，有 HTTPS 和 SSH 兩種連線方式。使用 HTTPS 會用 GitHub 的帳號密碼 / personal access tokens 來做身份驗證，而 SSH 則需要先在電腦裡設好金鑰，設定完成後會依金鑰來認出你的電腦
```bash
Frank@?? MINGW64 ~/Desktop/git_repo (main)
$ git remote add origin https://github.com/ellenlee/git-demo-remote.git
```

可以透過 `git remote add` 指令，在同一個專案上設定多個遠端主機：

![Pasted image 20231005171710.png](Pasted%20image%2020231005171710.png)

`git branch -vv` 可以透過這個指令查看目前的分支有哪些，並透過 `git branch <分支名稱>` 來新增分支，透過 `git checkout` 來選取分支
```bash
git branch -v
git branch -vv (詳細)

//結果如下
PS C:\Users\Frank\Desktop\CS_courses\AC\dev_backend\forum-express-grading-github-actions> git branch -vv
* main 3e54325 [origin/main] chore:  use pull_request_target event and checkout pull_request.head
```

參考：[git branch](git%20branch.md)

#### git remote remove：刪除遠端儲存庫

如果不小心設定錯誤，會需要用到刪除指令：
```bash
Frank@?? MINGW64 ~/Desktop/git_repo (main)
$ git remote remove origin
```

這樣就會把已設定好的 `origin` 主機刪除，可以重新再用 `git remote add` 加入

#### 設定不同 url 的 fetch  跟 push url

##### 1. 更新遠端儲存庫的 URL

更新 Git 遠端儲存庫（remote repo）的 URL 不需要刪除再新增，可以直接使用 `git remote set-url` 命令來修改現有遠端儲存庫的 URL
```bash
git remote set-url <遠端 repo name> <新的URL>
```

通常遠端儲存庫的名稱預設是 `origin`，如果遠端儲存庫使用其他名稱，記得替換成相應的名稱。例如，如果要將遠端儲存庫從 GitHub 更改為 GitLab：
```bash
git remote set-url origin https://gitlab.com/使用者名稱/專案名稱.git
```

##### 2. 分別設定 fetch 跟 push

```bash
git remote set-url <遠端 repo name> <新的URL>
git remote set-url --add --push <遠端 repo name> <另外一個URL>

# 刪除 url
git remote set-url --delete --push <遠端 repo name> <另外一個URL>
```

第一次使用 `git remote set-url --add --push` 時，會將**預設的 push URL 覆蓋掉**（原本的 URL 就是 fetch URL），所以需要再用同樣的指令把原始的 URL 加回來作為 push URL。此外 fetch 只能有一個 url，但是 push 可以有多個，所以可以**設定同個遠端 repo name，然後同時 push 到不同的遠端 repo**，這時候代表==不同的遠端 repo 只是多個備份==，但如果想要區分==不同的遠端 repo，建議設定不同名稱的 repo name==

```bash
# 1. 先設定 fetch URL（或是已經有了）
git remote set-url origin_github https://github.com/user/repo1.git

# 2. 新增第一個 push URL（這會覆蓋原本的 push URL）
git remote set-url --add --push origin_github https://github.com/user/repo2.git

# 3. 把原本的 URL 加回來作為 push URL
git remote set-url --add --push origin_github https://github.com/user/repo1.git

# 最終結果：
# origin_github  https://github.com/user/repo1.git (fetch)
# origin_github  https://github.com/user/repo2.git (push)
# origin_github  https://github.com/user/repo1.git (push)
```

可以透過 `git push -v` 看詳細的 push 結果，不然一般的 push 是兩個 push 結果依序輸出，但沒有其他的說明文字，容易被忽略
```bash
(.venv) PS C:\Users\Frank\Desktop\fhir_mapping_dev> git push -v
Pushing to https://github.com/Wei-Hsiang86/test.git
To https://github.com/Wei-Hsiang86/test.git
 = [up to date]      dev -> dev
updating local tracking ref 'refs/remotes/origin_github/dev'
Everything up-to-date
Pushing to https://github.com/Wei-Hsiang86/fhir_converter.git
To https://github.com/Wei-Hsiang86/fhir_converter.git
 = [up to date]      dev -> dev
updating local tracking ref 'refs/remotes/origin_github/dev'
Everything up-to-date
```

#### 什麼時候指令要帶遠端儲存庫

**用在需要「參考」遠端狀態的時候：**
```bash
# 比較差異
git diff dev origin/dev

# 查看 log
git log origin/dev

# Reset 到遠端狀態
git reset --hard origin/dev

# Merge 遠端分支
git merge origin/dev
```

**不需要帶儲存庫名字的時候：**
```bash
git checkout feature/分支名稱
git switch feature/分支名稱
```
### - git push：推送分支

#### 用法

設定好遠端儲存庫後，我們就可以將本地的程式碼「推」上遠端儲存庫，也就是使用 `git push` 指令來將本地專案的最新狀態推上遠端

在使用 `git push` 指令時，需要提供兩個資訊：

- `origin`：要推送的 **遠端儲存庫名稱**，這裡 `origin` 是 GitHub 的預設名稱
- `master`：這是想要推送的本地分支的名稱，如果沒有設定其他分支，預設的主要分支會叫 `master`。不過請注意自 2020.10 起，Github 因為[一些理由Links to an external site.](https://www.ithome.com.tw/news/140094)會把 `master` 重新命名叫 `main` (都是稱呼 **本地端的名字**)，也就是 `git branch -M main` 這行程式碼在做的事，這行代表幫 branch改名字

```bash
Frank@?? MINGW64 ~/Desktop/git_repo (main)
$ git branch -M main

Frank@?? MINGW64 ~/Desktop/git_repo (main)
$ git push -u origin main
```

- `u` 是 `-set-upstream` 的縮寫，意即「設定上游」。這個參數會讓本地的 `main` 和遠端 `origin` 儲存庫裡的 `main` 從此有一個上下游的關係，以後只要輸入 `git push`，就會自動把本地的 main 推送到遠端的 origin

#### 刪除遠端分支

##### 方法一：使用 push --delete（推薦）

```bash
git push origin --delete branch-name
```

例如刪除遠端的 `feature/old-feature` 分支：

```bash
git push origin --delete feature/old-feature
```

##### 方法二：使用冒號語法（舊語法）

```bash
git push origin :branch-name
```

這個語法的邏輯是「把空的內容推到遠端分支」，效果等同於刪除

```bash
git push origin :feature/old-feature
```


**如果也想把本地的分支一起刪掉：**

```bash
# 刪除本地分支（需要先切換到其他分支）
git branch -d branch-name

# 如果分支還沒合併，強制刪除
git branch -D branch-name
```

##### 完整範例

假設你要刪除 `feature/login` 分支：

```bash
# 1. 切換到其他分支（例如 main）
git checkout main

# 2. 刪除遠端分支
git push origin --delete feature/login

# 3. 刪除本地分支
git branch -d feature/login

# 4. 確認刪除成功
git branch -a
```

- 刪除遠端分支需要有相應的權限
- 通常主分支（main/master）會被保護，無法直接刪除
- 刪除後其他協作者需要執行 `git fetch --prune` 來同步刪除資訊

```bash
# 清理本地已經不存在的遠端分支參照
git fetch --prune
```

這樣就可以刪除遠端分支了！

參考：
1. [Git 基礎 - 檢視提交的歷史記錄](https://git-scm.com/book/zh-tw/v2/Git-%E5%9F%BA%E7%A4%8E-%E6%AA%A2%E8%A6%96%E6%8F%90%E4%BA%A4%E7%9A%84%E6%AD%B7%E5%8F%B2%E8%A8%98%E9%8C%84)
2. [Git 概念、常用語法及 GitHub 使用介紹](https://hackmd.io/@Yu040419/SyHrpos6V)
3. [git 協作](git%20協作.md)
4. [git branch](git%20branch.md)