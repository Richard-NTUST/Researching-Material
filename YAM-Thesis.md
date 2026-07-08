
---

這篇論文題目是：
**「透過O-Cloud上O2基礎設施管理，為O-RAN建立自動化CI/CD測試框架」**

有三層陌生感：
1. **電信網路本身**（5G、基地台架構）
2. **O-RAN這個開放標準**（什麼是開放、為什麼要開放）
3. **這篇論文在O-RAN裡做什麼**

---

## 建議的閱讀路線圖（Roadmap）

```
第一層（背景）
  └→ 5G網路基本概念
  └→ 什麼是RAN（無線接入網）

第二層（O-RAN標準）
  └→ O-RAN是什麼、為何要開放
  └→ O-Cloud、SMO、IMS、DMS是什麼

第三層（這篇論文）
  └→ 問題是什麼（動機）
  └→ 解法是什麼（FOCOM、NFO、CI/CD、Sideload）
  └→ 實驗怎麼做、結果是什麼
```

---

## 講解順序

| 步驟 | 對應論文頁 | 內容 |
|------|-----------|------|
| ① | 第3-5頁 | O-RAN是什麼、為何需要它 |
| ② | 第5頁 | 有什麼挑戰（問題定義）|
| ③ | 第6-9頁 | 相關背景技術 |
| ④ | 第10頁 | 這篇的解法總覽 |
| ⑤ | 第12-21頁 | 系統架構與方法細節 |
| ⑥ | 第22-47頁 | 實驗與結果 |
| ⑦ | 第48-49頁 | 結論與未來 |

---

## 第一站（第3頁）

### O-RAN是什麼？

傳統的行動基地台（例如你手機連的5G塔）是**封閉系統**，硬體和軟體都由同一個廠商（如Ericsson、Nokia）捆綁販售，電信業者沒有選擇空間。

**O-RAN（Open Radio Access Network）** 的核心想法就是：**把這個封閉系統打開**，讓不同廠商的硬體和軟體可以互相搭配。

論文第3頁的圖（來源：Ericsson）顯示O-RAN的三個關鍵元素：

> **出處：論文第3頁，Key Elements of Open RAN**

- **開放管理與協調（Open management A1/O1/R1/O2）**：讓SMO（管理大腦）可以透過標準化介面控制底下所有東西
- **5G軟體層**：跑在一般商用硬體上，不需要專屬設備
- **Cloud RAN**：基地台功能跑在雲端伺服器，圖中標紅框的就是這個概念

---

## 第二站：為什麼需要O-RAN？（第4頁）

論文第4頁列出了O-RAN的**承諾（好處）**和**挑戰（問題）**：

> **出處：論文第4頁，Motivation**

### 承諾（好處）：
| 好處 | 意思 |
|------|------|
| **開放性（Openness）** | 不同廠商的零件可以互相搭配使用 |
| **彈性（Flexibility）** | 電信業者可以自由選擇最好的硬體和軟體組合 |
| **創新（Innovation）** | 加快新功能開發速度 |

### 挑戰（問題）：
| 挑戰 | 意思 |
|------|------|
| **整合複雜度** | 多廠商零件要能一起正常運作很難 |
| **測試規模** | 每次更新都要重新測試，工作量很大 |
| **缺乏標準化工具** | 不同雲端平台、不同廠商的監測工具都不一樣，沒有統一標準 |

---

**這就是這篇論文存在的理由：挑戰太多，所以學長提出了一個自動化測試框架來解決。**

---


# 步驟②③④：挑戰、背景技術、解法總覽

---

## 挑戰的細節（第5頁）

> **出處：論文第5頁，Challenges of O-RAN**

第4頁只是列出挑戰的名字，第5頁用一張架構圖讓你看清楚**挑戰從哪裡來**。

圖裡有個核心角色叫 **SMO（Service Management & Orchestration）**，你可以把它想成**整個O-RAN的大腦/指揮中心**。

圖中顯示SMO要同時管理兩朵雲（Cloud A 和 Cloud B），每朵雲裡都有：
- **Hardware Provisioning**：分配實體機器資源
- **VNF Deployment**：把軟體部署上去（VNF = Virtual Network Function，虛擬化的網路功能，例如基地台軟體）

### 三個挑戰長這樣：

