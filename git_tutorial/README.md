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
$ git config --global user.name "Joe"
$ git config --global user.email "joe@test.tw"
```
輸入完成後，就可以設定目前使用的電腦在Git管理專案的使用者名稱和Email，而且這個設定可用在該電腦上所有Git管理的專案。這裡提醒一下，那個字元`$`只是Bash終端機的提示字元，實際上下指令時，是要輸入字元`$`後面的那串文字才對。

如果只想針對一個Git管理的專案設定特定的使用者名稱和Email，可以使用下面的指令：
```bash
$ git config --local user.name "Joe"
$ git config --local user.email "joe@email.tw"
```
重點是`git config`後面接的參數`--global`，只要有這個參數，就可以修改全域的參數。在`git config`後面接`--local`就可以只針對當前的專案調整設定。

### 更換編輯器
使用Git管理專案時會需要使用編輯器，Git也允許使用者更換編輯器，以下面指令範例是把編輯器更換成Vim：
```bash
$ git config --global core.editor vim
```
Git預設使用的編輯器是Vim，如果有需要也可使用GUI的編輯器，像是Notepad++。不過，本文採用的編輯器會是Vim。

### 顯示當前專案更動的設定
修改完專案的設定，可以使用下面的指令來查看設定值：
```bash
$ git config --list
```

## 建立專案給Git管理
接下來我們來建立一個專案，並且使用Git來管理這個專案。我們先建立一個專案，第一步先建一個資料夾，用專案的名稱來為這個資料夾命名。我們以`testGit`來當作專案名稱，在終端機中可以輸入下面的指令來建立專案資料夾：
```bash
mkdir testGit
```
建立好之後就切換進`testGit`中，我們也可以在終端機中輸入指令來切換當前的目錄：
```bash
$ cd testGit
```

建立好專案並且進到專案資料夾後，我們需要使用下面的指令來初始化專案：
```bash
$ git init
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
$ git status
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
上面的訊息中最重要的是`Untracked files`，在這行下面會列出檔名`Readme.md`，這是要告訴我們到目前為止檔案`Readme.md`是**未追蹤**中的，它要求我們使用`git add`和`git commit`把該檔案放進儲存庫中，讓Git可以持續追蹤該檔案的狀態。

我們再做一個實驗，先建立一個空的目錄，然後查看狀態：
```
$ mkdir test

$ git status
On branch master
nothing to commit, working tree clean

```
回報的訊息說沒任何東西需要搬移到暫存區，我們已經建立新的目錄，為什麼Git卻沒要求我們把目錄`test`放進儲存庫呢？這是因為Git只在意檔案內容的變動，而不會管目錄底下的變化，所以我們建立目錄`test`時，Git才會沒察覺該目錄的存在。

現在我們試試看，在目錄`test`中建一個空檔案，然後查看狀態：
```bash
$ touch test/.file

$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        test/

nothing added to commit but untracked files present (use "git add" to track)
```
在我們建立空檔案`.file`之後，Git就查覺到目錄`test`的存在。假如我們想把目錄放到儲存庫中，讓Git可以管控，我們可以先建一個空檔案，然後使用`git add `和`git commit`來放進儲存庫中。現在我們還是先把目錄`test`刪除掉：
```bash
$ rm -rf test
```

### 讓Git管理檔案的版本:`git add`
我們在前一小節中建立了檔案`Readme.md`，也使用`git status`確認過，`Readme.md`還沒讓Git幫我們管理。那麼我們該如何讓Git管理新檔案呢?

我們可以需要先使用`git add`把檔案放進暫存區中，該指令用法如下：
```bash
$ git add <filepath>
```
`filepath`是我們想讓Git管理的檔案路徑，現在我們只有一個檔案`Readme.md`需要讓Git管理，我們可使用下面的指令：
```bash
$ git add Readme.md
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
$ git add .
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
在輸入完畢，Git就會把`Readme.md`和訊息`Init commit`一併放進儲存庫中。

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

### 檔案的刪除與重新命名和搬移
交給Git管控的檔案也可以刪除掉，當然也可以重新命名。

#### 刪除檔案
我們先從檔案的刪除說起好了，我們有三個方法可以刪除檔案，第一個方法是直接在檔案總管上刪除檔案，第二個方法則是使用Bash提供的指令來刪除檔案。現在我們使用指令來刪除掉檔案`cat.txt`：
```bash
$ rm [-rf] 檔名或目錄
# -r  : 主要用於刪除目錄以及該目錄下的子目錄和檔案
# -f  : 強制刪除的意思，會無視任何情況直接刪除

