## Tonyq Wang (tonyq)
- 自己開發 termianl 工具： https://github.com/tony1223/better-agent-terminal
我把 overmind 的精神整合到 bat 了，不得不說這效果好到爆表！
spec的部份是這個: https://github.com/DarthSim/overmind?fbclid=IwY2xjawRY43tleHRuA2FlbQIxMABicmlkETF2SVY1WDBpdWdiS2FWZXhFc3J0YwZhcHBfaWQQMjIyMDM5MTc4ODIwMDg5MgABHssMZixRvNXuWS9ofmEShTThjORz9KAtTpwLnO77Y6slSSwU27ot_O6DM3Q9_aem_l4YSeWW98Pogbm3UTKvMIQ

工具的話是: https://github.com/tony1223/better-agent-terminal?fbclid=IwY2xjawRY45RleHRuA2FlbQIxMABicmlkETF2SVY1WDBpdWdiS2FWZXhFc3J0YwZhcHBfaWQQMjIyMDM5MTc4ODIwMDg5MgABHt89m9IkdVJBbEqnm1YpqyXqYc161R0WhY5-IU-lP1Of60VQFaijh2xQ9_Y__aem_A8kHD2yJt1SvMitzNKFIxQ
我把後者整合了前者的規格
而且這解決了一個問題，就是有些情況你就是希望workspace 維持某些服務持續啟動方便快速驗證......