| 挑戰 | 在圖裡的位置 | 白話意思 |
|------|------------|---------|
| **Challenge 1：整合複雜度** | SMO要同時連兩朵雲 | 每朵雲的API、操作方式都不一樣，SMO要各自適配，很麻煩 |
| **Challenge 2：測試規模** | Cloud B的VNF那一側 | 每次更新軟體，都要重新跑一遍所有測試，人工做根本跑不完 |
| **Challenge 3：缺乏標準工具** | Metrics Gathering Cloud A/B | 每朵雲蒐集效能數據的方式都不一樣，沒有統一介面 |

---

### 一個關鍵名詞先記住：VNF

**VNF = Virtual Network Function**

傳統上，基地台的每個功能（例如訊號處理、封包轉送）都跑在**專屬硬體**上。O-RAN把這些功能「虛擬化」，讓它們可以跑在**一般的雲端伺服器**上，這些虛擬化的功能就叫VNF。

這篇論文測試的VNF主要是**OAI gNB**（一個開源的5G基地台軟體）。

---

## 背景技術（第6-9頁）

這裡論文介紹了四個背景知識，我帶你逐一看。

---

### 背景1：O-Cloud是什麼？（第6頁）

> **出處：論文第6頁，Related Work: O-Cloud**

O-Cloud就是**專門跑O-RAN網路功能的雲端平台**。

你可以把它想成：普通雲（AWS、Azure）是給一般應用程式用的，O-Cloud是**專門為電信網路功能優化的雲**。

第6頁的圖裡，O-Cloud有兩個核心服務：

```
O-Cloud
  ├── IMS（Infrastructure Management Service）
  │     → 管理硬體資源：哪些機器、哪些節點可用
  └── DMS（Deployment Management Service）
        → 負責把VNF軟體部署到機器上
```

SMO透過一個叫做 **O2介面** 的標準化通道跟O-Cloud溝通：
- **O2ims**：SMO ↔ IMS（問：有哪些機器可用？）
- **O2dms**：SMO ↔ DMS（說：幫我把這個軟體部署上去）

**這個O2介面就是這篇論文的核心！** 學長實作的FOCOM和NFO，就是在SMO這側實作O2介面。

---

### 背景2：SMO的內部結構（第7頁）

> **出處：論文第7頁，Related Work: O-RAN SMO**

SMO內部有很多模組，這篇論文只需要關心兩個：

| 模組 | 全名 | 功能 |
|------|------|------|
| **FOCOM** | Federated O-Cloud Orchestration and Management | 管理多個O-Cloud，知道每朵雲有什麼資源 |
| **NFO** | Network Function Orchestrator | 決定VNF要部署在哪裡，然後下令部署 |

**簡單比喻：**
- FOCOM = 房仲（知道哪裡有空房間）
- NFO = 搬家公司調度員（決定把東西搬去哪間房，然後叫人去搬）

這兩個模組在OSC（O-RAN Software Community，官方開源社群）**沒有實作**，所以這篇論文自己做了。

---

### 背景3：Open Source Projects（第8頁）

> **出處：論文第8頁，Related Works: Open Source Projects**

論文介紹了三個開源專案，這些是實驗會用到的工具：

| 專案 | 角色 | 白話說明 |
|------|------|---------|
| **StarlingX** | O-Cloud平台 | 一個完整的O-Cloud實作，有IMS和DMS，是Wind River（Intel旗下）開源的 |
| **OKD** | O-Cloud平台 | Red Hat OpenShift的社群版，也可以當O-Cloud用 |
| **Nephio** | GitOps自動化框架 | 用Git來管理網路設定的自動化工具 |

**這篇論文的實驗同時跑在StarlingX和OKD兩個平台上**，目的是驗證學長做的框架能跨平台運作。

---

### 背景4：跟其他論文的比較（第9頁）

> **出處：論文第9頁，Related Works: Paper**

這頁是學長跟其他研究的比較表，讓你看**這篇論文的貢獻在哪**：

| 論文 | 用什麼管理 | 有沒有O-Cloud | 有沒有實體O-RU |
|------|-----------|--------------|--------------|
| Wang et al. | FOCOM+Ansible | 模擬的（K3s） | 沒有 |
| Bonati et al. | OpenShift GitOps | OpenShift | USRP（軟體無線電） |
| Liu et al. | FlexRIC | 裸機Linux | USRP |
| **這篇論文** | **FOCOM/NFO** | **StarlingX + OKD（兩個！）** | **LiteOn + Pegatron（真實硬體）** |

