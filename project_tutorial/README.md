# 專案管理指南
當我們使用Git開發專案時，假如有很多人會一起維護這個專案，最好事先規劃好一些規則讓大家遵守，否則大家各做各的可能會出狀況。本文主要是針對這部分提供一些建議，當然只要跟團隊的所有成員講好，也可以不用完全使用本文的建議。

## 提交訊息的規範
這邊要介紹用於提交訊息的規範，它可以讓我們在未來看歷史紀錄時，可以在不用閱讀程式碼的情況下，知道每次修改都更動了哪些檔案，以及為何修改。

提交訊息時最好使用`git commit`，這會讓Git打開編輯器讓我們輸入訊息。使用這種方式，我們比較能夠寫出更詳細的訊息，也可花點時間思考一下，應該怎麼寫比較能夠讓別人看得懂。提交訊息的格式可以參考下面：
```
<Type>:[<Scope>]: <Subject>

<Body>

[<Footer>]
```
後面幾個小節會一一說明每個欄位的用途。

### Type: 提交訊息的類型
`<Type>`這個欄位用來分類提交訊息的類型，當閱讀者在查閱歷史紀錄時，可以從這個欄位知道每次提交的訊息是不是他想看的，這可以節省很多時間。可用的類型如下表：

類型        | 說明
------------|-------------------------------------
feat        | 新增或是修改功能
fix         | 修正BUG
doc         | 更動專案中的文件，或是更動程式碼裡面的註解
refactor    | 重構程式碼，但是這不是為了修正BUG或是新增功能
style       | 主要針對撰寫程式碼的風格來修改
perf        | 修改程式碼來提升執行效能
test        | 增加用來測試的程式碼
chore       | 更動輔助工具，比方說用來建置專案程式碼的腳本檔
revert      | 撤銷先前提交的修改
remove      | 移除不需要的程式碼或功能

### Scope: 涵蓋範圍
`<Scope>`這個欄位是用來說明提交訊息時，修改的涵蓋範圍，比方說式資料庫、控制層之類的，這部分會因專案而有所不同。主要是讓閱讀者知道這次修改的幅度大小，以及修改的方向。這個欄位非必要，所以也可以不填

### Subject: 提交訊息的標題
`<Subject>`這個欄位是提交訊息的標題。這個部分請用簡短的文字說明修改了哪些東西，最好不要超過50個字元。由於這個欄位填入的不是句子，結尾不要使用句號。基本上只要用簡短的文字說明修改的重點即可。**這個欄位是必須填寫的。**

### Body: 詳細說明修改內容
`<Body>`這個欄位是**必須填寫的**，這邊請用更詳細的文字說明，修改了哪些程式碼，以及為何需要修改。這可以讓閱讀者在不需要查閱程式碼的情況下，可以

### Footer: 填寫任務編碼
`<Footer>`這個欄位主要用於說明這次修改主要是針對哪一個任務編碼。假如沒有任務編碼，這個欄位可以不填。

### 範例
看到這邊應該還是不知道該怎麼寫吧，這裡提供幾個範例。

假設我們正在開發一款遊戲，我們在遊戲中新增功能讓主角可以飛行。這個時候提交的訊息可以這樣寫：
```
feat: 主角可以飛行

新增類別myFlyAction，來實現主角可以飛行。
```

如果我們發現剛剛用來實現飛行功能的程式碼有BUG，而且我們也已經修正完成。假設這個問題有任務編號，這個編號是111，提交的訊息可以這樣寫：
```
fix: 飛行超出邊界

問題：
1. 主角飛行時會超出畫面的邊界
2. 敵人飛行時會異常閃爍

原因:
1. 成員函數fly()計算主角下一個時間點的位置的數學公式有誤，導致主角會超出邊界
2. 成員函數fly()使用比較沒有效率的實作方式，讓敵人飛行時會閃爍

調整項目：
1. 修正計算下一個時間點位置的數學公式
2. 使用更有效率的方式來時做成員函數fly()

issue #111
```

提交的訊息可以用中文，也可以用英文，只要團隊所有成員都看得懂，就沒什麼問題。這邊再給英文的範例，我習慣會在`<Subject>`欄位的第一個字母使用大寫，主要是跟前面的標籤區分。
```
doc: Remove useless comments

The comments cannot offer usalful information, so I remove them.
```

## 設定提交訊息的樣板
前一章提過使用一些規範來提交訊息，讓團隊其他成員只要閱讀歷史紀錄就可以很快進入狀況。剛使用前一章的內容來提交訊息，應該會覺得要記憶這麼多事情很麻煩，那麼有沒有什麼方法可以輔助我們呢？

