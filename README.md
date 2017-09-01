* 0.1.0
  * function a
* 0.2.0
  * function b
  * This line is from USER A
  * 0.2.1
    * fix function B
* 0.3.0
  * function c
  * This line is from USER A for version 0.3.0
* 0.4.0
  * function d
  * This line is from USER A for version 0.4.0

<hr>

[Git Flow - Charlie version 步驟]

* 詳細分享(包含 push 各個 branch-release/hotfix/feature)
    * https://kknews.cc/zh-tw/other/zb4moq.html
* 反正不管 tag / branch，都可以 checkout 複製出來
* 我就用 git flow 好了
* 雖然 人人都適合 用 git flow tool, 但是 可以 finish 這個功能，最好只有一個人
* 只保留 (保持 remote / local 一致)
    * branch
        * master / develop
    * tag
        * 每次的版本升級
    * Remote 部分
        * git flow 完成，預設
            * 刪除 local 非 develop / master
            * 但是 remote 會保留，再找時間，把他給 delete 好了
            * 記得 delete 後，要
                * git remote update
                * git remote prune origin
    * Local - 合作user 部分
        * git flow 完成, 預設
            * 合作的人還是會在 local 留著 branch, 可以直接刪除
            * 反正 remote 還留著
    * Remote branch 不刪，就是 那個黑色標籤在不在，但是點點都會在, 所以真的沒差
        * 
區分三種角色
* 中央控管 (release manager using git flow tool)
    * github create repo
    * git flow init -d
        * 
    * git remote add origin git@github.com:charlietag/git-flow-usage.git
    * git push --all -u
        * 把所有 branch (develop/master)都推到 github
        * 順帶 把 upstram (.git/config) 設定好，以便未來 pull / push
        * github 預設會以 master 為 主要 default branch
    * (The following) Run 3 times for 3 releases
    * git flow feature start function_a
        * echo "function A" > file_funcA
        * git add .
        * git ci -m 'add file func_A'
    * git flow feature publish function_a
    * git flow feature finish function_a
    * git push --all
        * 
    * git flow release start 0.1.0
        * vi README.md
        * git add .
        * git ci -m 'add readme and add version changed into readme'
    * git flow release publish 0.1.0 (遠端 branch 建立)
        * 
    * git flow release finish 0.1.0 (遠端 branch 刪除)
        * [merge to master] commit message ---> Merge branch 'release/0.1.0' merge (add function A)
        * [tag commit message] Add function A, add file function_a (tag)
        * [merge to develop] Merge branch 'release/0.1.0' into develop (Add function_a file)
    * git push --all
    * git push --tags
    * git remote show origin
        * 
    * git remote prune origin  (不知道為啥，remote 被誤刪了, 用 prune 整理一下)
        * 
    * Run 3 times for 3 releases
        * 但是這次，有搭配第二個開發者
            * git flow feature start function_b
                * echo "function B" > file_funcB
                * git add .
                * git ci -m 'add file func_B'
            * git flow feature publish function_b
        * (user) phase 1下面User 修改 後 push
            * git fetch
                * git status
                * 會說有一個遠端的 commit 需要 git pull 來 merge
            * git pull
            * git flow feature finish function_b
            * git push --all
        * git flow release start 0.2.0
            * vi README.md
            * git add .
            * git ci -m 'add readme and add version changed into readme - version 0.2.0'
        * git flow release publish 0.2.0
        * (user) phase 2下面User 修改 後 push
            * git fetch
                * git status
            * git pull
        * git flow release finish 0.2.0
            * (merge to master) Merge branch 'release/0.2.0' (after fetch from user A desc, prepare to release)
            * (tag message) add release tag 0.2.0 (User A is envolved)
            * (merge to dev) Merge branch 'release/0.2.0' into develop (User A is envolved for releasing version)
        * git push --all
        * git push --tags
    * 上面一模一樣跑一次, Run 3 times for 3 releases (這次 user 端, 使用 git flow tool 去 fetch, 應該只有 git branch --track ，變成 git flow pull/track tool)
        * (user) phase 3下面User 修改 後 push
            * 不要使用 git flow feature pull origin function_D <-- 沒有幫你 update remote status (git remote update)
        * (user) phase 4下面User 修改 後 push
    * Git hotfix 嘗試, 雖然我有把 release branch 留在上面，我還是試試看 hotfix from tag
        * 下面測試結論，從中間 fix 一點意義都沒有，git flow 模式都會 merge 回 master, 所以都還是在同一條，這個 fix 會影響到後面的主幹
        * 如果真的要 hotfix 特定版本，可能真的分開不同的 branch, 但這樣就跟不同 project 一樣了...
        * 應該說 git flow 的設計，是沒有考量多版本的不能像是 rails 還回頭修不同 version的 bug....
        * 而是一條健走到底, 不能回頭修改
        * 要像是 rails 能回頭，就不能用 git flow
            * git branch branch-0.2.0 0.2.0  (從tag 0.2.0 , checkout branch 0.2.0)
            * git flow hotfix start 0.2.1 branch-0.2.0
                * edit something
                * git add .
                * git ci -m 'hotfix for funcB , version to 0.2.1'
            * git flow hotfix publish 0.2.1
            * git flow hotfix finish 0.2.1
            * (conflict) master
                * 
                * git add .
                * git ci -m 'fix conflict'
            * git flow hotfix finish 0.2.1
            * (conflict) develop
                * 
                * git add .
                * git ci -m 'fix conflict'
            * git flow hotfix finish 0.2.1
            * git push --all
            * git push --tags
