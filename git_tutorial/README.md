# Git 使用教學
本文記錄了Git常用指令。

## 什麼是Git?
Git是一個版本管控的程式，它可以幫我們管理專案的版本。概念上就好像我們在玩遊戲，我們拿到很稀有的道具，在這個時候保留一個存檔點，假如未來我們不小心弄丟那個道具，這時我們可以使用那個存檔點來救回弄丟的武器和道具。

Git在版本管控上不僅執行很快，而且有些版本管控的程式還需要架設伺服器才能使用，只要在電腦安裝Git這個小程式就能做版本管控。如果有興趣的話，可以去[維基](https://zh.wikipedia.org/wiki/Git)看看Git的說明。

最後，給一個小建議，雖然Git也有GUI介面，只要按按鈕就可以做版本管控，但是在學習怎麼使用Git時，最好還是在文字介面的終端機中敲敲鍵盤輸入指令。這麼做的確有點痛苦，可是熬過去後，在使用GUI版的Git時，才會比較明白每個按鈕背後到底做了哪些事情，否則Git管理的專案很有可能會亂成一團。

## 使用Git修改專案的設定
### 使用者設定
在開始使用Git管理專案之前，要先設定好使用者的名稱和Email。要先設定使用者名稱和Email的原因是，當一個專案有多個人在維護時，需要區分每次修改是由誰負責的，未來專案中出現錯誤時，比較容易找到問題點。

請開啟終端機，並且輸入下面兩行指令：
```bash
git config --global user.name "Joe"
git config --global user.email "joe@test.tw"
```
輸入完成後，就可以設定目前使用的電腦在Git管理專案的使用者名稱和Email，而且這個設定可用在該電腦上所有Git管理的專案。

如果只想針對一個Git管理的專案設定特定的使用者名稱和Email，可以使用下面的指令：
```bash
git config --local user.name "Joe"
git config --local user.email "joe@email.tw"
```
重點是`git config`後面接的參數`--global`，只要有這個參數，就可以修改全域的參數。在`git config`後面接`--local`就可以只針對當前的專案調整設定。

### 更換編輯器
使用Git管理專案時會需要使用編輯器，Git也允許使用者更換編輯器，以下面指令範例是把編輯器更換成Vim：
```bash
git config --global core.editor vim
```
Git預設使用的編輯器是Vim，如果有需要也可使用GUI的編輯器，像是Notepad++。不過，本文採用的編輯器會是Vim。

### 顯示當前專案更動的設定
修改完專案的設定，可以使用下面的指令來查看設定值：
```bash
git config --list
```

## 建立專案給Git管理
接下來我們來建立一個專案，並且使用Git來管理這個專案。我們先建立一個專案，第一步先建一個資料夾，用專案的名稱來為這個資料夾命名。我們以`testGit`來當作專案名稱，在終端機中可以輸入下面的指令來建立專案資料夾：
```bash
mkdir testGit
```
建立好之後就切換進`testGit`中，我們也可以在終端機中輸入指令來切換當前的目錄：
```bash
cd testGit
```

建立好專案並且進到專案資料夾後，我們需要使用下面的指令來初始化專案：
```bash
git init
```
使用上面的指令後，應該會發現專案的資料夾中會出現`.git`的目錄，這個目錄就是Git能對專案進行版本管控的關鍵。我們使用Git對專案版本管控時，就是把專案中每次改動都記錄到`.git`中。也就是說，**只要把`.git`目錄刪掉，Git就沒辦法再對該專案進行版本管控。**

## 讓Git接管檔案的版控
### Git管理檔案的機制
Git之所以能針對一個專案中每個檔案進行版本管控的原因在於，它在檔案的每一次更動中，比較當前更動的內容與更動前的內容之間的差異，然後把這個差異記錄下來，而不是儲存每次更動的內容。這麼做的好處是執行效率高，而且產生出來用於版本管控的檔案也比較小。

概念上就好像是先對檔案拍張完整的照片，每次內容有異動時只針對局部有變動的地方拍照，並記錄這些變動的位置。我們只需要記下這些變動的位置和變動的內容，就可以做到版本管控，而且這些變動資訊比起每次紀錄一份完整的檔案的照片少很多。也因為如此，我們實際上要保留的資訊可以很少，卻能做到版本管控。

Git在對檔案進行版本管控時，就如同把檔案在三個不同的區域中搬移，這三個區域如下：
- 工作目錄(Working Directory): 在這個區域下，我們可以編輯任何檔案，也可以新增檔案，也可以刪除檔案
- 暫存區(Staging Area): 在這個區域中，會存放已經更動過內容的檔案，也包含新增的檔案，需要使用`git add`才能把工作目錄下更動過的檔案搬移到這裡
- 儲存庫(Repository): 已經更動過內容的檔案最終存放的位置，需要使用`git commit`來把暫存區的檔案移動到這裡

在Git中目錄`.git`就是上述提到的儲存庫，網路上有些網站可能會使用`Repositotry`的縮寫`repo`來代表儲存庫。有了這三個區域，Git會以兩階段式來處理每個檔案：
- 第一階段： 在工作目錄下編輯檔案，在確定不再編輯時把這些編輯過的檔案都搬移到暫存區中
- 第二階段： 當我們已經把所有編輯過的檔案搬到暫存區，而且已經不再編輯同專案的其他檔案時，我們就要把檔案從暫存區搬移到儲存庫，並且留些訊息說明這次專案的變動有哪些檔案更動過，以及哪些內容更動過
  
### 確認檔案狀態:`git status`
Git管理專案中每個檔案時，我們都會使用如記事本之類的編輯器編輯程式碼，等確定編輯好了，就會讓Git幫我們儲存編輯好的檔案。我們可以使用下面的指令來確認當前專案下有沒有檔案更動過：
```bash
git status
```
如果照著本文下指令的話，目錄`testGit`中除了隱藏目錄`.git`以外，沒有其他檔案和資料夾，使用上面的`git status`會回報說沒有檔案更動過：
```bash
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)

```

現在我們嘗試建立一個檔案`Readme.md`，讓Git管理該檔案，該檔案的內容如下：
```
Hello world!
This is Readme.md
```
此時目錄`testGit`中就存在一個檔案`Readme.md`，如果我們輸入`git status`，就會得到下面的訊息：
```bash
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        Readme.md

nothing added to commit but untracked files present (use "git add" to track)

```
上面的訊息中最重要的是`Untracked files`，在這行下面會列出檔名`Readme.md`，這是要告訴我們到目前為止Git都還沒管理檔案`Readme.md`，而且該檔案還是新建立的。

### 讓Git管理檔案的版本:`git add`
我們在前一小節中建立了檔案`Readme.md`，也使用`git status`確認過，`Readme.md`還沒讓Git幫我們管理。那麼我們該如何讓Git管理新檔案呢?

我們可以需要先使用`git add`把檔案放進暫存區中，該指令用法如下：
```bash
git add <filepath>
```
`filepath`是我們想讓Git管理的檔案路徑，現在我們只有一個檔案`Readme.md`需要讓Git管理，我們可使用下面的指令：
```bash
git add Readme.md
```
使用上面的指令後，我們使用`git status`來確認看看當前專案的狀態：
```bash
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   Readme.md

```
從上面的訊息中`Changes to be committed:`這行以及下面列出的檔名告訴我們，我們成功把檔案`Readme.md`放到暫存區中。

假如我們修改了多個檔案，或是新增多個檔案時，比方說有十個檔案，難道我們就要連續使用`git add`十次嗎?其實可以不必這麼麻煩，我們可以使用下面的指令，git會幫我們找出所有新建的檔案，以及已經修改過的檔案，然後把這些檔案放進暫存區中：
```bash
git add .
```
上面指令中最關鍵的是`.`，在Bash中`.`代表當前目錄的意思，我們把它當作`<filepath>`給`git add`使用，就能一次把所有修改過和新增過的檔案放進暫存區中。

### 提交檔案的更動訊息:`git commit`
我們使用`git add`把變更過的檔案搬移到暫存區，並不代表該檔案已經讓Git管理，我們還需要使用`git commit`來把這次的更動提交出去，指令`git commit`用法如下：
```bash
git commit
# -m <message> :    直接提交這次的更動訊息，後面的<message>就是用來說明這次更動了哪些檔案和更動的原因
# --amend :         使用前一次的提交的更動訊息
```
上面只列出`git commit`常用的兩個參數，其中使用`-m`時後面可以直接輸入這次更動的訊息，但是如果不使用這個參數，Git會開啟編輯器，我們就需要在編輯器輸入更動的訊息。參數`--amend`的用途是直接使用前一次提交的更動訊息，來提交這次的更動。

接下來我們用範例來說明這兩個參數的功能。如果上一小節的範例指令都用照做，暫存區應該會有`Readme.md`，現在我們使用`git commit`來提交這次的更動訊息：
```bash
$ git commit -m "Init commit"
[master (root-commit) 355a48c] Init commit
 1 file changed, 2 insertions(+)
 create mode 100644 Readme.md
```
這邊提醒一下，那個`$`不是真的要輸入的指令，那只是終端機的提示字元，實際上輸入指令時，是要輸入`$`後面那串指令。在輸入完畢，Git就會把`Readme.md`和訊息`Init commit`一併放進儲存庫中。

如果要提交的訊息非常長，可以只使用`git commit`，此時就會跳出編輯器，只要在編輯器中輸入完訊息並且儲存，然後關閉編輯器，Git就會提交這次的訊息。至於Git會跳出什麼樣的編輯器，則依據Git的設定而定，預設使用vim，當然也可以更換編輯器，更換方式請參考[更換編輯器](#更換編輯器)。

接下來用範例來說明參數`--amend`的功能。首先，我們再建立一個檔案`test.txt`，然後使用帶有參數`--amend`的`git commit`：
```bash
$ touch test.txt
$ git add test.txt
$ git commit --amend
[master 9eee82b] Init commit
 Date: Thu Feb 17 10:55:07 2022 +0800
 2 files changed, 2 insertions(+)
 create mode 100644 Readme.md
 create mode 100644 test.txt

```
上面第一行指令在Bash中是用來產生空檔案，下一行指令輸入完畢，會跳出一個編輯器，上面會顯示上一次提交的訊息，也就是`Init commit`這個訊息。當我們儲存畢關閉編輯器時，更動訊息`Init commit`除了包含前面建立的`Readme.md`以外，還有剛剛新增的檔案`test.txt`。後續還會說明`--amend`適合的情境。

接下來說明一下，使用`git commit`時一定要輸入訊息的原因，以及需要輸入什麼樣的訊息才行。`git commit`會要求一定要輸入訊息，否則不會讓我們提交當前更動的檔案到儲存庫裡，原因在於讓我們更方便管理專案。

假設我們在在某個程式碼裡加了幾行程式，那麼我們要如何知道這幾行新加的程式碼的功能呢?雖然我們可以寫些註解來說明那幾行程式碼的功能，也許是為了修正某個BUG，但是有時註解未必能很直接了當的說明新加的程式碼的功能，這個時候我們反而要多浪費時間分析程式碼，又或是執行那段程式碼才明白它的功能。就算那段程式碼是我們自己寫的，可是只要有相當長的時間沒接觸，也可能會忘記。

在這種情況下，既然我們已經使用Git來管理專案了，我們就可以使用更簡單的方法來解決，那就是使用`git commit`提交訊息到儲存庫中。只要用很簡短又明確的訊息來說明，以後我們只要看了這段訊息就知道新加的程式碼的用途，以及什麼時候加了那幾行程式碼。

至於應該要提交什麼樣的訊息，這點只要能用文字明確描述這次提交的訊息中，有哪些檔案更動過，以及為何要更動就可以了。其實只要自己看得懂，提交什麼樣的訊息都可以，當然如果有其他人也共同維護，其他人也最好能看得懂。雖然前面的範例輸入的訊息是英文，其實也可以使用中文。

### 查看歷史紀錄: `git log`
在上一小節中，我們已經把`Readme.md`和`test.txt`提交到儲存庫中，接下來該說明如何查閱我們先前提交的資訊。我們可以使用`git log`來查閱提交的資訊，該指令用法如下：
```bash
$ git log
# --oneline:  每一個提交訊息只以一行顯示
# --graph:    以圖形的方式來呈現
```
`git log`有兩個參數可以使用，第一個參數`--oneline`會把每個提交訊息以一行來顯示，如果想快速查看提交的歷史紀錄，這個會很好用。第二個參數`--graph`則會以文字的方式，來呈現完整的歷史紀錄，其實與直接使用`git log`顯示出來的資訊類似，稍後我們用範例來說明這兩者之間的差異。

現在我們使用`git log`來看看專案`testGit`提交訊息的歷史紀錄。
```bash
$ git log
commit 9eee82b4d028aa45f30913421708efd801938062 (HEAD -> master)
Author: Joe <joe@email.tw>
Date:   Thu Feb 17 10:55:07 2022 +0800

    Init commit
```
`git log`會顯示的資訊有幾個，我們一個一個了解吧。第一行`commit`後面接的那段`74d7f90a2d21fc56fb59efda49252972d6e3888b`是一組十六進位的數字，它是使用SHA-1(Secure Hash Algorithm 1)演算法計算出來的結果，這組數字可以看做是一個編號，在這裡它代表這個提交訊息的編號，後面會詳細說明。在第一行最後面`(HEAD -> master)`，這個也暫時先不管，後面再詳細說明。第二行則顯示提交這個訊息的作者和Email。第三行則是顯示提交這個訊息的時間，最下面的則是這個提交的訊息。

如果我們只想快速查看提交訊息的歷史紀錄和SHA-1的編號，我們可以這樣使用`git log`：
```bash
$ git log --oneline
9eee82b (HEAD -> master) Init commit
```
我們可以看到`git log`會以一行的方式來顯示一個提交的訊息，這包刮SHA-1的編號和訊息。

接下來我們來說明參數`--graph`對`git log`會有什麼樣的輸出資訊。在說明之前，我們再加入一個空檔案`cat.txt`，並提交一次訊息。
```bash
$ touch cat.txt
$ git add .
$ git commit -m "Add cat.txt"
[master b9cd707] Add cat.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 cat.txt
```
然後我們再編輯`test.txt`，並且提交一次訊息。`test.txt`的內容如下：
```
This file is test.
```
接著，我們使用`git add .`來把`test.txt`加進暫存區中，然後使用`git commit`來提交訊息，當Git跳出編輯器時，請輸入下面的訊息：
```
Update test.txt

Update test.txt
```
在做完這些事之後，我們來看看提交訊息的歷史紀錄吧。
```bash
# 使用 git log 來顯示歷史紀錄
$ git log
commit a8b412ecc331c7df85df9c1f6cec2e6a9d092283 (HEAD -> master)
Author: Joe <joe@email.tw>
Date:   Thu Feb 17 13:51:59 2022 +0800

    Update test.txt

    I update test.txt.

commit b9cd7079788b33a7e4191c990a276c59b668e87d
Author: Joe <joe@email.tw>
Date:   Thu Feb 17 11:58:31 2022 +0800

    Add cat.txt

commit 9eee82b4d028aa45f30913421708efd801938062
Author: Joe <joe@email.tw>
Date:   Thu Feb 17 10:55:07 2022 +0800

    Init commit

# 使用git log --graph
$ git log --graph
* commit a8b412ecc331c7df85df9c1f6cec2e6a9d092283 (HEAD -> master)
| Author: Joe <joe@email.tw>
| Date:   Thu Feb 17 13:51:59 2022 +0800
|
|     Update test.txt
|
|     I update test.txt.
|
* commit b9cd7079788b33a7e4191c990a276c59b668e87d
| Author: Joe <joe@email.tw>
| Date:   Thu Feb 17 11:58:31 2022 +0800
|
|     Add cat.txt
|
* commit 9eee82b4d028aa45f30913421708efd801938062
  Author: Joe <joe@email.tw>
  Date:   Thu Feb 17 10:55:07 2022 +0800

      Init commit


```
有注意到`git log`和`git log --graph`顯示資訊的差異吧。當我們使用`git log`時，得到的是完整的每筆提交的訊息，其中包括SHA-1的編號、提交的作者與Email和提交的訊息。然而，我們使用`git log --graph`時，除了得到跟`git log`相同的資訊以外，最右邊還會有`*`和`|`構成的圖形，這個圖形可以想像成時間線，每個`*`標示的都是提交訊息的時間點。未來提到分支(branch)時，`git log --graph`有時就想看分支之間的關係就會很好用。

上面的範例中再補充一點，我們在`test.txt`中輸入了文字，並且在提交時輸入了兩行訊息，這兩行中還多了一行空行。有時我們可能是為了解決某個BUG，而需要修改很多檔案，我們提交訊息時最好完整點，以後在查看時會比較清楚這次修改了什麼，又為了解決什麼問題而修改。以我們修改`test.txt`為例，我們在提交訊息的第一行可以看做是標題，第三行則是用更完整說明我們對`test.txt`做了哪些事，而第二行的空行則用來區隔標題與詳細內容。