$ rm cat.txt
```
至於第三個方法則是使用Git提供的指令來幫我們刪除檔案，這個指令就是`git rm`，用法有點類似Bash的`rm`，用法如下：
```bash
$ git rm [-rf] [--cached] 檔案或目錄
# -r        : 主要用來刪除一個目錄
# -f        : 強制刪除的意思，使用了它就會什麼都不管就把檔案和目錄刪除掉，這個要小心使用
# --cached  : 用來把檔案從Git的儲存庫中移除，並不是真的要刪除檔案
```
有用過Bash的`rm`就會知道，在不使用`-r`的情況下只能刪除檔案，如果想刪除掉一整個目錄，就要使用`-r`，這個時候包括該目錄下所有的子目錄和檔案都會全部被刪除。至於那個`-f`，有時刪除檔案可能會因為某些原因而導致沒辦法刪掉，這個時候就可以使用這個參數來強制刪除。最後的`--cached`則不會真的刪除檔案，它只是把檔案從Git的儲存庫中移除，該檔案就不再被Git管控，但是該檔案還是存在的。

接下來用範例說明如何刪除檔案。我們就用`rm`來刪除檔案`cat.txt`，然後看看當前專案的狀態吧。
```bash
$ rm cat.txt
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        deleted:    cat.txt
```
在刪除檔案以後，使用`git status`查看專案的狀態，可以發現Git告訴我們有檔案刪除。這裡要解釋一下，對Git來說刪除檔案也是一種檔案的更動，也就是檔案的狀態本來是存在的，而現在則消失了。因為檔案被刪除也被視為是一種檔案的更動，所以我們當然要把這個更動提交度到儲存庫裡：
```bash
$ git add .
$ git commit -m "Delete cat.txt"
[master 395bee4] Delete cat.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 delete mode 100644 cat.txt
```
這邊要提一下`git rm`和`rm`之間的差別，當我們使用`rm`刪除檔案時，需要使用`git add .`把檔案的刪除狀態搬移到暫存區中，然後使用`git commit`來提交到儲存庫中。可是當我們使用`git rm`時，這個動作相當於`rm`和`git add .`這兩個指令都做。也就是說，如果不想下這麼多指令，可以直接使用`git rm`，然後使用`git commit`提交到儲存庫，就可以把檔案刪除掉。

我們再來談談`git rm --cached`，有時我們已經把一個檔案提交到Git的儲存庫上了，事後才發現這個檔案不應該讓Git管控，可是這個檔案也許是程式碼運作時需要用到，不應該被刪除掉，這個時候我們就可以使用`git rm --cached`來幫我們刪除檔案。接下來我們就用範例來說明吧，我們先建一個檔案`tmp.txt`，然後把這個檔案提交到儲存庫裡。
```bash
$ touch tmp.txt
$ git add tmp.txt
$ git commit -m "Add tmp.txt"
[master 4913011] Add tmp.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 tmp.txt
```
現在我們來刪除檔案吧，然後查看專案的狀態：
```bash
$ git rm --cached
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        deleted:    tmp.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        tmp.txt
```
Git告訴我們`tmp.txt`已經刪除了，可是還有一個檔案`tmp.txt`不是追蹤狀態。我們可以從檔案總管查看`tmp.txt`是不是還存在，我們也可以使用Bash提供的指令來查看：
```bash
$ ls -l
-rw-r--r-- 1 JH-06 197121 28 Feb 17 10:54 Readme.md
-rw-r--r-- 1 JH-06 197121 19 Feb 17 13:51 test.txt
-rw-r--r-- 1 JH-06 197121  0 Feb 17 16:14 tmp.txt
```
檔案`tmp.txt`因`git rm`使用參數`--cached`而沒真的刪除掉，只是從儲存庫中被移除掉，所以Bash的指令`ls`和檔案總管都還看得到。現在，我們還是把`tmp.txt`加回去吧。
```bash
$ git add tmp.txt
$ git status
On branch master
nothing to commit, working tree clean
```

#### 重新命名和搬移
檔案的重新命名也是一種變更，這邊也有三個方法可以更改檔名，第一個方法就跟刪除檔案一樣，直接在檔案總管上處理，第二個方法就是使用Bash指令的`mv`來改檔名。我們用`mv`來更改`test.txt`的檔名：
```bash
$ mv 原始檔名 新檔名或路徑
# 原始檔名      : 想要重新命名或搬移的檔名
# 新檔名或路徑  : 若是一個在當前目錄下不存在的檔名，則對原始檔案更改名稱，
#                 假如是一個路徑，則會把原始檔案搬過去，相當於在檔案總管中把檔案剪掉並貼到新的目錄中

$ mv test.txt bird.txt
```
我們來看一下當前的狀態是如何：
```bash
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    test.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        bird.txt

no changes added to commit (use "git add" and/or "git commit -a")

```
我們從上面的訊息中可以得知，當我們更改檔名時，對Git來說這相當於兩個動作，第一個是刪除檔案`test.txt`，然後建立一個新檔案`bird.txt`。

至於檔案的重新命名的第三個方法，就跟刪除檔案一樣，Git也提供指令來達成這件事，而且這個指令相當於`mv`與`git add .`的結合。也就是說，使用Git提供的指令來更改檔名，我們可以少使用一個指令。這個指令的用法如下：
```bash
$ git mv [-f] 原始檔案 新檔名或路徑
# -f  : 有強制的意思，會無視不能改名的情況
```
就跟`git rm`類似，在某些情況下我們沒辦法更改檔名，這個時候就可以使用參數`-f`來強制改名。

這邊再提一點，不論是`mv`或是`git mv`除了可以用來更改檔名以外，只要在原始檔名後面接的不是檔名，而是一個路徑，則該檔案就會搬移到所指定的路徑。我們先建一個目錄`tmp`，然後試著使用`git mv`把檔案`tmp.txt`搬到目錄`tmp`中吧。
```bash
$ mkdir tmp

$ git mv tmp.txt tmp

