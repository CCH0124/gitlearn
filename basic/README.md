## Git 倉庫中記錄變更
工作目錄下的檔案處於兩種狀態
- tracked
    - 指上次快照中包含的檔案，可分為三類
        - 未修改
        - 以修改
        - 已暫存
- untracked
    - 工作目錄中除去以追蹤檔案之外的所有檔案

```sequence
未跟蹤->已暫存:添加檔案
未修改->已修改:編輯檔
已修改->已暫存:暫存檔
未修改->未跟蹤:移除檔
已暫存->未修改:遞交
```

### 檢視目前檔案狀態
```shell=
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md
        basic/

nothing added to commit but untracked files present (use "git add" to track)
```
會輸出 Untracked files 或 tracked 檔案

### 追蹤新檔案
```shell=
git add {file}
```

範例
```bash
$ git add README.md
$ git status 
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   README.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        basic/
```

### 忽略檔案
`.gitignore` 檔案中寫入要被忽略的檔案
寫入 `.gitignore` 檔案中的規則如下
- 空行或以`#` 開始的行會被忽略
- 支援 `glob` 模式
- 以`/`開頭的模式可用於禁止遞迴比對
- 以`/`結尾的模式表示目錄
- 以`!`開始的模式表示相反

### 提交變更
```shell=
git commit
git commit -a #跳過暫存區
```
![](https://i.imgur.com/iAoMlhs.png)
從 c1 的點連續 `commit` 兩次的結果

範例
```bash
$ git commit -m "git learn"
[master (root-commit) a81fadb] git learn
 2 files changed, 72 insertions(+)
 create mode 100644 README.md
 create mode 100644 basic/README.md
```

### 移除檔案
```shell=
$ git rm file # 移除狀態會記錄至暫存區
$ git rm --cached file #不讓 git 追蹤，且忘記設置至 .gitignore
```

範例
```bash
$ git add basic/error.file 
$ git commit -m "add error file"
[master faa4a0b] add error file
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 basic/error.file
$ git log --graph
* commit faa4a0b6fae3f360e80dc0079b454dc5f7df5a9d (HEAD -> master)
| Author: CCH0124 <cchong0124@gmail.com>
| Date:   Sun Dec 5 18:21:05 2021 +0800
|
|     add error file
|
* commit a81fadb432771845c61efcaaf010a55e16d41b47
  Author: CCH0124 <cchong0124@gmail.com>
  Date:   Sun Dec 5 18:19:23 2021 +0800

      git learn
$ git rm basic/error.file 
rm 'basic/error.file'
$ git status 
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        deleted:    basic/error.file

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   basic/README.md
```

### 檢視遞交紀錄
```shell=
git log -p -2 # 顯示近兩次遞交，並輸出差異（帶有 diff 資訊）
git log --stat
git log --pretty=[oneline、format、--graph(配合 format)] #自定義輸出格式
```
>在 git log 上，只會顯示當前分支的相關提交

`--stat` 會有以下內容
- 改動的檔案列表
- 共有多少檔案被改動
- 檔案裡有新增行和刪除列
- 輸出總計資訊

範例
```bash
$ git log --stat --graph
* commit faa4a0b6fae3f360e80dc0079b454dc5f7df5a9d (HEAD -> master)
| Author: CCH0124 <cchong0124@gmail.com>
| Date:   Sun Dec 5 18:21:05 2021 +0800
|
|     add error file
|
|  basic/error.file | 0
|  1 file changed, 0 insertions(+), 0 deletions(-)
| 
* commit a81fadb432771845c61efcaaf010a55e16d41b47
  Author: CCH0124 <cchong0124@gmail.com>
  Date:   Sun Dec 5 18:19:23 2021 +0800

      git learn

   README.md       |  0
   basic/README.md | 72 ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
   2 files changed, 72 insertions(+)
```


```shell=
git reflog # 觀察詳細歷史紀錄，曾經刪除的紀錄都會顯示
```
範例
```bash
$ git reflog 
faa4a0b (HEAD -> master) HEAD@{0}: commit: add error file
a81fadb HEAD@{1}: commit (initial): git learn
```

### 取消操作
```shell=
git commit --amend # 重新嘗試遞交
```
執行後會進到編輯器，並顯示上次提交的資訊

範例
```bash
$ git add basic/amend.file
$ git commit -m "add amend file"
[master 1bf2c4b] add amend file
 2 files changed, 1 insertion(+)
 create mode 100644 basic/amend.file
 delete mode 100644 basic/error.file
 $ git commit --amend
[master e6c7f99] add amend file and delete error file
 Date: Sun Dec 5 18:33:01 2021 +0800
 2 files changed, 1 insertion(+)
 create mode 100644 basic/amend.file
 delete mode 100644 basic/error.file
$ git log -p -1
commit e6c7f99dd1920996c3ae0b5694d98b6b9b701c94 (HEAD -> master)
Author: CCH0124 <cchong0124@gmail.com>
Date:   Sun Dec 5 18:33:01 2021 +0800

    add amend file and delete error file

diff --git a/basic/amend.file b/basic/amend.file
new file mode 100644
index 0000000..3c4ff57
--- /dev/null
+++ b/basic/amend.file
@@ -0,0 +1 @@
+amend test
\ No newline at end of file
diff --git a/basic/error.file b/basic/error.file
deleted file mode 100644
index e69de29..0000000
```
### 取消已暫存的檔案
`git reset` 把分支的參考點退回到上一個 `commit` 來取消修改。你可以認為這是在"重寫歷史"。`git reset` 往回移動 `branch`，原來的 `branch` 所指向的 `commit` 好像從來沒有存在過一樣。
```shell=
git reset HEAD <file> # 只是更改暫存區，檔案會留在工作目錄上
git reset HEAD^ --hard # 連同檔案都不存取，^ 表示還原一個版本
```

針對要還原的動作時可採用，接上 `--hard` 可以連同檔案都刪除
:::info
Git Revert
雖然在你的 local branch 中使用 git reset 很方便，但是這種「改寫歷史」的方法對別人的 remote branch 是無效的哦！
:::

範例
```bash
$ git add basic/CONTRIBUTING.md
git reset HEAD basic/CONTRIBUTING.md
Unstaged changes after reset:
M       basic/CONTRIBUTING.md
M       basic/README.md
$ git status 
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   basic/CONTRIBUTING.md
        modified:   basic/README.md

no changes added to commit (use "git add" and/or "git commit -a")
```

### Example
初始題目，要完成這一關，切換到 bugFix 的 parent commit。這會分離出 HEAD。
![](https://i.imgur.com/aHY4t4W.png)

切換到 bugFix 
```shell=
git checkout bugFix
```
![](https://i.imgur.com/RKYzsgO.png)

切換到 parent commit
```shell=
git checkout HEAD^
```
![](https://i.imgur.com/5I6Y64x.png)

但這邊可以一次解決
```shell=
git checkout bugFix^
```

### 取消對檔案的修改
```shell=
git checkout -- {file}
```
`checkout <commit hash>` 會將 HEAD 指向該 commit 位置。
`checkout` 會伴隨著 `HEAD` 和 `分支名稱`。
可前往 commit 點或分支
:::danger
危險指令
:::

:::info
在 Git 遞交的任何變更幾乎總是可以進行恢復。
:::

:::info
`HEAD` 是一個 `reference`，它是指向目前所 `checkout` 的 `commit`，基本上，其實就是你目前所在的 `commit`。

在 `commit` tree 中，`HEAD` 總是指向最近的一次 `commit`。大部份 git 的指令如果要修改 `commit` tree 的狀態的話，都會先改變 `HEAD` 所指向的 commit。

`HEAD` 通常指向一個 `branch` 的名稱（比如 bugFix）。當你 `commit` 的時候，改變了 `bugFix` 的狀態，這一個變化可以從 `HEAD` 的改變中看到。
:::

:::info
"~" 符號
假設需要在 commit tree 中向上移動多個 commit。使用太多 ^ 會非常討人厭，所以 Git 也加入了波浪（~）符號。

波浪符號後面可以選擇一個數字（你也可以不選擇），該數字可以告訴 Git 我要向上移動多少個 commit。

:::

### revert
在 git 裡主要用兩種方法來取消修改，一種是 `git reset`，另外一種是 `git revert`。

雖然在你的 local branch 中使用 git reset 很方便，但是這種「改寫歷史」的方法對別人的 remote branch 是無效的哦！

為了取消修改並且把這個狀態分享給別人，我們需要使用 `git revert`。

##### revert Example
要完成這一關，分別取消 local branch 和 pushed branch 上的最近的一次 commit。

記住 pushed 是一個 remote branch，local 是一個 local branch，有了這麼明顯的提示應該知道要用哪種方法了吧？

初始
![](https://i.imgur.com/qOpoPgb.png)

將 local c3 reset 至 c1
```shell=
git reset c1
```
![](https://i.imgur.com/eEbCNBe.png)

切換至 pushed 分支
```shell=
git checkout pushed
```
![](https://i.imgur.com/9fqEktZ.png)

執行 revert

```shell=
git revert c2
```
![](https://i.imgur.com/783gKvj.png)

### 移動 commit git cherry-pick
想要複製幾個 commit 並且接在你目前的位置（HEAD）下面的時候，這會是一個非常直接的方式。

##### Example

初始題目，要完成這個關卡，只需要從三個 branch 複製幾個 commit 到 master 下面，你可以從視覺化的目標看到我們需要哪些 commit。

![](https://i.imgur.com/uKC0Cou.png)
```shell=
git cheyyy-pick c3 c4 c7
```
![](https://i.imgur.com/5wOs0MR.png)


### 分離 HEAD
分離 `HEAD` 就是讓其指向一個 `commit` 而不是 `branch` 的名稱。這是指令執行之前的樣子：

`HEAD -> master -> C1` 執行 `git checkout C1` 就變成 `HEAD -> C1`
![](https://i.imgur.com/N7PoW24.png)

##### Example
從 bugFix 分離出 HEAD 並且讓它指向一個 commit。
透過 hash 值可以指定 commit。每個 commit 的 hash 值顯示在各自的圓圈中。

初始情境
![](https://i.imgur.com/nqa5Ajd.png)

將 HEAD 分離
```shell=
git checkout c4
```
![](https://i.imgur.com/e5jpvv3.png)