* feature 開發 (git 原生 command)
    * (user) phase 1下面User 修改 後 push
        * git clone git@github.com:charlietag/git-flow-usage.git
            * git remote update # 更新一下，才知道 remote status，不然會常常奇怪，github 上有的 branch, 怎麼 git branch -a 看不到
            * git branch -a | grep '^\s*remotes' | egrep --invert-match '(:?HEAD|master)$' | while read branch; do echo "${branch#remotes/origin/}" "$branch"; done
            * git branch -a | grep '^\s*remotes' | egrep --invert-match '(:?HEAD|master)$' | while read branch; do git branch --track "${branch#remotes/origin/}" "$branch"; done
                * git fetch --all
                * git pull --all
        * git checkout feature/function_b
            * echo "function B from userA" > function_b_user
            * git add .
            * git ci -m 'add file function_b_user from user A'
            * git push  
                * #預設 git push ---> git push origin {current_branch} ; 
                * 預設 git pull ---> git pull --all; 所以通常 pull 要指定 git pull origin {branch} 
                * 不管 pull / push ，還是習慣指定 branch , or --all
                    * 好像 git flow init 過後，我的預設全部 pull ，被 rewrite 了 (應該是，路徑太複雜 如 featurre/xxx , 他git 比對不到...)
    * (user) phase 2下面User 修改 後 push
        * git remote update # 更新一下，才知道 remote status，不然會常常奇怪，github 上有的 branch, 怎麼 git branch -a 看不到
        * git br --track release/0.2.0 remotes/origin/release/0.2.0
        * git fetch --all
        * git pull --all
            * echo "this line is from user a" >> README.md
            * git add .
            * git ci -m 'add desc line from user a'
            * git push
* feature 開發 (git flow command) <--- 感覺比較不方便，只有中央控管的使用 git flow tool 比較實用
    * (user) phase 3下面User 修改 後 push
        * git flow feature pull origin function_c <--- 先跑這個會有問題，因為他不會幫你設定 .git/config
        * git flow feature track function_c
            * 他幫你做了
            * git remote update
            * git branch --track feature/function_c remotes/origin/feature/function_c
            * git co feature/function_c
                * 
            * git ci -m 'add file function_c_user from user A'
            * git remote update
            * git push
    * (user) phase 4下面User 修改 後 push
        * git flow release track 0.3.0
            * 
        * echo "This line is from USER A for version 0.3.0" >> Readme.md
        * git add .
        * git ci -m 'add desc line by User A'
        * git push