$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        renamed:    tmp.txt -> tmp/tmp.txt

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    test.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        bird.txt
```
我們使用`git status`查看狀態，可以得知兩件事，第一件事是我們先前把`test.txt`改名為`bird.txt`，第二件事是`tmp.txt`改名為`tmp/tmp.txt`。對Git來說搬移檔案也算是重新命名，可以這樣思考，原本檔名`tmp.txt`改成`tmp/tmp.txt`，只是對我們來說這個動作是把檔案搬到目錄`tmp`中。

最後，我們把這兩個動作都提交到儲存庫中吧：
```bash
$ git add .

$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        renamed:    test.txt -> bird.txt
        renamed:    tmp.txt -> tmp/tmp.txt


$ git commit -m "Rename test.txt -> bird.txt and Move tmp.txt into directory tmp"
[master cf04238] Rename test.txt -> bird.txt and Move tmp.txt into directory tmp
 2 files changed, 0 insertions(+), 0 deletions(-)
 rename test.txt => bird.txt (100%)
 rename tmp.txt => tmp/tmp.txt (100%)
```
在我們使用`git add .`之前，使用`git status`可以看到本來`test.txt`回報說被刪除並建立新的檔案`bird.txt`，可是使用了`git add .`則回報說`test.txt`重新命名為`bird.txt`。

### 讓Git忽略檔案: `.gitignore`
在開發專案時，有時候我們把某些檔案放進儲存庫中讓Git管控，事後想想這些檔案不應該給Git管控。那些檔案可能含有像是帳密這類機密資訊，也可能那些檔案是程式碼需要用到的設定檔而導致更改的頻率很高，又或是建置專案與程式碼產生的臨時檔案。

Git也有辦法忽略掉我們不想管控的檔案，方法是建立檔案`.gitignore`，在這個檔案中制訂規則來忽略掉我們不想讓Git管控的檔案，然後使用`git add`指令把該檔案加到儲存庫中。我們先建立檔案`config.cfg`，然後查看狀態：
```bash
$ touch config.cfg

$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        config.cfg

nothing added to commit but untracked files present (use "git add" to track)
```
因為我們還沒建立`.gitignore`，所以Git查覺到有檔案建立出來。現在我們先建立檔案`.gitignore`：
```bash
$ touch .gitignore
```
接下來我們在`.gitignore`中加入一條規則來忽略掉`config.cfg`，請在`.gitignore`中填入下面的內容：
```
config.cfg
```
現在我們來看看Git是否能夠察覺`config.cfg`，並要求我們要提交到儲存庫：
```bash
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .gitignore

nothing added to commit but untracked files present (use "git add" to track)
```
看到了吧，當我們把`config.cfg`寫進`.gitignore`中，Git就會忽略掉`config.cfg`，可是`.gitignore`是新建的檔案，所以Git會要求我們提交到儲存庫中。

我們再來思考一個情況，假設我們要開發一套遊戲程式，這套遊戲程式需要使用`config.cfg`來維護程式某些設定，而且考慮到`config.cfg`有可能會一時失誤亂設定，這個時候我們需要準備`default.cfg`來當作用來初始化的設定檔，讓程式的設定恢復最初的狀態。也許未來還要根據不同的情境準備多個不同的設定檔，說不定會有10多個設定檔，難道我們要一一列舉出來才能讓Git忽略掉嗎?其實可以不必這麼做，雖然這些設定檔的檔名都不同，但我們知道這些設定檔都有一個共通點，那就是副檔名都是`.cfg`。這個時候我們可以搭配萬用字元`*`和附檔名`.cfg`來組成一個忽略規則，來讓Git忽略掉附檔名為`.cfg`的所有檔案，現在我們把`.gitignore`的內容改成下面：
```
*.cfg
```
接著，我們測試看看Git能不能忽略掉`config.cfg`，然後再測試看看我們再建立一個檔案`default.cfg`，看看Git能不能忽略掉`default.cfg`：
```bash
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .gitignore

nothing added to commit but untracked files present (use "git add" to track)

$ touch default.cfg

$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .gitignore

nothing added to commit but untracked files present (use "git add" to track)
```
我們修改了`.gitignore`的內容，Git還是照樣能夠忽略掉`config.cfg`，而且因為我們使用萬用字元`*`，所以`default.cfg`也會被忽略掉。

現在我們來試試看忽略掉目錄`tmp`中的`tmp.txt`，我們在`.gitignore`中再加入一行：
```
tmp/tmp.txt
```
然後我們在`tmp/tmp.txt`中輸入下面的內容：
```
This is tmp file.
```
接下來查看專案的狀態：
```bash
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   tmp/tmp.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .gitignore

no changes added to commit (use "git add" and/or "git commit -a")
```
`git status`回報說`.gitignore`是未追蹤的狀態，這個很正常，因為我們還沒提交到儲存庫中，可是奇怪的是Git卻說`tmp/tmp.txt`已經修改了。我們已經修改`.gitignore`來忽略掉`tmp/tmp.txt`，為什麼我們修改了這個應該要被忽略的檔案，Git卻還會持續追蹤這個檔案的狀態呢？

會有這個狀況是因為在我們把`tmp/tmp.txt`忽略掉的這條規則加到`.gitignore`中之前，就已經提交到儲存庫中。也就是說，Git只會在`.gitginore`加入忽略條件之後，才會這條忽略條件生效。如果在`.gitignore`加入忽略條件之前，就已經把需要忽略的檔案提交到儲存庫，那麼這些檔案就不會被忽略掉。在這種情況下，我們需要先編輯好`.gitignore`，然後使用`git rm --cached`來把應該要被忽略掉的檔案從儲存庫中移出，我們來試試看移除`tmp/tmp.txt`：
```
$ git rm --cached tmp/tmp.txt

