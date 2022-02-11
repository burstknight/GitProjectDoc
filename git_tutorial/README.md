# Git 使用教學
本文記錄了Git常用指令。

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
git config --local user.email "joe@test.tw"
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