這篇論文的特別之處：
1. **同時支援兩個不同的O-Cloud平台**（別人只用一個）
2. **接了真實的O-RU硬體**（別人大多用模擬器）
3. **自己實作了FOCOM和NFO**（OSC沒有，別人也沒做）

---

## 第四站：這篇論文的解法（第10頁）

> **出處：論文第10頁，Motivation（Solutions）**

學長把前面三個挑戰，對應提出三個解法：

```
挑戰1：異質O-Cloud平台（StarlingX & OpenShift介面不同）
  └→ 解法1：透過O2介面實作FOCOM和NFO，讓SMO不需要知道底下是哪種雲

挑戰2：人工測試規模不夠
  └→ 解法2：CI/CD自動化測試框架（Jenkins + rApp自動跑72個測試場景）

挑戰3：各平台效能數據蒐集方式不同
  └→ 解法3：Sideload Profiler（統一的效能監測架構）
```

---

## 三個解法的白話說明

**解法1（FOCOM + NFO）：** 
就像是設計一個「通用插座轉接頭」，不管底下是StarlingX還是OKD，上面的SMO都用同一套O2介面溝通。

**解法2（CI/CD + rApp）：** 
每次程式碼更新，Jenkins自動觸發→建立映像檔→rApp自動部署VNF→測試→收數據→清理。全程不需要人手動操作。

**解法3（Sideload Profiler）：** 
在每個VNF旁邊掛一個「監測小程式」（Sideload Pod），統一蒐集CPU、記憶體、功耗等數據，不管在哪個平台都用同一套介面回報。

---

## 小結：目前的全貌

```
問題：O-RAN有三個挑戰（整合複雜、測試費力、監測不統一）

學長的系統：
  SMO層
    ├── FOCOM（學長自己做的）→ 解決「不同雲介面不統一」
    ├── NFO（學長自己做的）  → 解決「部署VNF需要人工」
    └── rApp + Jenkins       → 解決「測試需要人工」
  
  O-Cloud層（StarlingX / OKD）
    └── Sideload Pod         → 解決「監測數據不統一」
```

---

# 步驟⑤⑥：系統架構、方法細節與實驗結果

---

## 步驟⑤：系統架構與方法細節（第12-21頁）

---

### 整體系統架構（第12頁）

> **出處：論文第12頁，System Architecture**

這頁是整篇論文最重要的一張圖，先把它讀懂：

```
人（工程師）
    ↓
CI/CD Tools（Jenkins）
    ↓
┌─────────────────────────────┐
│ SMO                         │
│   ┌──────────┐  ┌─────────┐ │
│   │ O2       │  │Non-RT   │ │
│   │ ┌──────┐ │  │RIC      │ │
│   │ │FOCOM │ │  │ ┌─────┐ │ │
│   │ │NFO   │ │  │ │rApp │ │ │
│   │ └──────┘ │  │ └─────┘ │ │
│   └──────────┘  └─────────┘ │
└─────────────────────────────┘
    ↓（O2介面）
┌─────────────────────────────┐
│ O-Cloud（StarlingX 或 OKD） │
│   Near RT RIC               │
│   gNB / RFSim / UESim       │
│   CU / DU                   │
└─────────────────────────────┘
    ↓
General Purpose H/W（一般伺服器）
    ↓
實體O-RU（LiteOn / Pegatron）＋實體UE（Samsung手機）
```

圖裡標出三個解法對應的位置：
- **Solution 1**（左側）：FOCOM+NFO → 跨平台互通
- **Solution 2**（右側）：CI/CD → 自動測試規模化
- **Solution 3**（右下）：Sideload Profiling → 統一效能監測

虛線框的部分（RAN Emulator、RU/UE Emulator）標示為**Future Work**，這次實驗沒做。

---

### 模組一：FOCOM（第13-15頁）

> **出處：論文第13-15頁，FOCOM Module**

#### FOCOM做什麼？

FOCOM站在SMO這側，透過O2ims介面去問O-Cloud：
- 你有什麼資源（機器、節點）？
- 有哪些Deployment Manager可以用？

**白話比喻：** FOCOM就像是一個「資產管理員」，負責建立並維護一份清單，記錄每朵雲有什麼資源。