$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        deleted:    tmp/tmp.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .gitignore
```
可以看到使用`git rm --cached `幫我們把`tmp/tmp.txt`從儲存庫中移除。我們來把`.gitignore`提交到儲存庫中，請確認`.gitignore`的完整內容跟下面的一樣：
```
*.cfg
tmp/tmp.txt
```
然後我們把`.gitignore`提交到儲存庫中：
```bash
$ git add .

$ git commit -m "Add .gitignore"
[master 7f707c6] Add .gitignore
 2 files changed, 2 insertions(+)
 create mode 100644 .gitignore
 delete mode 100644 tmp/tmp.txt
```
在做完上面的動作以後，不管我們怎麼修改`tmp/tmp.txt`的內容，Git也不會理會。假如不想保留任何要被Git忽略的檔案，也可以直接使用下面的指令：
```bash
$ git clean -fX
# -f  : 強制的意思
# -X  : 會依據.gitignore的規則來忽略掉的規則來刪除掉所有在專案中的檔案
```
使用上面的指令會根據`.gitignroe`的規則，可以一勞永逸的刪除掉所有要被忽略掉的檔案。這邊提醒一下，請確定真的要刪除掉所有需要忽略掉的檔案，否則等指令執行完了，可能就沒辦法救回那些被刪掉的檔案。

還需要提一點，只要被忽略的檔案曾經被提交到儲存庫中，即使編輯`.gitignore`並使用`git rm --cached`將其移出儲存庫，雖然Git不會再追蹤其狀態，但是在儲存庫中還是存在這些檔案。如果這些檔案非常大，目錄`.git`就會比較肥，後面會有一個小節會說明如何處理這個問題。

最後，我們統整一下編輯`.gitignore`忽略規則的幾個注意事項：
- 以一行為一個單位來制定一條規則，來忽略掉單一或多個檔案
- 若以檔名來當作一條忽略規則，Git只會忽略掉這個檔名
- 萬用字元`*`可以代表0到任意多個字元，可以搭配路徑和副檔名來忽略掉特定規則的檔案
- `#`是單行註解的符號，只要有這個符號，該符號後面的文字都會被Git忽略掉
- 只對新增規則以後的檔案才會被忽略
- 若在新增忽略規則之前就已經把檔案提交到儲存庫中，就需要事後使用`git rm --cached`或`git clean -fX`來把需要忽略的檔案移除掉
  
我們就拿下面的`.gitignore`的內容為例，來說明上面的注意事項：
```
# 忽略掉所有副檔名為.cfg的檔案
*.cfg

# 忽略掉目錄tmp的tmp.txt
tmp/tmp.txt
```
這邊列舉出上面範例的幾個重點：
- 第一行和第四行是註解，說明下一行的忽略條件的用途
- 第二行中因為萬用字元`*`可以代表任意的檔名，也可以包含某些目錄，所以只要副檔名為`.cfg`的檔案都會被忽略掉，就算這些檔案存在於專案目錄下的任何一個子目錄都會被忽略
- 最後一行只針對目錄`tmp`底下的`tmp.txt`制定忽略規則，也就是說這條規則只忽略掉這個特定的檔案

## 分支(Branch)
在本章節中將會介紹什麼是分支(Branch)，以及如何使用分支來管控專案。

### 何謂分支?
在科幻電影裡，主角會使用時光機器進行時光旅行，來到某個重要的分歧點。在這個重要的分歧點，主角的父母因沒買到彩券並中獎，而讓他們一家人過得非常困苦。主角第一次來到這個重要的分歧點，他誘使他的父母購買彩券，但是卻被壞人盯上，結果獎金被壞人搶走。這個時候產生了一個新的時間線，在這個時間線中雖然主角的父母中大獎，卻因獎金被搶走而過得很慘。主角第二次來到重要分歧點，而這次他不僅又使父母買彩券並中獎，還設法阻止壞人搶走獎金。這次又產生了一個時間線，在這個時間線中主角和父母用這筆獎金過得幸福美滿。

分支就類似於上面例子提到的時間線，雖然我們沒有時光機器，但是我們可以使用Git來幫我們管理專案時，在重要的分歧點上建立一個新的分支，甚至還可以建立多個分支，然後做我們想要達成的事情。那麼，分支在什麼情況下可以派上用場呢?

比方說我們在開發一個遊戲程式，在這遊戲中使用了我們自己設計的數學公式來模擬重力，但是因為當初沒設計好，導致計算過程比較耗時。也許我們經過分析後，發現只要修改數學公式，並且使用新的實作方式就可以讓計算過程更快，而且模擬重力的結果跟修改之前的一樣，但是我們不確定這麼做是否可行。這個時候我們就可以建立一個新的分支，在這個新的分支上嘗試實作新的方法。只要測試結果是對的，就保留這個新的分支，並且與原有的程式碼整合起來，就算失敗了，也不會影響到原有的程式碼。

