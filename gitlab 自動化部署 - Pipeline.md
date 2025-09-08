GitLab Workflow內容包括，git 版本控制系統、CI/CD Pipeline，以及專案管理的功能，像 Wiki、Issue Tracking、Kanban、Burndown Chart等。GitLab 的 CI/CD Pipeline 會依據使用者存放在各專案內的 **.gitlab-ci.yml** 檔自動建立，且能順利整合從測試到部署的大多數常見工具或第三方服務

### - CI/CD 的三個階段

**Continuous Integration 持續整合**  
藉由頻繁的整合，來減輕版本之間差異過大時，會發生的合併困難甚或失敗。在此階段，通常還會加入自動化測試來確保待合併的分支，不會發生功能錯誤

**Continuous Delivery 持續佈署**  
在確定程式碼的功能性正常後，會自動因應不同的環境進行相對應的程式檔佈署。佈署完成後，可以進行手動測試驗證

**Continuous Deployment 持續交付**  
在已經有相對應的程式檔後，自然就要落地到機器上來正式運行，給客戶們使用啦，這就是這階段的目的

CI/CD可以加快代碼發布速度，CI/CD使開發人員能夠更快地做更多的事情，確保可靠性和可用性，提高開發人員的效率，自動化，工具和可觀察性。Gitlab Pipeline 優點如下：
1. 易於配置(**.gitlab-ci.yml**)
2. 自動化測試降低回歸測試成本
3. 分支持續整合問題即時反饋

參考：
1. [gitlab 自動化部署 - Pipeline](https://ithelp.ithome.com.tw/articles/10299528)
2. [淺談開發流程 — Git Flow 到 Trunk-Based Development 的團隊經驗雜談](https://medium.com/@shanpigliao/a956a379987)