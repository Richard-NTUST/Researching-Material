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

-> Key words

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

3、閱讀第一篇文獻資料，5G from Space: An Overview of 3GPP  Non-Terrestrial Networks    [II. RADIO ACCESS NETWORKS FOR NR NTN  A. Release-15 Study Item on NR NTN 1]

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


**0329

1、閱讀第一篇文獻資料，5G from Space: An Overview of 3GPP  Non-Terrestrial Networks    [II. RADIO ACCESS NETWORKS FOR NR NTN  A. Release-15 Study Item on NR NTN 2]

-> Key words

   Terrestrial Networks  (TN)
 
   Non-Terrestrial Networks (NTN)

   Multipath

   lineof-sight probability  (LoS)

   Angular Spread

   Delay Spread

   Elevation Angle

-> What is the most different between NT and NTN?

  The signal networks are multipath, so it can interchange form different router, while in NTN networks are parallel, angular spread is close to zero, so 3GPP in NT is not suit for here.

2、閱讀第一篇文獻資料，5G from Space: An Overview of 3GPP  Non-Terrestrial Networks    [II. RADIO ACCESS NETWORKS FOR NR NTN  A. Release-15 Study Item on NR NTN 3]

-> key words

   Free-Space Path Loss  (FSPL)

   clutter loss  (CL)

   shadow fading (SF)

   scintillation

   Clustered delay line  (CDL)

   Tapped delay line  (TDL)

-> What is the core information about Figure.2 ?

   The x-axis is elevation angle , the y-axis is path loss scaled in dB. 

   The more altitude, the more path loss.

   The higher frequency, the more path loos.

   The less elevation anglem the more path loss.  In 90 degree get the minimum of path loss.

-> What kinds of path loss mentioned in the paragraph ?
  
   Clutter loss, shadow fading, scintillation, distance, frequency

   Clutter loss means the singal is shield by building on the ground. It can be expected and considered as a constant.

   Shadow fading means the singal is shield by building on the ground. It can not be expected and usually related to relating position.

   Scintillation (閃爍) is describing that some rapid signal fluctuations caused by atmospheric anomalies. It is particularly severe at lower elevation angles.

   Distance is specified in free-space path loss (FSPL)
   
   Frequency's impact was describe in #194 .


-> If one want to simulate a satellite base station, what is the two channel setting it can select?

   For 3GPP, it can choose CDL and TDL.  The two setting parameter is used to model the path loss except to distance.


**0331

1、寫下午跟顧教授的減短自我介紹
     
     Hello Prof. Gu, nice to meet you. I am Richard, a sophomore here
     
     My project focuses on 5G Non-Terrestrial Networks, especially low earth orbit satellite
   
     Recently, I have been reading 3GPP specifications. I just learned how Elevation Angle affects Path Loss, and the concepts of TDL/CDL channel models.

     My next step is to study the OpenAir Interface NTN code. I know OAI is very famous in France, so I am very excited to learn from you today. Thank you!


2、整理今天下午的討論

     1、有出國機會，在二升三或三升四的暑假，是公司實習或交換，如果都要去的話要用不同企劃，錢錢學校跟實驗室會負責

     2、Trello是以前在用的，現在主要都改在github上回報

     3、Kenny學長做AI結合、richard做基地台攻擊、__學長做基地測量，先跟Kenny做，然後回報成果，在去做別人的，最後確定自己想做哪個

     4、認識Joanna學姊，了解NTN群組11人是誰

     5、確認系排50%以內高機率可以留下來


     6、之後每兩星期開會回報進度，時間與如何進行之後在群組討論

     7、認識另外兩位專題同學，要了聯絡方式，之後創google文字編輯器互相聯絡



**0427

1、過去一個月在準備期中考，但我沒有百分百善用時間，感覺有點在逃避這裡

2、閱讀第一篇文獻資料，5G from Space: An Overview of 3GPP  Non-Terrestrial Networks    [II. RADIO ACCESS NETWORKS FOR NR NTN  B Release-16 Study Item on NR NTN 1]

-> Key words

   Rel-15

   Rel-16
   
   3GPP TR 38.821

-> What is the main information mentioned in this paragraph?

   Previous version Rel-15 defines some parameters that how NR supports to NTN, now Rel-16 is finding the least efforts to put 5G in the space.

   What the Rel-16 modify are architecture, higherlayer protocols, and physical layer. The details stores in 3GPP TR 38.821.

3、閱讀第一篇文獻資料，5G from Space: An Overview of 3GPP  Non-Terrestrial Networks    [II. RADIO ACCESS NETWORKS FOR NR NTN  B Release-16 Study Item on NR NTN 2]

-> key words

   Next-generation RAN (NG-RAN) 

   5G base station (gNB)

   central unit (CU)

   distributed unit (DU)

   user plane (UP)

   control plane (CP)

   medium access control (MAC)

   radio link control (RLC)

   packet data convergence protocol (PDCP)

   service data adaptation protocol (SDAP) 

   discontinuous reception (DRX)

   hybrid automatic repeat request (HARQ)

-> What is the main information mentioned in this paragraph?

   Moving 5G communication system to space is avaliable, but long propagation delays must cause some question in some aspects.

   For SDAP, it is not necessary to introduce any modification.

   For PDCP and RLC, it need to enhance status feedback and sequence numbers.

   For MAC, HARQ and DRX should have a great change for they works relying on timer.

4、閱讀第一篇文獻資料，5G from Space: An Overview of 3GPP  Non-Terrestrial Networks    [II. RADIO ACCESS NETWORKS FOR NR NTN  B Release-16 Study Item on NR NTN 3]

-> Key words

   idle mode

   user equipment (UE) 

   very small aperture terminal (VSAT)

   Doppler shifts

   moving cells

-> What is the main information mentioned in this paragraph?

   UE's communication via satellites moving around fast in the space will track area frquently and UE's interface will asssume you are moving fastly, which cause to run out of battery.
    
   The solution is to define cell selection as additional measure parameter.

   Satellites moving around fast in the space also impact UE's handshake.

    UE	                    antenna	          approach	     
     
    Handheld UE	          weak                S-band
       
    VSAT / Phased array	     strong              S-band / Ka-band

-> what is the three physical problem to overcome?

   Long propagation delays 

   Large Doppler shifts, it means fast movement cause to signal's frquency be extended or shrink

   Moving cells, gNB also moving in the space rather than stationary on the ground.
   