### 建立與切換分支: `git branch`, `git checkout`
當我們想建立一個新的分支時，可以使用`git branch`，用法如下：
```bash
$ git branch <new_branch>
# <new_branch>  : 新分支的名稱

$ git branch
# 用於顯示所有的分支
```
使用`git branch`建立分支時，只要在該指令後面輸入分支的名稱即可。基本上分支名稱沒有什麼限制，唯一的限制是不能包含空格。`git branch`主要的用途是管理分支，除了剛剛提到可以建立新分支以外，還可以只使用`git branch`會顯示專案中所有的分支。當然，`git branch`也能夠修改現有分支的名稱，甚至可以刪除已經存在的分支：
```bash
$ git branch -m <old_branch> <new_branch>
# -m            : 用於修改分支的名稱
# <old_branch>  : 已經存在分支的名稱
# <new_branch>  : 新的分支名稱

$ git branch [-d|-D] <branch>
# -d        : 用於刪除已經存在的分支
# -D        : 類似-d，但是會強制刪除
# <branch>  : 欲刪除的分支名稱
```
這裡提一下，`|`代表兩個參數中只能選一個來使用。使用`git branch -m`修改分支名稱時，後面接的`<old_branch>`必須是已經存在的分支，而`<new_branch>`則必須是不存在的分支名稱才行。在刪除分支時，所使用的分支當然也必須是已經存在的。而`git branch -d`和`git branch -D`之間的差別在於，後者會無視任何情況，直接刪除掉分支，而前者在遇到一些狀況時會中只刪除的動作。

當我們建立好分支後，就應該要切換到新的分支中，去完成這個新分支的工作。我們可以使用`git checkout`來切換分支，用法如下：
```bash
$ git checkout [-b] <branch_name>
# -b            : 當切換的分支不存在時，會建立新的分支
# <branch_name> : 欲切換過去的分支名稱
```
當我們直接使用`git checkout`時可以切換到已經存在的分支，切換過去時。這裡要提一下，當我們使用`git checkout -b`時，只要後面接的分支名稱不存在，Git就會幫我們先建立好，然後切換過去，就相當於先執行`git branch`建立新分支後，執行`git checkout`切換分支。

在大致說明用法以後，接下來用範例來體會一下這些指令的功能吧。我們先建立新的分支，然後切換過去，然後新增檔案並提交到儲存庫中：
```bash
$ git branch test

$ git checkout test
Switched to branch 'test'

$ touch test.txt

$ git add .

$ git commit -m "Add test.txt"
[test 8d0a25f] Add test.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 test.txt

$ ls -l
total 2
-rw-r--r-- 1 JH-06 197121 28 Feb 17 10:54 Readme.md
-rw-r--r-- 1 JH-06 197121 19 Feb 17 13:51 bird.txt
-rw-r--r-- 1 JH-06 197121  0 Feb 17 18:02 config.cfg
-rw-r--r-- 1 JH-06 197121  0 Feb 18 09:58 default.cfg
-rw-r--r-- 1 JH-06 197121  0 Feb 21 17:04 test.txt
drwxr-xr-x 1 JH-06 197121  0 Feb 18 11:03 tmp/
```
在做完上面的指令以後，這邊我們使用`ls -l`可以發現`testGit`目錄底下多了一個我們剛剛建立的新檔案`test.txt`。我們使用`git log`來確認看看提交的歷史紀錄：
```bash
$ git log
commit 8d0a25fc3a80cf1bea78003c7df1697dc4ea3855 (HEAD -> test)
Author: Joe <joe@email.tw>
Date:   Mon Feb 21 17:04:12 2022 +0800

    Add test.txt

commit 7f707c650e3b3c9cafeebbc63f309a04057e542d (master)
Author: Joe <joe@email.tw>
Date:   Fri Feb 18 10:43:01 2022 +0800

    Add .gitignore

commit cf04238a79903b480935682237eedabd465d15e7
Author: Joe <joe@email.tw>
Date:   Thu Feb 17 17:32:12 2022 +0800

    Rename test.txt -> bird.txt and Move tmp.txt into directory tmp

commit 4913011d31e613f0fe0a041c3df7bd813cc2c95b
Author: Joe <joe@email.tw>
Date:   Thu Feb 17 16:14:46 2022 +0800

    Add tmp.txt
:
```
可以發現最上面的就是我們剛剛提交的訊息。這邊留意一下，在最上面的訊息的最右邊有個`(HEAD) -> test`代表我們現在處於`test`分支，關於`HEAD`後面會再詳細說明。我們切回分支`master`，並且看看目錄`testGit`會有什麼樣的變化：
```bash
$ git checkout master
Switched to branch 'master'

$ ls -l
total 2
-rw-r--r-- 1 JH-06 197121 28 Feb 17 10:54 Readme.md
-rw-r--r-- 1 JH-06 197121 19 Feb 17 13:51 bird.txt
-rw-r--r-- 1 JH-06 197121  0 Feb 17 18:02 config.cfg
-rw-r--r-- 1 JH-06 197121  0 Feb 18 09:58 default.cfg
drwxr-xr-x 1 JH-06 197121  0 Feb 18 11:03 tmp/
```
我們切回分支`master`以後，剛剛我們建立的檔案`test.txt`居然不見了，這是怎麼回事？`git checkout`運作機制是這樣的，當我們使用`git checkout master`時，Git會從目錄`.git`中把對應於分支`master`的狀態取出來，然後將該狀態的所有檔案取代掉目錄`testGit`。也就是說剛剛我們建立的檔案`test.txt`也不是真的被刪除掉，它還存在於目錄`.git`中。

