## 選擇修訂版本
### 單一修訂版本
1. 短格式 SHA-1
```bash
$ git log # 檢視遞交
commit 86244eef8ef42ae4215ce5d30ba9058e999f83a8 (HEAD -> testing)
Author: CCH0124 <cchong0124@gmail.com>
Date:   Mon Feb 28 00:58:33 2022 +0800

    made change

commit 90fd25929dccc0498b365bb04b0cfa0d032feabb
Author: CCH0124 <cchong0124@gmail.com>
Date:   Mon Feb 28 00:50:09 2022 +0800

    made a change
```
加上 `--abbrev-commit` 參數時，在沒有重複下會採取如下得更簡短的值。通常會以 8 個字元作為 image 的 tag。
```bash
$ git log --abbrev-commit --pretty=oneline
86244ee (HEAD -> testing) made change
90fd259 made a change
b4a48de add branch
253e835 (tag: v0.0.1) add git basic
af2ecc6 finish basic git
48336c9 del CONTRIBUTING.md
8f6bc4f aff CONTRIBUTING file
e6c7f99 add amend file and delete error file
faa4a0b add error file
a81fadb git learn
```
2. 分支參考
顯示分支最後一次遞交，以上面範例來說 `86244ee` 這個物件是指向 `testing` 分支因此以下兩個相同
```bash
git show 86244ee
git show testing
```
3. reflog
git 在作用同時，後臺會儲存一份 `reflog`，這是一份最近的 `HEAD` 和分支參考記錄檔。只要當前 `HEAD` 因為某些原因被修改時，git 會將對應的資訊儲存在這些臨時歷史紀錄中。
```bash
$ git reflog
86244ee (HEAD -> testing) HEAD@{0}: checkout: moving from main to testing
f6b6c00 (main) HEAD@{1}: checkout: moving from testing to main
86244ee (HEAD -> testing) HEAD@{2}: commit: made change
90fd259 HEAD@{3}: checkout: moving from main to testing
f6b6c00 (main) HEAD@{4}: commit: made java change
c14401d (origin/main) HEAD@{5}: checkout: moving from testing to main
...
```
我們可使用 `@{n}` 代表 reflog 的輸出。
```bash
$ git show HEAD@{5}
commit c14401d645331ec8f210f03413175f9db03a6b13 (origin/main)
Author: CCH0124 <cchong0124@gmail.com>
Date:   Mon Feb 28 00:37:04 2022 +0800

    update

diff --git a/branch/README.md b/branch/README.md
index 12f0102..c6d61a5 100644
--- a/branch/README.md
+++ b/branch/README.md
...
```
此方法也可用於檢視分支從前的位置，下面表示 testing 分支昨天的 HEAD 位置。
```bash
$ git show testing@{yesterday}
warning: Log for 'testing' only goes back to Mon, 28 Feb 2022 00:32:38 +0800.
commit b4a48de53586bedeb8cb08b302d191dee8a8864a
Author: CCH0124 <cchong0124@gmail.com>
Date:   Sun Dec 12 18:03:56 2021 +0800

    add branch

diff --git a/branch/README.md b/branch/README.md
...
```
>這些東西只要存在於 reflog 都可以操作。reflog 只存在於本機，因此就算複製了一份，會因為沒有操作而導致內容為空。
4. 祖先參考
透過`^`，可以讓 git 知道說要的是此遞交的父遞交。下面範例當前 `HEAD` 是 `86244ee` 遞交物件。
```bash
$ git show HEAD^ --abbrev-commit
commit 90fd259
Author: CCH0124 <cchong0124@gmail.com>
Date:   Mon Feb 28 00:50:09 2022 +0800

    made a change
...
$ git show HEAD^^ --abbrev-commit # 當前遞交的遞交的遞交
commit b4a48de
Author: CCH0124 <cchong0124@gmail.com>
Date:   Sun Dec 12 18:03:56 2021 +0800

    add branch
...
$ git show HEAD~2 --abbrev-commit
commit b4a48de
Author: CCH0124 <cchong0124@gmail.com>
Date:   Sun Dec 12 18:03:56 2021 +0800

    add branch
...
```
> 其中 `~` 和 `^` 使用上是相同的

## 遞交範圍
上述可以指定個別遞交。當如果擁有大量的分支可以透過範圍來解決像是分支上有哪些工作尚未合併到主分支等問題。
1. .. 雙點號
讓 git 找出那些不在同一分支上的遞交。
```
A <- B <- E <- F <- master
      \
       <- C <- D <- featureA
```
下面表示了可從 featureA 分支獲取而不能從 master 分支中獲取的分支
```bash
git log master..featureA
D
C
git log featureA..master
F
E
```
因此想要知道當前分支尚未遞交到原端 master 分支的遞交時可使用此方式。
2. 多點
想檢視兩個以上分支遞交
```bash
git log refA refB ^refC
git log refA refB --not refC
```
3. ... 三點
檢視非共有的遞交，加上 `--left-right` 可以顯示屬於哪一側分支的遞交
```bash
git log master...featureA
F
E
D
C
git log --left-right master...featureA
<F
<E
>D
>C
```

## 儲藏與清理