我為什麼沒有早五年知道這東西。
這東西可以用在假設你 前端 後端 docker （可能db）需要分開執行的情況下，你寫好設定檔(procfile） ，他可以幫你開多 process執行，並把 log 聚合。
- BAT codex agent 這個模式我已經調到可以用了，至少我自己用起來在 gpt 5.5 的模式下是可用的。
- 我是有點猶豫要不要再弄個 plan mode 出來，但是目前就算沒有，我覺得整體透過 prompt 引導還是可以順的。
- 觀察模型行為：gpt 5.5 在這點上驚豔的是，他幫我把反覆跑完之後的髒髒 repo 重新整理過，並且重新設計實驗條件，在一個很 solid 而且漂亮的實驗環境跟參數設計的前提下相當快速的利用了既有的資料逼進了前提。而且在某個關鍵的問題上選擇了看起來是正確的方向，成功地躲過了其他之前挑戰者掉進去的坑......他對待實驗跟分切實驗的方法，我覺得是一種全新的思考，也就是他自己就具有某種疊代性的思考，他會去想如果他失敗了怎麼切、他成功了怎麼保留，把環境設計成一種具實驗條件的環境，而且重視一些基礎的測試條件探索，具備著一定的自我驗證能力，而不需要你持續的引導他自我驗證。在這點上我覺得取代掉了很多 flow 本身的引導。
- plan 的視角掃專案。
- 注意看 claude code or Codex 的改版：最值得注意的：native binary 架構改動 (113)、效能提升 (116-117)、Opus 4.7 context 修復 (117)、vim visual mode (118)、設定(/config) 改成寫檔 (119)。請更新 claude code 到最新版，116版有重大更新把 BAT 更新到 v2.2.19  會跟上這個版本。
- 自己做小工具： eclipse  plugin ，直接對 SQL 的搜尋理解小工具，但至少初步POC 還是挺不錯的，不知道有沒有其他DB類的工具能做到了，
使用工具，開源的 dbeaver ce ，開發的是 eclispe plugin，整合 claude code sdk。 （所以要有買cladue code 才能用）
demo 使用的是範例資料庫，不牽涉實際資料。
技術原理是透過 plugin 開內部 MCP 把當前 editor 對應的 connection 跑 DDL 資料、進行查詢，在這個封閉狀態下用 MCP 做隔離層是比較乾淨的選擇。
算是為之前的 MCP/cli 議題找到一個反例。
- 昨天看了看 opencode 的流程，計畫在 BAT 裡面整一個平行相近的版本。（有一些關鍵組件不太一樣）
但我昨晚放著給他跑，早上稍微整理一下發現，這是時間黑洞。
 codex agent 給的資料被封裝掉的東西太多了導致他很像半成品。從這個基礎作很多事情都做不出來（比方說沒有 plan mode 是個重傷，沒 plan 我已經不會用了。）
直接操作 codex cli，預計之後大概會有大幅的改版把我的東西全部改壞。
用 open code 路徑就是要怕模型商翻臉跟 claude code 一樣不給用。 XDD
真要說做這件事情的好處是真正把 context management 的理解往前推一步，你想過你下的每個 message / tool call 怎麼變成 context 的嗎？怎麼壓縮、怎麼放，怎麼產生效果。
這一步的關鍵是要掌握這件事情。
- codex 想要拿更多東西，可能得用 client / server 的方式來啟動，我還為了這件事花了不少力氣做了一個 codex-shim（已踩坑）。
不過 codex 是有 plan mode 的，而且比 claude code 還聽話，我已經好幾次看到 claude code 在 plan mode 就直接開始寫 code 的。
codex cli 有 plan mode ，但是 codex cli 的中斷感太煩了
你的作法是走進 qty 層在那層跟他互動，我覺得那也是個聰明的方法之一，但是我的工具再上一層，我不想為這件事情跳層 ...XD
對我來說，走去那層我前期投資就都打水飄了。
白話文，走去 cli層的話就是所有的 hook 都得自己來

- 觀察Agent Coding 改版
claude agent SDK 在最近五個版本都在去重量，蠻有趣的，不知道為什麼。
1. 把對 claude cli.js 的依賴拔掉，變成用 native binary ，這個他們 cli 已經推了30個版本以上了，算是官方政策，不意外。
2. sdk 本來會內建綁一個 claude code cli 在裡面，現在改成可以用環境的 cli 了（或者用env 指定cli path）。
就我的經驗，會作這樣的變化，表示他們應該有啥東西把SDK裝到一個新的環境，並且卡到了一些什麼事情了。
應該可以期待一下之後其他服務版本，有什麼是整合 agent sdk 的殺招？

- 開始用 codex 之後，我多做了一個 /auto-continue 指令
這個功能很簡單，偵測到 codex 停下來之後送一句 prompt 叫他繼續，可以設次數...

- 最近很頻繁使用 procfile
因為我自己算是很重視啟動速度跟 restart效率的人，所以每到或開發一個新專案，我自己都是第一時間會檢查開發環境的情況。
過去常常就是一個環境setup要作很多事情，弄資料庫、弄環境、弄 docker compose、弄 tls、跑什麼神奇 npm run 指令、跑 cloud sql、跑 cron 。
很多時候真的是挺煩的。
而且很多都是 stateful，也就是會占用掉一個 terminal 去執行，然後就回到當初作BAT的根本問題，一個 project 用了三四個terminal，三個project 十幾個terminal，喜咧考。
後來有陣子用 tmux named session，狀況是好一點了，但缺點是要背好多 key，專案的名字本身就是那個 key，為什麼要花腦袋寶貴的 memory buffer 處理他。
最近用 procfile，他可以把多個 process merge在一起，以cli的形式，然後可以局部重啟，可以界面整合。
一個畫面處理完過去所有的雜事，再搭配 AI 可以幫我寫指令。
我只能說真的相見恨晚..................................
我已經三四年沒有看到過這種相見恨晚的東西了。大概可以跟 git , di 工具並列吧。

- 見解
下面是一版「**原文引用 + 簡單解釋**」的整理，語氣盡量保留原本評論的味道，但讓讀者比較容易看懂。


-context managing 本質是戰爭迷霧的管理。
所以要先把所有問題投影成一個可以觀測的維度（維度越低解的越快），然後把解過的問題紀錄，採用DP的邏輯快取已經有過得答案再往前進.....
但是最可怕的其實不是這種問題，是不存在答案的問題，沒有答案的話任何探索都兼具有效跟不有效的特性。
而投影方法的選擇本身則直接暗示了你發問方所預設的答案，如果這個預設錯誤，最後答案會全錯...........
這也是為什麼駕駛重要，確認大方向的正確從來沒有比過往更重要過了。

- 目前用起來的初步差異：
codex 的開發模式跟 claude code 風格有些細節上的不同。
claude code 比較目標導向，codex 的邏輯是另一種風格。
我剛拉住他，叫他不要急著抽共用核心（因為這樣另外一邊要重測），不然我今天大概都得耗在這件事情的驗證上了。
就是老派架構師的思想 XDDD 我大概可以看得到某種流派的影子
---

# 技術評論重點：原文引用與簡單解釋

## 1. CLI Agent 比網頁版更能發揮模型能力

> 「我說 cli agent 大於 網頁版，是基於 input 的落差。cli 才是最能發揮模型威力的玩法。」

簡單說，這個觀點認為：
**CLI Agent 比網頁版強，不是因為聊天介面比較酷，而是因為它能直接接觸真實工作環境。**

CLI 可以讀檔案、看專案結構、跑指令、看錯誤訊息、改程式、跑測試。
網頁版大多要靠人把資訊整理好再貼進去，所以 input 品質差很多。

---

## 2. Cursor 類工具有幫助，但提升有限

> 「至於 cursor 跟其他方案的問題是，他們把焦距縮窄了，只在專案裡面，然後他們試圖省token的方法有很明顯的副作用，所以我沒有非常推薦。」

簡單說，作者不是說 Cursor 沒用，而是覺得它的幫助有限。

Cursor 比一般編輯器聰明，但它主要還是圍繞在 IDE 和專案檔案裡。
而且它為了節省 token，可能會自動壓縮或挑選 context，這會讓模型少看到某些重要資訊。

---

## 3. Cursor 類工具大概提升 15%–30%，但 Claude Code 類 CLI Agent 可能翻倍

> 「但他們有沒有幫助，基本上就是有，我之前的評估是15-30％左右。有幫助，但不會到你想大力引用的程度，人為的介入感還是很重。」

> 「但 claude code 這類 cli agent 方案，我保守估計 100％ 真的沒問題。」

簡單說，作者認為：

Cursor 類工具有加分，但還是很需要人一直介入、整理、指揮。
Claude Code 這類 CLI Agent 則更像真正進入工作流程，能自己讀、改、跑、驗證，所以提升幅度更大。

這裡的「100%」可以理解成：
**在某些開發任務上，工作效率或產出能力可能接近翻倍。**

---

## 4. Antigravity 要看模型搭配與供應風險

> 「Antigravity 搭配 google 自家模型當然是有加成的（就如同claude code+claude 自己模型一樣概念）。」

> 「搭配其他模型，那就要考慮成本問題跟會不會有一天被斷供（莫忘Windsurf 當初被斷供的事件）。」

簡單說，作者認為 Antigravity 這類工具如果搭配 Google 自家模型，會有整合優勢。

但如果它依賴外部模型，就會有兩個問題：

一是成本可能不好控。
二是模型供應可能有風險，未來不一定能穩定使用。

---

## 5. 現階段不太看好多 Agent

> 「但我自己這個階段幾乎完全不買單多 agent 概念，我覺得未來某個時間點多 agent 可能會是下一個讓人驚艷的東西，但此刻就我自己覺得他就是個控制起來非常累而且不好掌握 token 的東西。」

簡單說，作者不是否定多 Agent 的未來，而是覺得現在還太早。

多 Agent 聽起來很強，但實際用起來可能很難控制：

* 每個 Agent 要吃 context
* 每個 Agent 都會花 token
* Agent 之間還要溝通
* 最後不一定比單一 Agent 更穩

所以現階段作者比較相信「把單一 Agent 用好」。

---

## 6. 目前真正的核心問題是 thinking token 怎麼分配

> 「主要的問題是，目前主流的 agent 目前在挑戰的其實是『thinking』要放多少空間給他去使用token。」

簡單說，現在 coding agent 的核心挑戰，不只是模型會不會寫 code，而是：

**要讓模型想多久、想多深、花多少 token 去思考。**

thinking 多一點，品質可能變好。
但 thinking 太多，就會變慢，也會消耗 context。

---

## 7. Thinking 會提升品質，但也會變慢、吃 context

> 「thinking 雖然可以強化品質，但同時也會拉長時間（等於一個內心戲很多要等他演完的開發者），而且還有一件很殘酷的事情是 read/think/write 都佔 context。」

簡單說，這段講得很關鍵。

AI Agent 的 context 不是只放使用者輸入，它還要放：

* 讀到的資料
* 思考過程
* 最後寫出來的內容

所以如果 thinking 佔太多空間，就會壓縮其他資訊。
作者用「內心戲很多的開發者」形容很貼切：他可能想得比較完整，但你也要等比較久。

---

## 8. 未來競爭會在 context、token 效率、thinking 比例

> 「接下來可以預期的可能方向是，一個是 context windows 有沒有機會再打開，另一個就是 token 有沒有機會作的更有效率……最後就是在 think 的比重上找到更適合的黃金比例。」

簡單說，作者認為接下來 AI Agent 的進步會集中在三件事：

第一，context window 能不能更大。
第二，token 能不能更有效率。
第三，thinking 的比例能不能抓得更剛好。

不是只要模型變大就好，還要看它能不能更有效地使用有限的 context。

---

## 9. Claude Code 有先行者優勢

> 「在這點上 claude code 自己同時又是出題者又是回答者，其實先行者優勢是比其他人明顯的。」

簡單說，作者認為 Claude Code 的優勢在於：

Anthropic 同時做模型，也做 Claude Code 這個 coding agent 工具。
所以它可以從真實使用中學到很多問題，再回頭改善模型和產品。

這種「模型 + 工具 + 實際使用回饋」的閉環，會形成優勢。

---

## 10. 作者不太信任 benchmark，更相信實際使用

> 「我並沒有非常信任跑分的結果，現在跑分的 benchmark 跟實際的使用還存在著一定的落差……現在我也覺得應該以實證為主。」

簡單說，作者認為排行榜和跑分只能參考。

真正重要的是：

**在實際專案、實際需求、實際工作流裡，它到底能不能幫你完成事情。**

這也是為什麼他重視 CLI Agent，因為 CLI Agent 更接近真實工作現場。

---

## 11. 多 Agent 其實是在切 context，但不一定更聰明

> 「因為現在多agent的核心方向是，把一個大的 context 切割成多個子 context……但實際上這是一個『變笨』的角度。」

簡單說，多 Agent 常見做法是把工作拆給不同 Agent。

例如：

* 一個處理前端
* 一個處理後端
* 一個處理文件
* 一個做整合

看起來很合理，但作者認為很多高價值任務需要的是「綜合判斷」。
如果切太碎，每個 Agent 只看到局部，反而可能失去整體理解。

---

## 12. Skill 比多 Agent 更重要

> 「怎麼讓這些context適當的壓縮跟傳遞，這就是 skill 在扮演的角色。」

簡單說，作者認為真正重要的是 skill。

Skill 可以把某些經驗、流程、規則、工具使用方式包起來，讓 Agent 在需要時調用。
它比較像是把人類經驗濃縮成可重複使用的工作能力。

所以作者比較看重：

**單一 Agent + 好的 Skill + 好的 Context 管理。**

而不是急著做很多 Agent。

---

## 13. 現在先把單一 Agent 用好就很有價值

> 「我是覺得現在還沒有需要那麼急著挑戰多agent，因為單一agent跟單一工作queue在一定難度的問題上，價值已經非常顯著。」

簡單說，作者的策略是：

先不要急著追多 Agent。
現在把 Claude Code 這類單一 CLI Agent 用好，就已經可以產生很大的生產力提升。

如果真的需要分工，也可以開多個 Claude Code session 分開做，不一定要塞在同一個多 Agent 系統裡。

---

## 14. 現階段重點不是省 token，而是用 AI 創造收入

> 「這個時間點的最佳策略，我覺得並不是努力白嫖。而是努力賺錢，趁著還有快錢可以賺。」

簡單說，作者最後的觀點很務實：

不要只想著怎麼省 token、怎麼白嫖工具。
更重要的是，趁 AI 還能帶來明顯效率差距時，把它變成產值。

也就是說，AI 工具的成本不是最大問題。
真正的問題是：

**你能不能用 AI 做出更高價值的工作，甚至賺回工具成本。**

---

# 總結一句話

這篇評論的核心意思是：

> 現階段最值得投入的不是多 Agent，也不是單純追求便宜 token，而是把 CLI Agent 用到極致。因為 CLI Agent 能接觸真實工作環境，input 品質高，能跑指令、改程式、驗證結果，才真正能放大模型能力。至於多 Agent，現在還太難控、太吃 token，未必比單一強 Agent 更有效。


- 趨勢
其實我覺得有個趨勢已經很明顯了，那就是企業即將普遍擁有自建系統，而不是過去的把系統建在別人家。
企業利潤將由多層次的平台各自分潤，轉換為體系內的垂直分潤。
而資料的自主權將會是接下來的重點，除了 ai 公司以外，要調查產業資料，將會更為困難。

- 我真的覺得.............. claude deisn ...........很強, "我想要wallet app view的設計稿  , 可以給 claude code套用的"
這真的堪稱恐怖, 我丟給他的只有一個 react native project....
他自己搞懂了 react native project 的 view 並完成了設計。
我真的很難想像他真的靠著一個資料夾讀對了畫面做對了設計......恐怖啊.....

- 我覺得模型離[被控制]還太早了，畢竟模型的本體還在持續發展，看到 sonnet 4/ opus 4.5 / 4.6 這些躍進，我是一直覺得我們只是在一段長長的旅途中的半路。
今天看到 4.7 也更加強我對這件事情的看法。
我知道很多人在這個階段很在意治理，但我們類比到人類來看好了，你會很在意小朋友在五歲前的治理秩序跟能力嗎?
我的角度是，只要安全沒有問題，剩下的應該是讓他們自由的探索，告訴我們他們的世界長什麼樣子，我們順著他探索到的地方決定允許或禁止。
所以對我來說，就是我帶著一個小精靈˙做我想做的事情，他想做的事情跟我不同路，我就阻止他，同路，我就讓他自己做。
搭配 git 等工具存檔。
所以我在這個階段基本上是全面放棄 test case，只有在非常窄的條件我才寫 integration test，而且比例大概佔不到總產出量的 0.1%。
相對的我投入大量的人類人工，去跟他打對台做關鍵場景驗證。當然這個場景驗證是經過設計的。
該怎麼說呢，也就是我讓他 cook 可能十個小時累積成我需要花一個小時積極的去測試跟驗證，而且這個驗證可以很有效率的確保他這十個小時是正常產出的(一部分也是靠他自己的規劃能力跟自我驗證能力)，然後我可以跟他達成1:10的驗證比。
我覺得這是我現在看待模型的關鍵....
- 我估計我今年大概不會再訂 jetbrain 了 QQ 主要是現在基本上沒什麼透過IDE build的需求，多數也都可以 cli build ...

- 這差不多是一個session 從休眠回來的標準狀態：
老 context 直接一個整批 write，然後每個 action (不是你每次輸入message喔，是ai 每做一個 read/ write /toolcall動作），開始用 context his 去敲打 cache read。
但有 cache 的前提下會小蠻多的，而這件事情跟 existing context size 成正比。
所以他不能單純的畫約成「一次」差多少倍，而是要加上模糊的「多少次」當前綴，變成一個線性等級的浮動成本數字。
（之所以real有差是因為左邊有些是用模型認為該 1h cache write 的價格數字，右邊是全都用 5m cache write 的價格，不知道為啥他現在價格計算跟他推薦的計算模式是不一致的，我是沒實際去測他到底是哪種，這也是最近在討論的事情。）
所以 5m cache write 不是只有缺點，他也有優點，成本會下降，
缺點就是沒 hit 到 cache 會很靠邀。


## 高健龍
-看著畫面上都是自己做的工具，讓我想到最近大家在討論的 Harness Engineering。其實我不太喜歡只停在名詞討論，我更在意能不能真的做出東西。
這幾個月花了不少力氣在做工具（現在還在做），慢慢驗證我的想法
與其說是在寫工具，更像是在做一個讓 AI 可以穩定產生 code 的流程生產線。
Spectra 負責定義「要做什麼」，而 Sherly 則負責讓這整件事情在一個順暢、順手的環境裡被執行。
不用管是哪一家的 Coding Agent，也不用在意模型怎麼升級。對我來說，AI 大廠打得越兇，這套流程只會越順、產出的品質只會越好。
這樣很好，Good！

- https://www.deeplearning.ai/.../spec-driven-development.../
一個多小時的影片可以看一下，看過之後再動手用 SDD 工具會更知道為何而戰、為誰而戰... 不是，會比較知道自己在幹嘛，而不是只是跟著敲指令而已

-原來 Spectra 真的有人在用耶（快 500 個 GH 星星了）！
昨天有個 Vue.js 的社群活動，活動結束後我只是去關個投影機，途中就被 3 個人問了關於 Spectra 的問題，其中一個還是 bug 回報（已修復）。
明明就是 Vue 的場子... 所以這年頭參加社群活動好像還是得多準備一些 AI 相關話題，不然都不知道該怎麼跟人搭話聊天
補個連結： https://spectra.5xcamp.us/?fbclid=IwY2xjawRY6WtleHRuA2FlbQIxMQBzcnRjBmFwcF9pZBAyMjIwMzkxNzg4MjAwODkyAAEe8CKmLn7wPeOQJgdHRDjUbR5nIu9JQrrsD9ihIcrCRHOW-_n6iXsv5rXPrDc_aem_s7eC8oR6kthZWM7Ohs7PXw

- https://x.com/trq212/status/2044548257058328723
記得在上課 & 公開分享的時候有提到 context window 不是越大越好，不然 Google Gemini 最早有 1M context 早就應該是最強的了（不過我在 Google 的社群場都會講 Gemini 最強）
重點在於「專注度」，那個 /rewind 指令在 context 管理方面可以幫上一些忙，這篇滿值得花點時間看
是說，不要再丟給 AI 看了，那是 AI 看不是你看喔

- Sherly 終端機現在可以知道正在執行的是哪一個 agent，除了給顏色跟 icon 之外，tab 也做了支援 OSC 0/2 可以吃到 app 丟出來的訊息，動態跟著改變 tab 的字樣（才知道原來各家 agent 都會送一些轉來轉去的符號出來）
還可以玩一些有趣的把戲，例如把 claude code 跟 codex 的 status line 串到 app 的 status bar，把空間留給終端機
另外，因應觀眾要求，所以我把寵物包的架構拆出來了，以後想要上架自己的寵物的話，只要給我一個 zip 壓縮檔，裡面的 assets 跟 manifest 寫好，之後就可以直接在下個版本發佈的時候跟著被大家看到了（這樣可以賣廣告版位的意思？）
今天上課還展示了一下終端機的沈浸式追劇模式，其實本意並不是要做這件事，後來發現用來追劇很方便，但 demo 的時候還是覺得有點恥

- 

## 其他
From "No to OpenClaw" to "Designer Posts a Figma Link, AI Auto-Creates Jira Tickets"
https://wchung.tw/blog/from-openclaw-to-openab-automating-design-handoff-with-ai?fbclid=IwY2xjawRXeqlleHRuA2FlbQIxMABicmlkETFWQVQ2WHMyRDZMTTJzM2R3c3J0YwZhcHBfaWQQMjIyMDM5MTc4ODIwMDg5MgABHoxoPdx26AsS-j9CHbbOvBO4LVU-nww0_JUEtS7NQWatim3u2bU3_lSJSwcG_aem_t1qaXjiSVpKqH--FUfnFug