#### FOCOM支援兩種O-Cloud：

| O-Cloud平台 | 用的IMS | 認證方式 |
|------------|--------|---------|
| StarlingX | PTI-O2 | OAuth2 + mTLS（雙向憑證） |
| OKD（OpenShift） | oran-o2ims | Bearer Token（類似通行證） |

兩種平台認證方式不同，FOCOM都要能處理，這就是「平台無關」的實作難點。

#### FOCOM實作了哪些API？（第15頁）

> **出處：論文第15頁，FOCOM Module: Current Implementation Status**

表格裡有兩欄很重要：
- **OSC smo-o2**：OSC官方有沒有做 → 全部都是 **X（沒有）**
- **This Work**：這篇論文有沒有做 → 全部都是 **V（有）**

這說明學長做的東西是OSC沒有的，是真正的貢獻。

實作的API包含：
```
/resourceTypes          → 查詢O-Cloud支援哪些資源類型
/resourcePools          → 查詢資源池（有哪些機器群組）
/resourcePools/{id}/resources → 查詢具體有哪些機器
/deploymentManagers     → 查詢有哪些DMS可以部署VNF
```

**技術實作細節：**
- 用**Flask**寫REST API（Python的網頁框架）
- 用**SQLite**存資料（輕量級資料庫）
- 支援**Docker**部署

---

### 模組二：NFO（第16-17頁）

> **出處：論文第16-17頁，NFO Module**

#### NFO做什麼？

NFO負責VNF的**整個生命週期**：

```
Step 1（PREP）：
  NFO → DMS：我要部署這個VNF套件，給我一個ID
  DMS → NFO：好，ID是xxx（201 Ready）

Step 2（ACTION）：
  NFO → DMS：用這些參數，把VNF部署起來
  DMS → NFO：收到，處理中（202 Accepted）

Step 3（MONITORING）：
  DMS → NFO：狀態是 STARTING...
  DMS → NFO：狀態是 PROCESSING...

Step 4（COMPLETION）：
  DMS → NFO：完成了（COMPLETED）或失敗了（FAILED）
```

**白話比喻：** NFO像是外送平台，你下單（NFO請求部署）→ 餐廳接單（DMS確認）→ 製作中（STARTING/PROCESSING）→ 送達（COMPLETED）。

#### NFO實作了哪些API？（第17頁）

> **出處：論文第17頁，NFO Module: Current Implementation Status**

同樣OSC全部沒做（X），學長做了核心的幾個：

| API | 功能 | 有沒有做 |
|-----|------|---------|
| POST /vnf_instances | 建立VNF實例 | ✅ |
| GET /vnf_instances/{id} | 查詢VNF狀態 | ✅ |
| POST /vnf_instances/{id}/instantiate | 部署VNF | ✅ |
| POST /vnf_instances/{id}/terminate | 移除VNF | ✅ |
| /heal、/scale等 | 進階功能 | ❌（未來工作） |

**用Helm部署：** VNF實際上是用**Helm Chart**（Kubernetes的套件管理工具）部署的，NFO會去Git倉庫拉取Helm Chart然後部署。

---

### 模組三：CI/CD + rApp（第18-19頁）

> **出處：論文第18-19頁，Proposed Method: CICD+rApp for automatic testing**

這是解法2的核心，把手動測試變成全自動。

#### 整個流程分兩段：

**左半段（Jenkins負責）：**
```
1. 從Git拉取VNF原始碼
2. 驗證容器倉庫憑證
3. 建立Base Image（基礎映像檔）
4. 建立Source Code Image（包含新程式碼）
5. 建立Final Image（最終版本）
6. 推送到容器倉庫
7. 通知rApp：新版本來了，開始測試
```

**右半段（rApp負責）：**
```
1. 收到測試請求（/tests/run/<test_id>）
2. 讀取測試案例YAML檔，產生所有測試排列組合
3. 透過NFO部署VNF，等待30秒穩定
4. 切換UE飛航模式，確認手機成功連上基地台
5. 跑iperf（網速測試工具），同時蒐集Sideload數據
6. 透過NFO移除VNF
7. 匯出測試結果
```

**關鍵設計：測試排列組合（Permutation）**

