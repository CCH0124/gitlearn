- 分支表示偏離開發主線並繼續你自己的工作而不影響主線開發
- Git 採用一系列快照的方式儲存資料。類似以 C 語言指標參考一樣
- Git 分支只不過是一個指向某次遞交的輕量級的可移動指標。
- Git 預設分支為 `master`。只要有遞交動作，會產生一個指向最後一次遞交的 `master` 分支

![](https://i.imgur.com/Ir31bws.jpg)
其中 98ca9 第一次 commit，34ac2 第二次 commit ...

### 建立新分支

建立一個指標去使用。
```shell=
git branch [branch-name]
```

![](https://i.imgur.com/cNlN0aZ.png)


Git 會使用 `HEAD` 的指標維護著目前處在哪一個分支。在 Git 中 `HEAD` 是一個指向目前所在的本機分支指標。當使用 `git branch` 時，只會建立分支，並不會切換到建立的分支，因此 `HEAD` 指向 `master`。



```shell=
git log --oneline --decorate # 目前所指的分支
841b435 (HEAD -> master, test) fix
6abe997 add
```

### 切換分支
此指令會改變 `HEAD` 指向的位置。
```shell=
git checkout [branch-name]
```

![](https://i.imgur.com/e5d423v.jpg)

```shell=
$ git log --oneline --decorate
841b435 (HEAD -> test, master) fix
6abe997 add
```

假設在此切換分支進行一次遞交。

![](https://i.imgur.com/EvjwWKX.png)

如果再用 `checkout` 切換至 `master` 時，`HEAD` 會指向 `master`。但除了指向，還會把工作目錄的檔案修復至 `master` 分支指向的快照狀態（最後一次遞交的狀態）。


如果切換至 master 後再將檔案做修改（同一檔案），由於是不同分支因此互相分離。

![](https://i.imgur.com/NV6SBbC.png)


使用 `git log` 驗證
```shell=
$ git log --oneline --decorate --graph --all
* 3b5df26 (HEAD -> master) change two
| * 3e8f636 (test) change
|/
* 545bb26 first
* 841b435 fix
* 6abe997 add
```

**在切換分之時，工作目錄檔案會被變動。當切換較舊的分支，其工作目錄會被恢復到該分支上最後一次 commit 的狀態。當 git 在當前狀態無法乾淨的完成恢復動作，就不會允許切換分支。**


### Branch forcing
我使用相對引用最多的就是移動分支。你可以使用 `-f` 選項直接讓分支指向另一個 `commit`。舉個例子:
```shell=
git branch -f master HEAD~3
```
（強制）移動 master 指向從 HEAD 往上數的第三個 parent commit。



##### Example
![](https://i.imgur.com/Ho1l20Z.png)

新增一個 branch
```shell=
git branch bugfix
```
![](https://i.imgur.com/Cu0HN4w.png)

切換分支
```shell=
git checkout bugfix
```
![](https://i.imgur.com/9lghwP9.png)


##### Branch forcing Example
移動 HEAD，master 和 bugFix 到目標所示的位置。
初始題目
![](https://i.imgur.com/BFYNoyQ.png)

讓 `master` 指向 `c6`
```shell
git branch -f master  c6
```

`bugFix` 指向 `c0`
```shell=
git branch -f bugFix  c0
```

![](https://i.imgur.com/GmMjEcN.png)

`HEAD` 指向 `c1`

```shell=
git checkout c1
```
![](https://i.imgur.com/jXXYgZn.png)

## 基本分支與合併操作
在切換分支須注意，*如果工作目錄或暫存區存在未遞交的更改，且這些更改宇要切換的分支衝突，Git 不會允許切換分支*。當然可以使用 `stash` 來繞過。

下面是一個從 master 分支化出分別為 iss53 和 hotfix 分支的流程，並使用 merge 進行合併。

```shell=
$ git log --oneline --decorate --graph --all
* 2bef476 (HEAD -> hotfix) added email
| * 3437bb9 (iss53) added a new fotter [iss53]
|/
* 3b5df26 (master) change two
* 545bb26 first
* 841b435 fix
* 6abe997 add
$ git checkout master
Switched to branch 'master'
$ git merge hotfix
Updating 3b5df26..2bef476
Fast-forward
 index.html | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 index.html
$ git log --oneline --decorate --graph --all
* 2bef476 (HEAD -> master, hotfix) added email
| * 3437bb9 (iss53) added a new fotter [iss53]
|/
* 3b5df26 change two
* 545bb26 first
* 841b435 fix
* 6abe997 add
```
![合併前](https://i.imgur.com/FOnctba.png)

![合併後](https://i.imgur.com/DkuYOul.png)

合併後可以使用 `git branch -d {BRANCH_NAME}` 刪除分支。此時的 iss53 分支沒有 hostfix 的修改內容，因此需要在 iss53 分支將 master 進行 merge。

:::info
fast-forward，試圖合併兩個不同的遞交，而順著其中一個遞交的歷史可以直接到達另一個遞交時，Git 就會簡化合併操作，直接把分支指標向前移動，因為此單線歷史不存在分歧工作。
:::

### 基本的合併操作
假設再從上面切換至 iss53 分支，再遞交一個 commit，接著用 master 合併 iss53

但這次合併有了分叉（C2）。此時要用三方合併。

![](https://i.imgur.com/jkRfgOT.png)

C2 為共同祖先
C4 被併入的遞交快照
C5 要合併的遞交快照

Git 會以三方合併為基礎的結果建立新的快照，接著再建立一個遞交指向新增的快照。合併遞交的特殊性在於它擁有不只一個父遞交。

![合併遞交](https://i.imgur.com/Y4nPMXc.png)

:::info
三方合併操作會使用兩個待合併分支上最新遞交的快照，以及這兩個分支的共同祖先的遞交快照。
可以用 git cat-file -p {c6HASH} 去觀察
:::

>此時會有 ORIG_HEAD 可用作回滾用。`git reset ORIG_HEAD`

##### Example
1. 建立新的 branch，叫做 bugFix
2. 用 git checkout bugFix 切換到 bugFix branch 
3. commit 一次
4. 用 git checkout 切換回 master branch
5. 再 commit 一次
6. 用 git merge 將 bugFix merge 到 master

前兩個用以下完成
```shell=
git checkout -b bugfix
```
![](https://i.imgur.com/yZLYD6b.png)

git commit 一次
![](https://i.imgur.com/v3d8t01.png)

用以下指令切回 `master`
```shell=
git checkout master
```
![](https://i.imgur.com/zOqiUej.png)

git commit 一次

![](https://i.imgur.com/mVxj4Mo.png)

用以下指令完成 `merge` 動作
```shell=
git merge bugfix
```
![](https://i.imgur.com/gQJxMMy.png)

### 基本的合併衝突處裡
因為共同修改了同一檔案的同一部分
```shell=
$ git merge iss53
Auto-merging index.html
CONFLICT (add/add): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```
檢視那些檔案沒被合併
```shell=
$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

        both added:      index.html

no changes added to commit (use "git add" and/or "git commit -a")
```

使用 cat 指令看衝突檔案
```shell=
$ cat index.html
<<<<<<< HEAD
s1222@gmail.com
=======
<h1>hello</h1>

WTF
>>>>>>> iss53
```
其中 ======= 上為 `HEAD` 下為 iss53。取決於開發者要選擇留下那一個，選擇完後執行`git add`增加至暫存區

### 分支管理
\* 目前所在分支，及 HEAD 指向的分支
```shell=
git branch # 取得目前所有分支
git branch -v # 查看每個分支的最新遞交
git branch -d # 刪除分支，需要將包含主線的資料給合併
git branch -D # 強制刪除分支
git branch [--merged|--no-merged] # 篩選已併入當前分支的所有分支和篩選尚未併入當前分支的所有分支
```

## 與分支有關的工作流

### 長期分支
- 長期
### 主題分支
- 短期
- 實現某一特定功能
- 相關工作

### 遠端分支
指向遠端倉庫的分支指標，當與伺服器進行任何網路通訊，它們會自動更新。想像書籤，會記錄著上一次連接伺服器時遠端倉庫中每個分支的位置。

遠端分支表示 -> `(remote)/(branch)`。

![遠端倉庫與複製下來本機倉庫](https://i.imgur.com/SPD8iHZ.jpg)

假設，遠端的倉庫有人更新了 master 的位置，本地的遞交歷史與伺服器的歷史就會產生偏移。

要與伺服器同步，須執行
```shell=
git fetch origin
```
查詢 origin 對應的伺服器地址，並從伺服器取得本機尚未包含的資料，然後更新本機資料庫，最後把 `origin/master` 指標移動至最新的位置上。

### 發送

```shell=
git push (remote)(branch)
```

> --set-upstream 是用來與遠端的倉庫進行關聯

### 追蹤分支
以遠端分支建立為基礎的本機分支會自動成為追蹤分支（tracking branch）或有時候也叫做上游分支（upstream branch）。
```bash
$ git checkout --track origin/serverfix
```
試圖執行分支切換操作時，如果該分支尚未被建立，並且該分支名稱和某個遠端分支名稱一致，那麼 git 會幫你建立追蹤分支。

### 擷取
`git fetch` 會擷取本機沒有的遠端所有最新更改資料，此指令完全不會更改你工作目錄。

### 刪除遠端分支
可能不再需要包含這個功能的遠端分支。
```shell=
git push origin --delete [BRANCH_NAME]
```
刪除了遠端分支指標，git 會保留一段時間，直到處發垃圾回收。原則上還是可以復原的。

## 變基（rebase）
要把更改從一個分支整合到另一個分支，有兩種方式
- merge
- rebase

`merge` 對兩個分支的最新遞交快照(C3、C4)以及此兩個遞交的共同祖先(C2)，進行三方合併，並建立一個新的合併遞交。 
![](https://i.imgur.com/1kHWgop.png)

:::info
六角學院：
rebasing 是 merge branch 的第二種方法。rebasing 就是取出一連串的 commit，"複製"它們，然後把它們接在別的地方。

雖然聽起來難以理解，rebasing 的優點是可以建立更線性的 commit history。假如只允許使用 rebasing 的話，則我們的 repo 中的 commit log 或者是 commit history 會更加簡潔好看。
:::

### 基本的變基操作

首先找到兩個要整合的分支（目前所在分支和要整合到的分支）的共同祖先，然後取得目前所在分支的每次遞交引用的更改（diff），並把這些更改儲存為暫存檔，這之後將目前分支重製為要整合到的分支，最後在該分支上依次引用之前儲存的每個更改。

```shell=
$ git checkout experiment
$ git rebase master
```
![把 C4 遞交的更改應用到 C3 遞交](https://i.imgur.com/c3EvadA.jpg "把 C4 遞交的更改應用到 C3 遞交")
進行合併後
```shell=
$ git checkout master
$ git merge experiment
```
![](https://i.imgur.com/3aWNViv.jpg)


使用 rebase 方式可以獲得更簡潔的遞交歷史，看起來像一條線。

總而言之乾淨俐落整合。不管使用 `merge` 還是 `rebase` 這兩個遞交的快照內容是完全一樣的。

`rebase` 是把某一條開發分支線上的工作在另一個分支線上按順序重現。
`merge` 是找出兩個分支的末端，並把它們合併到一塊。

**不要對已經存在於本機倉庫之外的遞交執行 rebase 操作，即不要對已經發送至遠端伺服器的公開遞交進行 rebase 操作** 原因是執行 rebase 操作時，實際上是拋棄了已有的某些遞交，隨後建立了新的對應遞交。

### rebase 與 merge
通常來說結合兩種操作方式是，對本機尚未發送的更改進行 `rebase` 操作，簡化歷史遞交，但絕不能對任何以發送到伺服器的更改進行 `rebase` 操作。


##### Example
1. 建立 bugFix branch
```shell=
git checkout -b bugFix
```
![](https://i.imgur.com/JdLA8re.png)
2. commit 一次
```shell=
git commit
```
![](https://i.imgur.com/1TFBjH1.png)
3. 切換回 master branch 再 commit 一次
```shell=
git checkout master
```
![](https://i.imgur.com/efBcGAI.png)
```shell=
git commit
```
![](https://i.imgur.com/vw6Omtw.png)
4. 再次切換到 bugFix branch，接著 rebase bugFix 這個 branch 到 master branch 上
```shell=
git checkout bugFix
```
![](https://i.imgur.com/tGKjrTu.png)
```shell=
git rebase master
```
![](https://i.imgur.com/IsalpA2.png)