現在我們在分支`test`上再建立一個新的分支，然後再切換過去，並且建立檔案並提交到儲存庫中：
```bash
$ git checkout test
Switched to branch 'test'

$ git branch dog

$ git checkout dog
Switched to branch 'dog'

$ touch dog.txt

$ git add .

$ git commit -m "Add dog.txt"
[dog 0c62b45] Add dog.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 dog.txt

$ ls -l
-rw-r--r-- 1 JH-06 197121 28 Feb 17 10:54 Readme.md
-rw-r--r-- 1 JH-06 197121 19 Feb 17 13:51 bird.txt
-rw-r--r-- 1 JH-06 197121  0 Feb 17 18:02 config.cfg
-rw-r--r-- 1 JH-06 197121  0 Feb 18 09:58 default.cfg
-rw-r--r-- 1 JH-06 197121  0 Feb 21 17:26 dog.txt
-rw-r--r-- 1 JH-06 197121  0 Feb 21 17:24 test.txt
drwxr-xr-x 1 JH-06 197121  0 Feb 18 11:03 tmp/

```
當我們使用完上面的指令，目錄`testGit`會多一個新的檔案，也就是我們剛剛建立的`dog.txt`。這裡要注意一下，當我們建立新的分支時，Git會從當前的分支中建出一個副本，它會包含該當前分支所有的狀態。以上面為例，我們先在分支`master`建立分支，所以分支`test`中會包含分支`master`所有的檔案，以及最新的修改內容。接著，我們又在分支`test`上再建立一個新分支`dog`，分支`dog`也擁有分支`test`的所有檔案，這其中也包含`test.txt`。

現在我們切回分支`master`，然後試試看能不能刪除分支`dog`：
```bash
$ git checkout master
Switched to branch 'master'

$ git branch
  dog
* master
  test

$ git branch -d dog
error: The branch 'dog' is not fully merged.
If you are sure you want to delete it, run 'git branch -D dog'.

```
我們切回分支`master`以後，使用`git branch`查看所有的分支情況，其中分支`master`左邊的`*`代表當前所在的分支是`master`。我們使用`git branch -d`居然不能刪除掉分支`dog`，這是為什麼呢？我們來分析Git回報的錯誤訊息，原來Git不允許刪除分支`dog`的原因是，分支`dog`已經提交過訊息，可是還沒合併回現有的分支。假如我們真的想刪除分支`dog`，也不是不行，可以這樣做：
```bash
$ git branch -D dog
Deleted branch dog (was 0c62b45).

$ git branch
* master
  test

```
看到了吧，使用`git branch -D`可以強制刪掉分支`dog`，然後使用`git branch`也可以發現分支`dog`是真的刪除掉了。刪除掉分支`dog`以後顯示的訊息，意思是分支`dog`確實已經刪除掉了，但是考慮到我們可能會想反悔，所以實際上還會暫時保留一個編號`0c62b45`，日後方便我們可以救回已經被刪掉的分支。不過，也不是所有被刪除的分支都救得回來，必須滿足一些條件才行，這個後面會再提。