第31頁的YAML範例說明，一個測試案例可以定義多個變數：
```yaml
parameters:
  wr_isolcpus: [8, 14]      # CPU核心數：2種
  iperf_bandwidth_mbps: [100, 400, 700]  # 流量大小：3種
```
2×3 = 6種組合，rApp會自動把每種組合都跑過一遍，不需要人工手動換設定。

---

### 模組四：Sideload Profiling（第20-21頁）

> **出處：論文第20-21頁，Proposed Method: Sideload Profiling**

#### 問題是什麼？

不同O-Cloud平台、不同VNF，蒐集效能數據的方式都不一樣（第21頁左半邊）：

```
Before（分散、不透明）：
  DPDK      → dpdk-telemetry（各自的工具）
  Application → Application Log
  Kubernetes → kubectl top, describe
  CRI-O     → crictl stats, inspect
  Pod       → /stats/summary
  OS        → perf
  Hardware  → Hardware（各廠商各自介面）
```

#### 解法是什麼？

在每個VNF Pod旁邊，部署一個**Sideload Pod**（就像隨從一樣掛在旁邊），統一把所有層面的數據蒐集起來，透過單一HTTP API回報：

```
After（統一、透明）：
  DPDK ┐
  App  │
  K8s  ├→ Sideload Profiler → HTTP API → SMO/rApp
  CRI-O│
  Pod  │
  OS   │
  HW   ┘
```

**好處：** rApp不需要知道底下是StarlingX還是OKD，只要打同一個HTTP API就能拿到所有數據。

---

## 步驟⑥：實驗結果（第22-47頁）

---

### 實驗前的假設（第23頁）

> **出處：論文第23頁，Experiment & Validation: Assumptions**

先記住三個前提：
1. **UE（手機）已預先測試過**，假設手機本身沒問題
2. **O-Cloud已預先建置完成**，因為StarlingX還不支援自動化建置，而且伺服器建置是一次性的工作
3. **SMO已預先安裝完成**

---

### 實驗一：Testbed建置（第24-29頁）

> **出處：論文第24-29頁，Experimental 1: Testbed Implementation**

#### 硬體環境（第24-25頁）

實驗室的實體配置：

```
左機櫃：
  ├── OKD RT SNO（Single Node OpenShift，實時核心版）
  └── StarlingX O-Cloud

右機櫃：
  ├── SMO伺服器（跑在VMware vSphere上）
  └── StarlingX RT Node（實時計算節點）

底部：
  └── Ufispace Fronthaul Gateway（前傳網路交換機）
      └── LiteOn O-RU + Pegatron O-RU（兩個廠牌的實體天線）

測試用UE：
  └── Samsung手機（透過ADB連接到筆電控制）
```

兩台伺服器的規格完全相同（第25頁）：
- CPU：Intel Xeon Gold 6326，32核心 @ 2.90GHz
- 隔離核心（跑VNF用）：16個核心
- Hugepages：40GB（網路處理用的大記憶體頁）
- 差別只在OS：OKD用CoreOS 9，StarlingX用Debian 6.6-rt

#### 多廠商互通測試（第26頁）

> **出處：論文第26頁，Integration with Different O-RU**

驗證兩個O-Cloud平台都能跟兩個廠牌的O-RU溝通：

```
OKD O-Cloud    ─┐
                 ├→ Ufispace Gateway → LiteOn O-RU
StarlingX O-Cloud ─┘                → Pegatron O-RU
```

4種組合全部測通，這是**多廠商互通性**的基礎驗證。

#### 三種VNF部署模式（第27-29頁）

> **出處：論文第27-29頁，OAI GNB 7.2 / nFAPI / F1**

學長測試了三種OAI基地台的部署架構：

| 模式 | 架構 | 白話說明 |
|------|------|---------|
| **GNB 7.2（Monolithic）** | 一個Pod包含全部功能 | 最簡單，一個容器搞定 |
| **nFAPI（PNF+VNF）** | 實體硬體（PNF）+虛擬軟體（VNF）分開 | 部分功能在裸機跑，部分在雲上跑 |
| **F1（CU+DU分離）** | CU和DU是兩個獨立Pod | 最複雜，符合真實電信網路架構 |

第27-29頁的截圖都是成功運行的log，看到「UE d4f4: LCID 4: TX 1285608399 RX 23323 bytes」這類訊息代表手機成功傳輸資料了。

---

