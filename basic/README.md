### Git 倉庫中記錄變更
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

##### 檢視目前檔案狀態
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

##### 追蹤新檔案
```shell=
git add {file}
```

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

##### 忽略檔案
`.gitignore` 檔案中寫入要被忽略的檔案
寫入 `.gitignore` 檔案中的規則如下
- 空行或以`#` 開始的行會被忽略
- 支援 `glob` 模式
- 以`/`開頭的模式可用於禁止遞迴比對
- 以`/`結尾的模式表示目錄
- 以`!`開始的模式表示相反

##### 提交變更
```shell=
git commit
git commit -a #跳過暫存區
```
![](https://i.imgur.com/iAoMlhs.png)
從 c1 的點連續 `commit` 兩次的結果

