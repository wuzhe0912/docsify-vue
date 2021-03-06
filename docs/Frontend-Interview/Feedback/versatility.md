# 泛用性考題

> 記錄面試遇到的泛用性考題

## 1. SPA 有哪些缺點？相較之下 SSR 有哪些優點？

### 優點

- 對使用者來說，`SPA`不再是過往`MVC`架構下，由`server`端產生頁面，而是改為`MVVM`的形式，自然就不需要每次更新頁面就全畫面重新渲染，只需要透過`ajax`抓取後端資料，動態更新一小部分區域即可，使用體驗良好。
- 也因為後端不需要考慮渲染問題`(view)`，除了團隊開發上可以分工更明確，除了可以降低後端效能的浪費，同時對頻寬的壓力也有所減輕，也就是相對不吃`server`資源。
- 既然前後端分離，對後端來說，資料本身就可以更具擴充性，只需要設計好`JSON`結構的接口，對`web`或`app`兩邊都可以重複使用，降低開發成本。

### 缺點

- SEO：因為頁面渲染的內容，都是透過`JS`動態抓取資料，早前的搜尋引擎在爬蟲時會抓不到`HTML`上的數據，但目前`Google`宣稱目前已經可以抓取(2021)，有待觀察。
- 首頁渲染效能：仰賴頁面載入`JS`後進行資料渲染，不可避免會有等待時間，自然會閃過整頁空白的畫面，使用體驗下降，尤其是弱網環境或是使用者手機硬體效能較差時，這部分的缺陷還會被進一步放大。

### SSR

- 為了改善前述`SEO`的缺陷，主流框架也提出相對的解決方案，`React -> Next`&`Vue -> Nuxt`，主要目的都是讓程式可以在`server`端先被執行過一次，讓`HTML`本身已經包含資料內容，這樣`SEO`的部分沒問題，再讓框架的部分依序執行，滿足`SPA`的使用體驗。

- 開發上，因為承繼原有框架的寫法與功能，對開發者而言，相對學習曲線較低，但是專案本身的複雜度會上升，除非是仰賴搜尋引擎的產品網頁，否則無此需求。一般後台`CMS`維持使用`SPA`即可，減輕維護成本。

## 2. localStorage & sessionStorage 的差異？

`localStorage`和`sessionStorage`都是從`HTML5`開始提供的`api`，儲存的容量大擁有`5MB`，也不會對效能產生問題，所以目前已用來取代`cookie`。同時兩者使用的`api`相同，前者永久保存除非手動清除，後者則是關閉瀏覽器即失效，因此多用前者居多。

### 實務情境

除了常見於保存登入的`token`，個人使用上還有兩種經驗：

- 1. 網站建立多語系功能，當使用者第一次進入網站時，會使用後台預設的語系，當使用者在前台頁面切換語系時，透過`localStorage`替使用者保存切換的語系，避免下次進入時失效，畢竟我們無法判斷客戶下次登入網站時間，所以這邊採用永久保存會是比較好的做法。

- 2. 賽事新聞網站，使用者會有自己心儀的球隊，希望可以每次進入網站時，都優先查詢自己喜歡的球隊，所以透過`localStorage`保存，使用者可以保存自己想要收藏的球隊資訊。同時可以根據需求，決定是否依據賽事`api`來改變當前球隊資訊，這樣在弱網環境下，若是只想讓使用者保存偏靜態的資訊，前端儲存除了使用體驗較好之外，也可以減少呼叫後端`api`。

## 3. 從輸入網址列到渲染畫面，過程經歷了什麼事？

- 1. 確認 IP 地址是否存在
     我們透過瀏覽器輸入一段網址，瀏覽器會根據這一串網址去呼叫`DNS Server`，讓`DNS`去確認這串網址對應的`IP`位置，如果沒找到自然就會結束本次查詢，但為了後面的內容，所以這邊是一定要找到的。`DNS`找到`IP`後會回傳給瀏覽器，瀏覽器開始連線到這個網址，接著進入第二階段

- 2. 透過網路請求資料
     這個階段基本可以理解為前端`(Client)`向後端`(Server)`拿資料。這個流程挺複雜的，我的理解拆為以下三階段：

  - 1. 打招呼：俗稱三次握手，`Client`向`Server`詢問是否存在，`Server`回應我在，接著`Client`宣告我準備要執行傳資料的動作囉。
  - 2. 傳送資料`(req & res)`：前端透過某個`method`向後端拿資料，這邊用`get()`，後端送封包過去後，再傳一組成功的狀態碼`(200)`給前端。
  - 3. 結束關閉：這階段俗稱四次揮手，`Client`向`Server`通知準備關閉，Server 向 Client 說 ok 我也要關閉了，並進行關閉，接著`Client`也進行關閉。

- 3. 取得資料，開始進行頁面渲染
     首先`HTML`和`CSS`會優先執行，前者開始搭建整個頁面`DOM Tree`，後者則是提前準備好所有樣式，接著來到`style` 階段，將樣式指到對應的元素，`Layout`開始排列頁面位置，最終完成頁面渲染。至於`JS`，為了確保使用者最低限度要能看到靜態頁面，所以依慣例我們會將`JS`的執行順序放至`body`底部，確保它最後執行。

- [詳細圖解](https://w3c.hexschool.com/blog/8d691e4f)

## 4. 瀏覽器兼容性方面，有哪些作法？

> 泛用性

- 開版時，針對專案進行樣式初始化。
- 透過`autoprefixer`盡可能`cover`需求方的主流瀏覽器，譬如所有市占率`0.5%`以上。
- 手動添加`CSS`前綴字來專門處理特定區塊在特定瀏覽器跑板問題。

> 客製化

瀏覽器兼容和裝置兼容性，兩者都很類似，很多時候要針對特定情境去找一些解法，而且解法可能會因為對方的版本更迭而失效，又得換個解法，舉例來說：

- 朋友詢問如何在`safari`禁用連點兩下縮放：

```
// 解法一：失效
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1,user-scalable=0"/>

// 解法二：失效
body {
  touch-action: pan-y;
}

# or

body {
  touch-action: manipulation;
}
```

但上面這些做法僅能使用在`2019-2020`年，當`ios`更新到 14 版時就失效了。最後想到的解法是，在元素上面添加一個`click()`事件，觸發一個`function()`並回傳空。
