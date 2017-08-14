---
title: 網路安全(1) - 基礎密碼學
date: 2017-04-16 13:34:50
tags: security cryptography
author: jyt0532
---

### 基礎密碼學

密碼學裡面有兩種加解密方式，一種是對稱性加密，一種是非對稱性加密。差別很簡單，對稱性加密，意思就是我加密跟解密用的是同一個鑰匙，所以只要 A 跟 B 都知道這把鑰匙，A 要傳給 B 的時候，就用這個鑰匙加密。B 拿到了之後再用同一個鑰匙解密，你們兩就可以互通有無，暗通款曲。即使有中間人攔截了你們的傳輸，只要他沒有你們的鑰匙，他就不知道你們在幹麻。

那 bug 是什麼呢？阿哈！那你們要偷來暗去之前，總是需要傳第一次告訴對方你們的鑰匙吧？要是第一次就被中間人攔截，你們不就沒戲了嗎？他攔截到偷偷記下來後再原封不動傳給接受方，那之後的所有攔截到的加密後的訊息，他都有你們共有的鑰匙可以解密了。那該怎麼辦呢？

### 非對稱式加密

英雄登場

非對稱式加密，就是每個鑰匙 pair 有兩個鑰匙，一個公鑰一個私鑰。

可以公鑰加密私鑰解密，也可以私鑰加密公鑰解密。

可以公鑰加密私鑰解密，也可以私鑰加密公鑰解密。

可以公鑰加密私鑰解密，也可以私鑰加密公鑰解密。

很重要所以說三次。

傳訊之前呢，A 跟 B 都先生成一組公私鑰的 pair，A 把 A 的公鑰傳給 B，B 把 B 的公鑰傳給 A。

好現在 A 有他自己的私鑰跟 B 的公鑰，B 有他自己的私鑰跟 A 的公鑰。
**A 要傳東西給 B 就用 B 的公鑰加密，然後 B 拿到之後用 B 自己的私鑰解密。**
一切搞定，即使在中途被攔截，只要 B 的私鑰沒有流出就完全不會有事。反之亦然。

中心思想就是利用公鑰可以用來加密的特性，而如果你是用公鑰加密你**必須**要用私鑰解密。所以我根本不怕公鑰流出(公開給所有人也沒差)，只要我私鑰保存好就好，我私鑰根本就沒傳過，也不可能被攔截。

那為什麼這解決了對稱性加密的問題呢？因為第一次的傳輸也只互傳公鑰，所以即使公鑰被攔截，之後中間人拿到加密過的訊息也不能怎麼樣，因為那個要對方的私鑰才能解，所以一切都非常的美好...

![Alt text](/img/jyt0532/car_accident-iloveimg-cropped.gif)

這裡是個伏筆，等等會回來。第一次有伏筆會先自己說自己是伏筆，看到這裡你就知道這篇文章絕不簡單，非比尋常。

### 數位簽章

故事繼續，那既然大家都有 B 的 public key ，那任何人都可以用 B 的公鑰加密傳訊息給 B，那 B 怎麼知道哪個是 A 寫的哪個是別人偽造的呢？這裡就要引進數位簽章的概念。

但說破哪值幾文錢？數位簽章就是 A 在傳送訊息前，用 A 的私鑰加密，傳給 B。B 再用 A 的公鑰來看是不是真的是 A 簽名的(事實上是對內容的 Hash 簽名，不過為了講解方便，就先當直接對內容簽)。

這裡運用的是**可以公鑰加密私鑰解密，也可以私鑰加密公鑰解密**的第二個特性。

你說公鑰所有人都有怎麼辦？反正全世界都知道這訊息是A的訊息也沒關係，只要沒有 B 的私鑰就沒有其他人可以看到內容！

### 融會貫通

驗收成果的時間來了。所以今天 A 要傳訊息給 B 要怎麼樣讓這訊息**只有 B 看得到，而且 B 也確定是 A 寫的**不是別人寫的呢？

=================================================================

一開始 A 先把 A 的公鑰傳給 B，B 先把 B 的公鑰傳給 A。

A 要傳給 B 之前，把要傳的內容，先用 B 的公鑰加密再用 A 的私鑰簽，然後 B 用 A 的公鑰確認簽章再用 B 的私鑰解密內容。

=================================================================

簡單吧！恭喜你已經比大多數人基礎都好了。

### 說好的伏筆呢

如果今天出現了一個神級的中間人，還是有辦法破解，怎麼破呢？

1. 在一開始 A 要傳送 A 的公鑰給 B 的時候，要是被 C 攔截，C 就可以爽拿 A 的公鑰。然後呢？自己再生個公私鑰 pair，把 C 的公鑰 1 給 B，跟他說這是 A 的公鑰。
![Alt text](/img/jyt0532/cryptography-step1.png)
2. 在一開始 B 要傳送 B 的公鑰給 A 的時候，要是被 C 攔截，C 就可以爽拿 B 的公鑰。然後呢？自己再生另一個公私鑰 pair，把 C 的公鑰 2 給 A，跟他說這是 B 的公鑰。
![Alt text](/img/jyt0532/cryptography-step2.png)
3. A 和 B 以為拿著對方的公鑰。
![Alt text](/img/jyt0532/cryptography-step3.png)
4. 今天 A 要傳給 B，他以為他用 B 的公鑰加密(事實上他用了公鑰 C2)，再用 A 的私鑰簽，想傳送給 B 但被 C 攔截。
![Alt text](/img/jyt0532/cryptography-step4.png)
5. C 用 A 的公鑰驗證，用私鑰 C2 解密之後，爽看內容，然後他想怎麼改寫就怎麼改寫。
6. 改寫完後，用 B 的公鑰加密後再用私鑰 C1 簽章。
![Alt text](/img/jyt0532/cryptography-step6.png)
7. B 拿到後，他用 A 的公鑰確認(事實上這是公鑰 C1)然後再用他自己的私鑰解密。
8. 一切神不知鬼不覺。

![Alt text](/img/jyt0532/change_face.gif)

### 破解中間人攻擊

要怎麼避免神級中間人的攻擊，其實非對稱式協議唯一的 bug 就是一開始公鑰交換的時候可以動手腳，所以解法就是只要在互傳公鑰的時候，有一個可信的第三方可以證明說一開始你拿到的公鑰是真的就可以。

就是 A 跟 B 要傳訊息之前互傳公鑰的這個步驟，A 拿到 B 的公鑰之後，去一個有公信力的人那邊問一下，嘿！這個真的是 B 的公鑰嗎？他說是你就可以信，他說不是那就可能被中間人攻擊，就這麼簡單。

恭喜你已經精通了中間人攻擊，對網路安全其他主題有興趣的讀者請到延伸閱讀收看續集。

## 延伸閱讀

1. [網路安全(2) - HTTPS](https://www.jyt0532.com/2017/03/08/https/)
2. [網路安全(3) - SSH](https://www.jyt0532.com/2017/03/09/ssh/)
2. [用什麼樣的密碼比較安全呢](https://www.jyt0532.com/2017/02/19/password-security/)

關於作者：
[@jyt0532](https://www.jyt0532.com/) 後端工程師，喜歡學習新知挑戰新事物，最近覺得 Anti pattern 比 Design pattern 有趣。