### 實驗二：回歸測試（第30-42頁）

> **出處：論文第30頁，Experiment 2: Experimental Settings**

這是整篇論文最核心的實驗，驗證CI/CD自動化測試框架的能力。

#### 測試矩陣

72個測試場景由這些變數排列組合而來：

```
O-Cloud：[StarlingX, OKD]           → 2種
VNF架構：[MONO, F1, NFAPI]          → 3種
O-RU廠牌：[LiteOn, Pegatron]        → 2種
CPU核心數：[8cores, 14cores]         → 2種
流量大小：[100Mbps, 400Mbps, 700Mbps] → 3種

總計：2×3×2×2×3 = 72個場景
```

#### 自動化流水線（第32頁）

> **出處：論文第32頁，Automated Pipeline**

第32頁截圖顯示Jenkins的實際執行畫面，流程依序：
```
Start → Registry Credential Testing → Clone Repository 
→ Build Base Image → Build Build Image → Build gNB Image 
→ Push Final Image → Trigger Bandwidth Tests → End
```

#### CPU熱圖結果（第33-36頁）

> **出處：論文第33-36頁，Experimental 2**

CPU Heatmap讀法：
- **X軸**：時間（分三段：100M、400M、700M流量）
- **Y軸**：CPU核心編號
- **顏色深淺**：使用率（紅色越深=使用率越高）

**StarlingX的結果（第35頁左半）：**
- 紅色條紋清晰、穩定，說明VNF**有規律地使用固定核心**
- 不同流量下核心使用率差異不大，表示**系統穩定**

**OKD的結果（第35頁右半）：**
- 紅色條紋較不規則，特別在700Mbps時有些核心出現閃爍
- 說明OKD在高負載時**CPU排程比較不穩定**

#### 吞吐量結果（第37頁）

> **出處：論文第37頁，NF with different CPU allocation**

這頁是最重要的結論圖，比較StarlingX vs OKD的實際網速：

**Kepler（OKD）：**
- 100Mbps、400Mbps段：大致穩定
- **700Mbps段**：綠色線條（NFAPI、8核心配置）大幅震盪，忽高忽低
- 某些場景甚至掉回接近0，再爬回來

**Joule（StarlingX）：**
- 三個流量段都相對穩定
- 各種配置的線條收斂，沒有大幅震盪

**結論（學長在第37頁寫的）：**
- StarlingX在高負載下吞吐量更穩定、變異數更小
- OKD在700Mbps + 8核心的配置下有明顯的資源競爭或排程問題

#### 回歸測試總結（第40頁）

> **出處：論文第40頁，Regression Test Summary**

這張表是整個實驗二的結論：

```
         OKD穩定性    StarlingX穩定性
LiteOn   全PASS       全PASS
Pegatron F1+8c+700M→FAILED   全PASS
         MONO+8c+700M→FAILED
```

四個重要發現：
1. **LiteOn在兩個平台都全過**
2. **Pegatron在OKD + 8核心 + 700Mbps時失敗**，但在StarlingX全過
3. **升到14核心後，Pegatron在OKD也全過了**
4. **OKD需要比StarlingX更多CPU才能處理同等高負載**

#### 為什麼會失敗？（第41-42頁）

> **出處：論文第41-42頁，GNB Error Log Dump**

Log裡顯示的錯誤：
```
Assertion (found) failed!
In nr_fill_pucch() /oai-ran/.../pucch_rx.c:93
PUCCH list is full
```

**PUCCH**（Physical Uplink Control CHannel）是手機回傳控制訊號的頻道。「PUCCH list is full」代表**基地台軟體的處理佇列滿了**，原因是CPU不夠快、來不及處理所有上行訊號，最終導致程式崩潰。

這直接印證了：**OKD在8核心高負載下，CPU排程效率不如StarlingX**。

---

### 實驗三：VNF效能分析（第43-47頁）

> **出處：論文第43-47頁，Experiment 3: Sideload Profiling**

這個實驗驗證Sideload Profiler能蒐集到有意義的數據。

#### Thread-to-Core Affinity（第43頁）

這張圖顯示**每個執行緒跑在哪個CPU核心上**：

學長在圖上標出兩個發現：
1. **Fronthaul相關執行緒**（fh_main_poll、fh_rx_bbdev等）100%佔用在特定核心上，這是正常的（需要低延遲）
2. **L1 TX和L1 RX的核心被其他程序共用**，這是潛在瓶頸，說明核心隔離設定可以再優化