### Git與SHA-1
#### SHA-1的用途
[SHA-1 (Secure Hash Algorithm 1)](https://zh.wikipedia.org/wiki/SHA-1)是一種雜湊函數，將資料輸入進這種函數，會產生出40個十六進位的數字。這種函數還有一個很重要的特性，輸入相同的資料，會產生出相同的值，但是輸入多個不同的資料，**理論上**也會得到不同的輸出值。也就是說我們可以利用這種函數的特性從輸出的值中，來判斷輸入的資料是否相同。

我們剛剛談到SHA-1這類雜湊函數有一個特性，那就是**理論上**只要輸入的資料不同，就能得到具有**不會重複的**輸出結果。這邊強調了「理論上」，也就是說雖然雜湊函數具有剛剛提到的特性，但是也有可能會發生輸入的資料不同，卻會得到相同的輸出結果，這種情況稱之為碰撞(Collison)。

如果雜湊函數沒設計好，的確有可能經常發生碰撞。Google和CWI Amsterdam合作確實能夠做出兩個相同的SHA-1值，但是內容卻不同的PDF檔。然而，我們也不需要擔心，因為Google擁有龐大的資源，才能做出這種內容不同的PDF檔卻有相同的SHA-1值，一般的情況下使用SHA-1是不太會遇到碰撞。

那麼Git如何使用SHA-1呢？對Git來說一切皆為物件，在處理檔案或是提交變更訊息到儲存庫，這一切都會使用SHA-1來處理，然後儲存在目錄`.git`中，後面會更詳細說明。

#### SHA-1與分支
Git也可以看做是一個檔案系統，它會建立一個目錄`.git`，然後使用這個目錄儲存一個專案中所有的檔案，以及每個檔案的修改歷史紀錄。

前面說過Git把一切都當作物件來看待，在處理檔案這邊，Git當然不會直接把專案中的檔案直接放進目錄`.git`中。它會先把檔案的內容拿出來計算SHA-1值，然後利用這個數值當作索引值，把該檔案的內容經過壓縮以後集中管理在一個檔案`*.pack`。當我們需要從目錄`.git`中拿出該檔案，就可以使用事先計算好的SHA-1值在`*.pack`檔中找出該檔案的內容所在位置，然後把檔案解壓縮回來。

我們先前提交訊息到儲存庫也是相同作法，Git會把我們提交的訊息、更動過的檔案、提交時間等資訊拿來使用，計算出相對應的SHA-1值，然後會有檔案專門紀錄這個SHA-1值。因為提交訊息的時間，所以我們在照著本文的範例做，使用`git log`查看歷史紀錄時，每個提交訊息的SHA-1值才會不一樣。

接下來我們來談一談Git的分支，剛剛提過每個提交的訊息對Git來說都是物件，所有提交的訊息物件以時間順序串起來就構成一條分支。如果有學過C語言，應該知道鏈結串列。在Git中分支的結構有點像鏈結串列，每個提交訊息的物件可以看做是鏈結串列中的節點，每個節點擁有的資料除了包含提交的訊息、檔案的修改內容和提交時間以外，還會儲存前一個提交訊息物件的SHA-1值。這個SHA-1有點像鏈結串列中一個節點的記憶體位置。因為每個節點都有前一個節點的SHA-1值，Git才能串聯出一條分支。

剛剛描述分支的情況並不算完全正確，準確來說，對Git而言分支有點像是鏈結串列中用來指向特定節點的指標變數，我們也可以想像成是一個標籤。也就是說當我們使用`git branch`建立分支時，其實是做出一個標籤，用這個標籤來黏貼在某個提交訊息物件。每當我們提交一次訊息到儲存庫時，Git就會把這個標籤拿起來，建立出一個新的提交訊息的物件串到前一個提交訊息之物件上，然後把這個標籤貼到這個剛做出來的提交訊息之物件上。

### 合併分支: `git merge`
接下來我們用[何謂分支?](#何謂分支)中開發遊戲的範例來說明為何需要合併分支。假設原本使用效率很差的重力模擬公式的分支為`master`，現在我們建立一個新的分支`test_new-method`來實作新的重力模擬的數學公式，我們不只實作出來，而且還能證明新的方法確實比舊的更有效率。這個時候我們就應該把分支`test_new-method`上做的所有修改都整合到分支`master`上，這個動作就是合併分支。

我們來談談`git merge`這個指令，這是合併分支常用的指令，用法如下：
```bash
$ git merge [--no-ff] <branch-name>
# --no-ff :  不使用快速模式來合併分支
```
一般使用`git merge`時會搭配`git checkout`使用，因為這個指令是把別的分支合併到當前的分支。如果有照著本文的範例做的話，`testGit`會有兩個分支`master`和`test`，現在我們先切換到`master`，然後使用看看`git merge`：
```bash
$ git checkout master
Switched to branch 'master'

$ git merge test
Updating 7f707c6..8d0a25f
Fast-forward
 test.txt | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 test.txt

$ git log --graph
* commit 8d0a25fc3a80cf1bea78003c7df1697dc4ea3855 (HEAD -> master, test)
| Author: Joe <joe@email.tw>
| Date:   Mon Feb 21 17:04:12 2022 +0800
|
|     Add test.txt
|
* commit 7f707c650e3b3c9cafeebbc63f309a04057e542d
| Author: Joe <joe@email.tw>
| Date:   Fri Feb 18 10:43:01 2022 +0800
|
|     Add .gitignore
|
* commit cf04238a79903b480935682237eedabd465d15e7
| Author: Joe <joe@email.tw>
| Date:   Thu Feb 17 17:32:12 2022 +0800
|
|     Rename test.txt -> bird.txt and Move tmp.txt into directory tmp
|
* commit 4913011d31e613f0fe0a041c3df7bd813cc2c95b
| Author: Joe <joe@email.tw>
| Date:   Thu Feb 17 16:14:46 2022 +0800
|
|     Add tmp.txt
|
* commit 395bee40b5c1c9a159e178779ccbbc8ebf78a7ff
| Author: Joe <joe@email.tw>
| Date:   Thu Feb 17 16:09:00 2022 +0800
|
|     Delete cat.txt
|
* commit a8b412ecc331c7df85df9c1f6cec2e6a9d092283
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

$ ls -l
total 2
-rw-r--r-- 1 JH-06 197121 28 Feb 17 10:54 Readme.md
-rw-r--r-- 1 JH-06 197121 19 Feb 17 13:51 bird.txt
-rw-r--r-- 1 JH-06 197121  0 Feb 17 18:02 config.cfg
-rw-r--r-- 1 JH-06 197121  0 Feb 18 09:58 default.cfg
-rw-r--r-- 1 JH-06 197121  0 Mar  3 15:13 test.txt
drwxr-xr-x 1 JH-06 197121  0 Feb 18 11:03 tmp/
```
當我們合併完，分支`master`就會包含分支`test`中所有的東西。如果我們使用`ls -l`指令查看，就會發現原本分支`master`中不存在檔案`test.txt`，可是在合併分支以後，就多了這個檔案。

也許你會想問，在分支`master`使用`git merge`來合併分支`test`，和在分支`test`使用`git merge`來合併分支`master`，有什麼差別？如果以結果來看，可以說沒什麼差別，因為分支`master`已經擁有分支`test`的所有檔案和修改狀態，而分支`test`也已經擁有分支`master`所有的檔案和修改狀態。不過，實際上還是不太一樣，在分支`master`使用`git merge`來合併，可以想像成分支`master`是一條主幹河流，而分支`test`則是一條支流小溪，進行合併時其實是支流的`test`流向主幹的`master`。

現在我們來談談那個參數`--no-ff`到底是什麼。如果只使用`git merge`會以快速模式(Fast-forward)來合併，假如使用`--no-ff`就會以非快速模式來合併。現在我們用範例來說明，首先我們切換分支到`test`，然後提交兩次訊息：
```bash
$ git check test
Switched to branch 'test'

$ echo "new file!" > new.txt

$ git add .

$ git commit -m "Add new.txt"
[test e878e12] Add new.txt
 1 file changed, 1 insertion(+)
 create mode 100644 new.txt

$ touch color.txt

$ git add .

$ git commit -m "Add color.txt"
[test 90bdb96] Add color.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 color.txt

$ git log --graph
* commit 90bdb96a6e365605d127f985cc6ddec765a23845 (HEAD -> test)
| Author: Joe <joe@email.tw>
| Date:   Thu Mar 3 15:33:22 2022 +0800
|
|     Add color.txt
|
* commit e878e12d1b408d8df3e85464b4cd748e8fc1db4d
| Author: Joe <joe@email.tw>
| Date:   Thu Mar 3 15:31:50 2022 +0800
|
|     Add new.txt
|
* commit 8d0a25fc3a80cf1bea78003c7df1697dc4ea3855 (master)
| Author: Joe <joe@email.tw>
| Date:   Mon Feb 21 17:04:12 2022 +0800
|
|     Add test.txt
|
* commit 7f707c650e3b3c9cafeebbc63f309a04057e542d
| Author: Joe <joe@email.tw>
| Date:   Fri Feb 18 10:43:01 2022 +0800
|
|     Add .gitignore
|
* commit cf04238a79903b480935682237eedabd465d15e7
| Author: Joe <joe@email.tw>
| Date:   Thu Feb 17 17:32:12 2022 +0800
|
|     Rename test.txt -> bird.txt and Move tmp.txt into directory tmp
|
* commit 4913011d31e613f0fe0a041c3df7bd813cc2c95b
| Author: Joe <joe@email.tw>
| Date:   Thu Feb 17 16:14:46 2022 +0800
|
|     Add tmp.txt
|
* commit 395bee40b5c1c9a159e178779ccbbc8ebf78a7ff
| Author: Joe <joe@email.tw>
| Date:   Thu Feb 17 16:09:00 2022 +0800
|
|     Delete cat.txt
|
* commit a8b412ecc331c7df85df9c1f6cec2e6a9d092283
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
(END)
```
現在我們使用非快速模式把分支`test`合併到分支`master`：
```bash
$ git checkout master
Switched to branch 'master'

$ git merge --no-ff test
Merge made by the 'recursive' strategy.
 color.txt | 0
 new.txt   | 1 +
 2 files changed, 1 insertion(+)
 create mode 100644 color.txt
 create mode 100644 new.txt

$ git log --graph
*   commit 65e497e3b11e3bcc75aa9c8799c0df017cc754fa (HEAD -> master)
|\  Merge: 8d0a25f 90bdb96
| | Author: Joe <joe@email.tw>
| | Date:   Thu Mar 3 15:35:58 2022 +0800
| |
| |     Merge branch 'test'
| |
| * commit 90bdb96a6e365605d127f985cc6ddec765a23845 (test)
| | Author: Joe <joe@email.tw>
| | Date:   Thu Mar 3 15:33:22 2022 +0800
| |
| |     Add color.txt
| |
| * commit e878e12d1b408d8df3e85464b4cd748e8fc1db4d
|/  Author: Joe <joe@email.tw>
|   Date:   Thu Mar 3 15:31:50 2022 +0800
|
|       Add new.txt
|
* commit 8d0a25fc3a80cf1bea78003c7df1697dc4ea3855
| Author: Joe <joe@email.tw>
| Date:   Mon Feb 21 17:04:12 2022 +0800
|
|     Add test.txt
|
* commit 7f707c650e3b3c9cafeebbc63f309a04057e542d
| Author: Joe <joe@email.tw>
| Date:   Fri Feb 18 10:43:01 2022 +0800
|
|     Add .gitignore
|
* commit cf04238a79903b480935682237eedabd465d15e7
| Author: Joe <joe@email.tw>
| Date:   Thu Feb 17 17:32:12 2022 +0800
|
|     Rename test.txt -> bird.txt and Move tmp.txt into directory tmp
|
* commit 4913011d31e613f0fe0a041c3df7bd813cc2c95b
| Author: Joe <joe@email.tw>
| Date:   Thu Feb 17 16:14:46 2022 +0800
|
|     Add tmp.txt
|
* commit 395bee40b5c1c9a159e178779ccbbc8ebf78a7ff
| Author: Joe <joe@email.tw>
| Date:   Thu Feb 17 16:09:00 2022 +0800
|
|     Delete cat.txt
|
* commit a8b412ecc331c7df85df9c1f6cec2e6a9d092283
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
(END)
```
當我們合併完，使用`git log --graph`查看會發現分支線多了一個空洞，這個不使用參數`--no-ff`是完全不同的。從這個範例中我們可以知道使用`git merge`合併時兩條分支會整合成一條，但是使用`git merge --no-ff`則會同時保留這兩條分支的樣子。如果我們希望可以看出哪些提交的訊息屬於哪一個分支，就可以使用`git merge --no-ff`，但是假如不在乎的話就可以使用`git merge`。
