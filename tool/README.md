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
2. 