#### Thread CPU使用率（第44頁）

> **出處：論文第44頁，Experiment 3: Sideload Profiling**

這頁顯示在低流量（1 Mbps）時各執行緒的CPU使用率：

**發現一：**
```
fh_main_poll-13：avg_cpu = 100.1%
fh_rx_bbdev-15：avg_cpu = 100.0%
fh_cp_gen-17：avg_cpu = 100.1%
```
這三個Fronthaul執行緒**永遠跑滿100%**，原因是它們用**Polling（輪詢）**方式工作（一直不停地問「有沒有新資料？」），即使沒有流量也會燒CPU。學長在圖上標注「Thread need to be clean」，這是給OAI開發者的優化建議。

**發現二：**
```
L1_rx_thread：avg_cpu = 11.7%
L1_tx_thread：avg_cpu = 35.7%
```
L1處理執行緒在低流量時使用率適中，是**On-demand（有需要才工作）**的健康模式。

#### 功耗分析（第46頁）

> **出處：論文第46頁，Experiment 3: Sideload Profiling Gather Power**

功耗圖顯示：
- **Socket 0（橘色）+ Socket 1（綠色）**：兩顆CPU的功耗
- **System Total（黑色虛線）**：iDRAC（伺服器管理介面）回報的整機功耗
- 功耗有週期性的尖峰（190W→210W→192W），對應Fronthaul的週期性工作

#### 記憶體分析（第47頁）

> **出處：論文第47頁，Experiment 3: Sideload Profiling Gather Memory**

記憶體使用量在啟動後快速穩定（紅色Used線收斂），沒有明顯的記憶體洩漏，系統記憶體管理健康。

---

## 總結：三個實驗說了什麼？

| 實驗 | 問題 | 結論 |
|------|------|------|
| **實驗一** | 框架能不能實際部署VNF？ | FOCOM+NFO成功在StarlingX和OKD上部署三種架構的OAI基地台，並連接兩個廠牌的實體O-RU |
| **實驗二** | 自動化測試能發現什麼？ | 72個場景全自動跑完；發現OKD在高負載+少核心時不穩定，StarlingX整體更穩定 |
| **實驗三** | Sideload能提供什麼洞察？ | 發現Fronthaul的Polling執行緒是CPU浪費的根源，提供了具體的優化方向 |

---

# 步驟⑦：結論與未來工作 + 可重現性分析

---

## 步驟⑦：結論（第48頁）與未來工作（第49頁）

### 結論（第48頁）

> **出處：論文第48頁，Conclusion**

學長用三個詞總結這篇論文的貢獻：

**1. Unified Orchestration（統一協調）**
- FOCOM和NFO讓你不需要管底下是StarlingX還是OKD
- 用同一套O2介面就能管理所有雲端平台
- 消除了對廠商專屬工具的依賴

**2. Automated Validation（自動化驗證）**
- Jenkins + rApp的閉環控制，讓回歸測試不需要人工介入
- 從程式碼提交→部署→測試→清理，全自動
- 72個場景人工跑需要好幾天，自動化就只需要等機器跑完

**3. Granular Visibility（細粒度可視性）**
- Sideload Profiler把原本「不透明」的容器內部狀態暴露出來
- 發現了Fronthaul Polling執行緒的CPU浪費問題
- 這種洞察在沒有Sideload之前是很難得到的

---

### 未來工作（第49頁）

> **出處：論文第49頁，Future Works**

| 未來方向 | 白話說明 | 難度 |
|---------|---------|------|
| **TM500 & AIRSG Integration** | 加入專業的多UE測試儀器，不再只用一支Samsung手機 | 需要昂貴設備 |
| **AI-Based Auto-Configuration** | 根據測試結果自動調整VNF參數，不需要人工設定 | 研究性強 |
| **Multi-Vendor Testing** | 加入更多O-Cloud平台（Nephio、Tanzu）和更多廠牌O-RU | 需要更多硬體 |
| **Closed-Loop Automation** | 測試失敗時自動調整設定重新部署，不需要人工介入 | 邏輯複雜 |
| **Complete O2 Implementation** | 補齊E2和O1介面支援 | 標準規格工作 |