我們可以先建一個樣板，裡面先寫好提交訊息的格式，然後設定專案在使用`git commit`就會把這個樣板讀進來。這邊提供一個簡單的範例：
```
#feat/fix/remove/doc
#:

# Why is it necessary? (Bug fix? Add new functions?)
#-

#[issue #id]
```
那個符號`#`代表註解，所以當我們編輯完訊息，Git不會處理那些用`#`後面的文字。假設這個檔案命名為`git_commit_template`，然後這個檔案放在目錄`~`中，其中`~`這個目錄在Linux中是指使用者的家目錄，在Windows中則是`C:\Users\{user}`，這個`{user}`就是你所使用的帳號。其實這個檔案可以放在任何地方，只要你找得到就沒什麼問題。現在我們試著在專案`testGit`中，設定使用這個樣板檔案，我們所使用的指令如下：
```bash
$ git config commit.template <template_path>
# <template_path>   :   樣板檔案的路徑

$ git config commit.template ~/git_commit_template

```
我們只要在指令`git config commit.template`後面接上樣板檔案的所在路徑即可。

如果有使用VIM，就會知道可以在編輯模式下按`Ctrl`與`N`來自動補齊。只要預先設定好樣板，以後在使用`git commit`就會非常有效率。

## Git Flow
當有越來越多人餐與同一個專案的維護，假如沒事先講好規則，每個人都照自己的意思隨意提交訊息，總有一天會這會變成大麻煩。因為未來我們可能會想瀏覽歷史紀錄，確認每個人修改的東西，或是新成員想追蹤修改紀錄來解決一些問題，這個時候就沒辦法從以前提交的訊息能夠在有限的時間得到有用的訊息。

