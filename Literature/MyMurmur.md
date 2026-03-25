**0319

1、Trello 上放的是實習回報，要注意的是General 跟 meeting time

2、下次見面問教授，是不是要把自己的時間安排都放在Trello上? 如果想準備實習又要怎麼做?

3、LINE群組中的github某些連結打不開，是沒有公開嗎? 還是說先不關我的事?

4、安裝模擬軟體是否有作業平台的限制? 要再搞一個LINUX來玩?

5、建立好github自己的repo，目前裡面有3個檔案：待讀資料、好用連結、自己的碎碎念

6、分類好待讀資料的優先順序

7、閱讀第一篇文獻資料，5G from Space: An Overview of 3GPP  Non-Terrestrial Networks   [Introduction 1 ]

-> 何謂3GPP? 

行動通訊的第三代規範。第三代，3rd Generation，合作夥伴計畫 Partnership Project

-> 大綱是啥? 

過去的5G通訊依靠地面基地台，現在想辦法離地，那就是要靠天上飛的衛星

-> 3GPP設立的15、16、17版本，依序有啥區別? 

15版本搞出地面5G，16版本加強，17版就是把5G用在非地面網路 Non-Terrestrial Networks (NTN)

-> NTN種類有哪些? 

衛星(satellite)、高空平台 (high altitude platform systems (HAPS))、地對空網路(air-to-ground network (ATG))

-> 衛星軌道有哪三種? 有哪些優缺點?

     類型	  全名	     高度	              特性
     
     LEO	  低軌道	     ~500–2000 km	      延遲低、速度快
     
     MEO	  中軌道	     ~2000–35000 km	    折衷
     
     GEO	  同步軌道	   ~35786 km	        延遲高但固定不動

-> 為何低軌衛星會紅? 為何要與3GPP結合? 

從經濟規模出發，越高越會Lag實用性差，但覆蓋面積大，低軌衛星剛好相反；用到3GPP是因為不想要重新設計新的協定

-> high altitude platform systems (HAPS) 是啥? 

   高空平台，可視為平流層的基地台
   
   代表：巨大的太陽能無人機、氣球
   
   高度：約 20km，還在大氣層內
   
   延遲：極低，距離地面近，傳輸時間與地面基地台相近
   
   覆蓋範圍：中

-> LEO 是啥?

   低軌衛星，Low Earth Orbit
   
   代表：Starlink、OneWeb
   
   高度：500km ~ 2000km
   
   延遲：中，比地面基地台慢，可用於視訊通話
   
   覆蓋：廣，但因為飛得太快，需要很多顆衛星輪流接力

-> GEO 是啥?

   同步軌道，Geosynchronous Earth Orbiting
   
   代表：氣象衛星、傳統衛星電視
   
   高度：36000km
   
   延遲：高，訊號用光速跑一趟來回需要半秒鐘
   
   覆蓋：超廣，只要3顆GEO衛星就能覆蓋全球除南北極以外的所有地方
   

   
**0325

1、回應教授的草稿

     Prof. Gu 您好

     我是 Richard，目前就讀台科大電子系大二

     我正在為接下來的專題做前期準備，目前的研究興趣集中在 5G/6G 非地面網路 (NTN) 領域。未來預計會針對實驗室的 "OAI NTN 開源程式碼" 進行深入研究與實作

     這幾週我正在研讀 3GPP 關於 NTN 的文獻，並在 GitHub 上建置相關研究筆記。很高興認識您！

2、繼續閱讀第一篇文獻資料，5G from Space: An Overview of 3GPP  Non-Terrestrial Networks    [Introduction 2 ]

-> 幾個關鍵英文縮寫

   New Radio (NR)
 
   narrowband IoT (NB-IoT)

   Long-Term Evolution (LTE)

-> What is NR?

   New Radio，5G的全新無線電標準

   forward compatibility, support for low latency, advanced antenna technologies, and spectrum flexibility

-> What is LTE?

   4G 行動通訊標準，長期演進技術

   3GPP is studying the feasibility of adapting NB-IoT and LTE-M to support NTN

-> What is the core of the article? compare to another essay?

   multiple areas from radio access network to services and system aspects to core and terminals

   design rationale

3、閱讀第一篇文獻資料，5G from Space: An Overview of 3GPP  Non-Terrestrial Networks    [II. RADIO ACCESS NETWORKS FOR NR NTN 1 ]

-> Key words

   Rel-15 study

   S-band 

   Ka-band 

   Earth-fixed beams

   moving beams

   handheld

   Very Small Aperture Terminals (VSAT)

4、研究如何製作流程圖的網站 Draw.io (diagrams.net)

5、蒐集一些連結到 NTN.index