---

## 有沒有可以再現的結果？

---

### 先理解這個系統的依賴層次

```
層次1：硬體（最難複製）
  └── Intel Xeon Gold 6326 伺服器 × 2台
  └── LiteOn O-RU + Pegatron O-RU（實體天線）
  └── Samsung UE + ADB筆電
  └── Ufispace Fronthaul Gateway

層次2：平台（需要管理員權限）
  └── StarlingX O-Cloud
  └── OKD O-Cloud

層次3：軟體框架（學長自己寫的）
  └── FOCOM模組
  └── NFO模組
  └── rApp + CI/CD Pipeline
  └── Sideload Profiler

層次4：測試設定（相對容易取得）
  └── Helm Charts（VNF部署設定）
  └── YAML測試案例檔
  └── Jenkins Pipeline設定
```

---

### 各實驗的可再現性分析

#### 實驗一：Testbed建置
> **出處：論文第24-29頁**

**需要什麼：** 層次1+2+3全部都要

**現實評估：**
- O-RU硬體是實驗室的，你很可能可以借用
- StarlingX和OKD應該已經建好在實驗室伺服器上
- **最關鍵的是：學長的FOCOM、NFO、rApp程式碼**

**結論：** ⚠️ 需要學長的程式碼 + 實驗室硬體，但環境應該還在

---

#### 實驗二：回歸測試（72個場景）
> **出處：論文第30-42頁**

**需要什麼：** 層次1+2+3+4全部

**現實評估：**
- 這個實驗最依賴整套系統
- 但**測試結果的分析部分**（CPU熱圖、吞吐量圖）你可以用學長留下的數據重新畫圖驗證
- Jenkins Pipeline設定（第32頁截圖）如果能拿到設定檔，理論上可以重跑

**最容易再現的子部分：**
```
單一場景手動測試：
  1. 手動透過NFO部署一個VNF
  2. 手動跑iperf測速
  3. 比對學長的數據
```

**結論：** ⚠️ 完整72個場景需要全套環境，但單一場景可以試試

---

#### 實驗三：Sideload Profiling
> **出處：論文第43-47頁**

**需要什麼：** 層次2+3（不一定需要O-RU）

**現實評估：**
- Sideload Profiler本身是個**獨立的監測工具**
- 理論上可以在任何Kubernetes叢集上部署並蒐集數據
- 如果實驗室的OKD或StarlingX環境還在，這個最有機會再現

**結論：** ✅ 相對最容易再現，只需要K8s環境 + 學長的Sideload程式碼

---

### 我建議你去要的東西清單

按優先順序：

**第一優先（核心程式碼）：**
```
□ FOCOM模組的原始碼
□ NFO模組的原始碼
□ rApp的原始碼
□ Sideload Profiler的原始碼
```

**第二優先（設定檔）：**
```
□ Helm Charts（VNF部署用，第31頁YAML範例）
□ Jenkins Pipeline設定檔（第32頁）
□ 測試案例YAML檔（例如pegatron-joule-cpu-bandwidth）
□ OAI gNB的設定檔（for StarlingX和OKD各一份）
```

**第三優先（實驗數據）：**
```
□ 72個場景的原始測試數據（CSV或JSON）
□ CPU熱圖的原始數據
□ iperf測試log
□ Sideload蒐集的原始metrics數據
```

**第四優先（環境設定）：**
```
□ StarlingX的安裝設定記錄
□ OKD的安裝設定記錄
□ SMO的Docker Compose或K8s設定
□ 網路拓樸設定（IP位址、VLAN設定等）
```

---

### 你的起點建議

考慮到你剛開始做，我建議這樣的順序：

```
Step A：先跑通Sideload（最小依賴）
  → 只需要一個K8s叢集（實驗室應該有）
  → 部署Sideload Pod
  → 確認能蒐集到CPU/記憶體數據

Step B：跑通NFO的單一部署
  → 透過NFO部署一個簡單的VNF
  → 確認COMPLETED狀態
  → 不需要O-RU

Step C：接上O-RU跑單一測試場景
  → 部署OAI gNB + 連接LiteOn或Pegatron
  → 跑一次iperf
  → 比對學長的數據

Step D：跑完整CI/CD Pipeline
  → 設定Jenkins
  → 跑一個小的測試矩陣（例如只跑8個場景）
```