有人為了解決這個問題，給了一個建議，那就是制定一個開發流程，讓開發團隊一起遵守。這邊主要介紹Git flow，這個開發流程從2010年就被提出來，有興趣的話，可以參考[這個網站](http://nvie.com/posts/a-successful-git-branching-model)的內容。

### 分支情境
Git Flow透過定義分支的用途，來建議怎麼開發一個專案。在維護專案的過程中，會使用到的分支有`master`、`develop`、`release`、`feature`與`hotfix`。只有分支`master`與`develop`會一直存活著，所以這兩個分支也被稱為長期分支。其他分支則會在完成他們自己的主要任務以後就會被刪除掉。接下來，我們來談談每個分支的用途。

#### master分支
這個分支主要是用來放穩定的版本，而且還可以隨是釋出正式版提供給使用者使用。原則上最好不要修改這個版本的東西，而且這個分支也只能從其他分支合併過來。當其他分支合併過來時，通常都會建一個標籤來註明準備要釋出的版本號。

#### develop分支
這個分支主要用來開發新功能，不過一般來說都會從這個分支作為基礎，分出一個新的分支feature，這些feature分支上實作新功能。當這些feature分支上成功實作出新功能，而且確定都沒有問題時，才會合併回分支`develop`。

#### release分支
只要認為在分支`develop`開發的新功能都沒有問題時，就會合併到這個分支進行最後的測試，來檢查是否還有BUG沒找到。假如最後測試都沒什麼問題，或是修正完所有的錯誤，就會把這個分支合併到分支`master`，同時也會合併回分支`develoop`。需要把分支`release`合併回分支`develop`的原因在於，假如在分支`release`上發現有BUG並且加以修正，就必須透過合併回分支`develop`來同步修正這些錯誤，未來在從分支`develop`開發新功能時，先前在分支`release`找到的錯誤才不會又出現。

#### feature分支
當我們準備要開發新功能，或是修正一些比較沒有那麼迫切需要解決的錯誤時，就可以從分支`develop`分出一個新的分支，在這個分支上完成我們想達成的事情。只要確定都沒問題，就會從這個分支合併回分支`develoop`。

#### hotfix分支
有時我們釋出正式版的程式以後，使用者可能會回報程式的錯誤，或是事後我們發現程式碼可能存在著一些錯誤。這些錯誤都有急迫性，這個時候就會建出這個分支，立即修復這些錯誤。

一般來說都是從分支`master`分出分支`hotfix`來修復這些比較緊急的錯誤。我們會從分支`master`分出來，而不是從分支`develop`分出來的原因是，分支`develop`可能還在開發新功能，這些剛實作的新功能可能還不穩定。如果我們直接從分支`develop`分出來修復那些緊急的錯誤，就需要多花些心思留意那些新功能。然而，分支`master`大部分功能都已經在之前測試過了，相對於分支`develop`來說比較穩定。因此，從分支`master`分出來就可以專注處理那些錯誤。

當我們在這個分支修正完所有的錯誤時，就會把這個分支合併回分支`master`。我們也要把這個分支hotfix也合併到分支`develop`，因為這些錯誤也都是從`develop`一併同步到分支`master`，所以才需要透過把分支`hotfix`合併到分支`develop`，否則以後分支`develop`準備要釋出正式版時，那些曾經修復過的錯誤又會發生。

## 變數與函式命名規則
當有多人共同維護同一個專案時，最好能遵守命名規則來對專案中每個變數、函式命名，未來有新成員接手，或是團隊其他成員需要修改或閱覽不是他自己編輯的程式碼時，比較能進入狀況。

### 駝峰式大小寫
[駝峰式大小寫(Camel Case)](https://zh.wikipedia.org/wiki/%E9%A7%9D%E5%B3%B0%E5%BC%8F%E5%A4%A7%E5%B0%8F%E5%AF%AB)在為變數和函式命名時很好用，而且這套命名規則可以讓變數與函式變得更易於閱讀。這個命名規則的用法為，當變數與函式的名稱是由二個或多個英文單字連結起來時，每個單字的字首以大寫表示，其餘的字母則以小寫表示。

#### 小駝峰式命名法(Lower Camel Case)
這個命名規則是變數或函式的名稱中的第一個單字的字首以小寫字母表示，其餘單字的字首則以大寫表示。舉例來說，假設有個變數示用來儲存一個人的姓氏，該變數可以這樣命名：`lastName`。

#### 大駝峰式命名法(Upper Camel Case)
這個命名規則是指變數或函示的名稱中所有單字的字首都以大寫字母來表示。若以前面的例子來說明，用來儲存一個人姓氏的變數名稱可以這樣命名：`LastName`。

### 變數命名規則
變數最好以小駝峰式命名法來命名，而且為了能夠可以在第一眼辨認出變數的型態，應該在字首部分多加一個字母用以表示該變數的型態。此外，變數的名稱最好使用名詞，也可與形容詞搭配命名。

下表是每個型態對應的用於表示變數型態的字母：
字首字母    | 型態             | 說明  
---------|------------------|-------------------------------------------
`i`      | `int`            | 用以表示有號整數型態(`int`)
`c`      | `char`           | 用以表示`char`型態
`is`     | `bool`           | 用以表示其值是布林值
`s`      | `std::string`    | 用以表示字串型態
`str`    | `std::string`    | 用以表示字串型態
`f`      | `float`          | 用以表示`float`型態
`d`      | `double`         | 用以表示`double`型態
`u`      |                  | 必須與`i`一起使用，用以表示無號整數，但不強迫使用
`p`      |                  | 假如變數是一個指標變數，需使用這個字母來表示，可與前面混用
`o`      |                  | 用以表示一個變數是一個物件，只要不符合C/C++的基本資料型態，皆可用這個字母來表示型態
`a`      |                  | 用以表示該變數是一個陣列，必須與上述的字母搭配使用才能夠說明其元素的型態
`v`      | `std::vector`    | 用以表示該變數是`std::vector`之物件，最好與前面混用，用以表示元素的型態，若要使用陣列建議使用`std::vector`

這裡用基本資料型態來說明如何使用剛剛介紹的表格來給變數命名：
```C++
int iId;                    // 這個變數字首用 i 來表示 int 型態，用來表示一個 ID
float fMoney;               // 這個變數字首用 f 來表示 float 型態，用來表示金錢的數值
double dDistance;           // 這個變數字首用 d 來表示其形態是 double ，用來儲存一個物體移動的距離
char cKey;                  // 這個變數的字首 c 用來表示其形態為 char ，用來儲存鍵盤輸入按鍵的ASCII之編碼
unsigned int uiSizeOfArray; // 這個變數的字首 ui 用來表示其值為無號整數，用來記錄一個陣列的長度
bool isFlag;                // 這個變數的字首 is 用以表示其值為布林值
int aiScore[20];            // 這個變數的字首 ai 用以表示一個陣列，而且每個元素皆為整數型態
std::vector<int> viVector;  // 這個變數的字首 vi 用以表示為 std::vector 之物件且其元素為 int 型態
std::string sLastName;      // 這個變數的字首 s 用以表示其值為字串
```
這邊需要說明的是，當變數的型態為字串時，其字首字母可以使用`s`也可以用`str`，以上面的範例來說明，變數`sLastName`也可以取名為`strLastName`。

在C++中可以宣告類別，並且建立類別的物件，字首`o`就可以用來表示一個變數為該類別之物件。在宣告類別時，也可以需告屬於該類別的屬性，這些屬性的名稱最好以`m_`當作字首，以便在該類別之成員函數中能夠辨認一個變數是區域變數還是該類別的屬性。
```C++
class myClass{
    // private fields
    private:
        int m_iId;

    // public methods
    public:
        myClass(int iId);
        void printId();
};  // End of class myClass

myClass oClass; // 這個變數的字首 o 用來表示該變數為一個物件
```
從上面的例子中可以看到，`m_iId`的字首為`m_`，這表明了該變數為類別`myClass`的屬性，而且後面的`i`也說明該變數的型態是`int`。

在C語言中字首`o`也適用於結構變數：
```C
struct Rectangle{
    int iX;
    int iY;
    int iWidth;
    int iHeight;
};

struct Rectangle oSquare;    // 這個變數的字首 o 用來表示該變數為一個物件
```

最後要說明的是指標變數，只要是指標變數，其名稱的字首必須使用`p`，後面再根據所指向之資料的型態來給予字母。
```C++
int *piId;
float *pfMoney;
myClass *poClass;
```

### 類別命名規則
為了能夠與變數區別，類別的名稱字首最好使用`my`，而且要採用小寫駝峰式命名法來命名。若該類別是抽象類別，最好在名稱中使用`Abstract`或`Interface`字段，以便分辨出該類別是一般的類別還是抽象類別。

以下的例子說明一般類別的名稱的命名方式：
```C++
class myCat{
    // private fields
    private:
        std::string m_sName;

    // public methods
    public:
        Cat(std::string sName){
            m_sName = sName;
        }   // End of constructor

        void run(){
            printf("%s run!", m_sName.c_str());
        }   // End of run
};  // End of class myCat
```
上面的例子中宣告了一個類別來模擬貓所擁有屬性和行為，這個類別的名稱命名為`myCat`。

以下的例子說明抽象類別的命名方式：
```C++
class myAbstractAnimal{
    // public methods
    public:
        virtual void walk() = 0;
};  // End of class myAbstractAnimal
```
上面的例子中宣告了一個抽象類別，這個抽象類別擁有一個抽象成員函數`walk()`，這個類別的名稱命名為`myAbstractAnaimal`。

### 命名空間
在C++中可以定義命名空間(namespace)，命名空間的名稱建議採用駝峰式大寫命名法。以下是一個例子：
```C++
namespace Geometric{
    struct Point{
        int iX;
        int iY;
    };  // End of struct Point

    struct Circle{
        struct Point oCenter;
        float fRadius;
    };  // End of struct Circle

    struct Rectangle{
        struct Point oPosition;
        int iWidth;
        int iHeight;
    };  // End of struct Rectangle
}   // End of namespace Geometric
```
在上面的例子中，宣告了一個命名空間來包裹三個結構`Point`、`Circle`和`Rectangle`，他們分別用來描述幾何學中的點、圓形與矩形，該命名空間則命名為`Geometric`。

### 巨集常數命名規則
在C/C++中可以使用`#define`來定義一個常數，這種常數的命名方式最好是由多個單字與`_`組成，每個單字的字母可以全部都是大寫，也可以只有字首是大寫。重點是每個單字中間必須使用`_`隔開。

以下是巨集常數的範例：
```C++
#define Num_Of_Array 100
#define DATA_PATH "../save.data"
```
在上面的例子中，第一個用`#define`定義的常數`Num_Of_Array`是一個整數常數，用來表示一個陣列的長度；第二個用`#define`定義的常數`DATA_PATH`用來表示一個檔案的路徑。

### 函式命名規則
不管是一般的函式，或是隸屬於某個類別的成員函式，名稱最好採用小寫駝峰式命名法。此外，單字最好包含動詞，因為函式被定義來達成某件事，是一個用來完成某件事的動作。

以下是幾個例子：
```C++
int readFile(const char* pcPath, char *pcLoadedContext);
void writeFile(const char* pcPath, const char *pcNewContext);
```
在上面的例子中，第一個函式命名為`readFile`，用以讀取一個檔案的內容；第二個函式命名為`writeFile`，用以把一段文字寫進一個檔案中。