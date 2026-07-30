# 研究流程規劃：消化 TEEP David 資料並重現實驗結果
（產出日期：2026-07-31，計畫檔原始位置：C:\Users\徐銘亨\.claude\plans\piped-seeking-fog.md）

## 目前進度總覽
狀態：規劃階段已完成並核准，尚未開始執行任何步驟。
- 已完成：三份素材（internship repo、ocloud-telemetry-agent repo、Liu.pdf）已由 AI 探索並整理重點，關鍵落差已與 Richard 確認（見下方 Context）。
- 尚未開始：步驟 1（環境建置）以下所有步驟都還沒動手，因為需要 Richard 自己的機器做雙系統安裝，AI 目前無法代為執行。
- 下一個該做的動作：步驟 1 — 在自己的 Windows 機器上建 Ubuntu 22.04+ 雙系統，並驗證 RAPL 可讀性（ls /sys/class/powercap/）。這是整個計畫的前置關卡，沒有原生 Linux 就無法進行步驟 2 的功耗量測。

## Context（為什麼要做這個、關鍵發現）
Richard 目前的專題方向與 David（TEEP 實習生，2026 上半年在 BMW Lab 做 RU 功耗與排程演算法關係研究）的研究高度重疊，因此決定先以 David 留下的三份資料建立背景知識，並嘗試重現他的實驗結果，再進一步發展自己的貢獻。三份資料已完整探索，關鍵發現如下：

1. internship/ repo：David 的實驗其實分成兩條完全不同的軌道，必須分開處理：
   - Track A（軟體代理 / Software Proxy）：用一般筆電/伺服器跑 iperf3 + Scaphandre（讀 Intel RAPL）量測 CPU 功耗，完全不需要真實 RU 硬體。腳本齊全（scripts/run_week4_gap_run.sh、analyze_week3_data.py）、SOP 文件完整（docs/FinalReport/01_Methodology_Reproducible_Measurement.md、03_Standard_Operating_Procedure.md）。這是 Richard 現在唯一能自己重現的部分，也是「最短時間重現成果」的答案。
   - Track B（真實 WINLAB O-Cloud + Pegatron RU 測試床）：需要實體進 WINLAB 實驗室、Jenkins/Quay/Helm 帳號權限、PDU/InfluxDB 存取。而且截至 2026-07-30，David 自己都還沒做出一組有效的 baseline vs. 27-PRB 排程器 A/B 對照——這是他留下的未完成、最直接可以接手的任務。

2. ocloud-telemetry-agent repo：Prof. Ray 推薦的參考資料，是量測 O-Cloud DU 能耗的方法論範本（A/B/C/D 階梯式對照實驗、RAPL/perf/Redfish collectors），但沒有附任何實測資料，且綁定特定實驗室硬體（"joule"/"galileo"），無法重現數字結果，只能當方法論與程式碼架構的參考。

3. Liu.pdf：內容其實是網路切片（network slicing）MAC 排程器論文（OAI LTE + FlexRAN + USRP B210 real testbed），完全沒有功耗量測，跟「RU 功耗 vs. 排程演算法」的題目對不上。已與 Richard 確認：當作 OAI MAC/PRB 架構的背景知識即可，不強求另找替代論文。

Richard 已確認的環境現況：目前手邊只有 Windows，尚未申請/取得 WINLAB 實體測試床存取（可能之後會申請，但沒有時間表）。因此計畫以 Track A 為主線、可立即執行，Track B 列為並行的背景研讀 + 未來銜接路徑，不假設存取時程。

## 步驟 1：環境建置（Linux + RAPL 可讀性）— 尚未開始，下一步
Track A 的功耗量測依賴 /sys/class/powercap（Intel RAPL），WSL2 通常無法存取，需要原生 Linux：

- 建議在現有 Windows 機器上做 Ubuntu 22.04+ 雙系統（dual-boot），而不是 VM（大多數 VM 平台不會把 RAPL MSR passthrough 給 guest，雙系統最穩）。
- 建置完成後第一件事：驗證 RAPL 可讀性——ls /sys/class/powercap/ 應看到 intel-rapl:0 等節點；若沒有，代表這台筆電/CPU 不支援或需要 root 權限調整，這會直接影響能不能用這台機器做 Track A。
- 安裝需求（皆列於 docs/FinalReport/03_Standard_Operating_Procedure.md 的 Prereqs）：Scaphandre（可用 docker run --privileged -v /sys/class/powercap:/sys/class/powercap hubblo/scaphandre prometheus，或原生 binary）、iperf3、Python 3.10+ 搭配 pandas/matplotlib/seaborn。

## 步驟 2：重現 Track A —「軟體代理」功耗-流量敏感度掃描（最短路徑的重現目標）— 尚未開始
先完整讀過 docs/FinalReport/01_Methodology_Reproducible_Measurement.md（場景矩陣：Idle → Load-L(30%)/M(60%)/H(100%) × 3 repeats，120s warmup + 300s baseline + 300s/step + 300s cooldown）與 03_Standard_Operating_Procedure.md（含 troubleshooting table），再照 SOP 執行：

1. 啟動 Scaphandre，curl localhost:8080/metrics 驗證有數據。
2. 啟動 iperf3 -s。
3. 執行 scripts/run_week4_gap_run.sh（env vars: TARGET_L/M/H、DURATION、IDLE、OUTPUT_DIR）跑完整 Idle→Load→Idle 序列，產生 markers.csv + power_uw.txt。
4. python scripts/analyze_week3_data.py runs/<my-run> 產生時序圖、線性度箱型圖、統計摘要。
5. QC gate：跟參考圖 assets/2026-01-28/plots/gap_analysis_sensitivity.png 比對——Idle 應 <10W、Load 應 >20W、平台段應平坦，markers.csv 每個事件都要有 Start/Stop。目標數量級對照 FinalReport 的既有結果（Idle 5.7W → 100% load 26.9W；Burst/TDM proxy 21.78W→11.25W，約 48% 下降）。

這一步做完，Richard 就有了一個完全自己重現、有 QC 依據的最小可行結果（MRR-equivalent）。

## 步驟 3（可選加碼）：srsRAN ZMQ 全堆疊代理實驗 — 尚未開始，非必要
若時間允許，docs/StudyNotes/2026-01-29_Guide-srsRAN-ZMQ-Setup.md 描述一個介於 Track A 與 Track B 之間的橋接實驗：用 Docker Compose 跑 srsran/open5gs + srsran/gnb（ZMQ RF driver）+ 自訂 srsUE image，在同一台原生 Linux 機器上跑，仍不需要真實 RU，但流量會真的經過一個 RAN 排程迴圈（比純 iperf3 代理更貼近題目本質）。可作為 Track A 之後、有餘力時的進階練習，不是必要路徑。

## 步驟 4：背景知識研讀（與步驟 2-3 平行進行，自訂步調）— 可隨時開始
建議順序（已確認符合 David 給的學習順序邏輯）：
1. docs/StudyNotes/2026-03-02_Frequencies-in-NR.md — NR 頻域基礎概念，先打底。
2. docs/StudyNotes/2026-03-04_OAI-Scheduler-Source-Code-Verified.md、2026-03-04_Enabling-Time-Frequency-Domain-Scheduling.md、2026-02-26_TBS-Calculator-Results-273-vs-27-Equivalence.md — OAI 排程器原始碼與 PRB 分配的理論基礎，直接對應 Track B 的 27-PRB cap 實驗設計邏輯。
3. docs/StudyNotes/2026-02-03_srsRAN-Scheduler-Deep-Dive.md — 排程器架構層級，對應步驟 3 若有做的話。
4. Liu.pdf — 當背景知識讀（OAI MAC 架構、FlexRAN 控制介面、RBG/PRB Type 0 分配機制），不要預期裡面有功耗數據。
5. docs/StudyNotes/2026-05-01_NVIDIA-cuMAC-GPU-Accelerated-Scheduling.md — 放最後讀，David 自己註記這是 close-source 且與 OAI 架構不相容，優先度較低。
6. ocloud-telemetry-agent 的 docs/architecture.md、tests/test_correctness.py（量測正確性規則，如 RAPL wraparound、stale counter 偵測）——當「怎麼做對功耗量測」的方法論參考，不需要真的跑起來（跑不動，缺硬體）。

## 步驟 5：用 C-verification-report.md 記錄重現過程（非強制，但建議）— 尚未開始
依 CLAUDE.md 既有共識，Open-Research-Playbook/templates/C-verification-report.md 的結構（確認理解 → 重現 MRR → 提出後續工作）剛好對應 Richard 現在在做的事，可以當非正式的個人查核表，把步驟 2-3 的重現結果、QC 是否通過、遇到的落差都記下來——這份文件同時也是將來跟 David / Prof. Ray 討論銜接 Track B 時最好用的溝通素材。

## 步驟 6：Track B（真實 WINLAB 測試床）— 銜接路徑，非現階段執行項
不假設存取時程，僅記錄下次有存取權限時該從哪裡接手：

- 直接切入點：docs/DailyNotes/2026-07-30_Daily-Note.md + docs/StudyNotes/2026-07-30_27-PRB_Live_Validation_Power_Merge_and_Run_QC.md + 2026-07-30_WINLAB_Weekly_Progress_Summary.md — David 留下的「下一步實驗清單」本質上就是待接手任務：做出一組同條件的 baseline vs. 27-PRB-capped 排程器 A/B 功耗對照，目前完全沒人做出來過。
- 已知的環境脆弱點（供未來排雷用）：PNF runtime 未進 git、UE iperf 長時間跑會 idle-timeout 提早中斷、Lavoisier BMC 重開機後需手動跑 setup_network.sh + 重啟 kubelet、共用實驗室常被別人改設定。
- 自動化三劍客：scripts/run_week4_gap_run.sh（Track A 用）、scripts/merge_winlab_e2e_power.py（Track B 流量↔PDU功耗對齊）、docs/Deployment/winlab_e2e_rapp_docker/（可直接部署到 HPE 機器的 rApp Docker bundle）。

## 待與 David / Prof. Ray 討論的開放問題（不要自行假設）
1. Track B 尚未完成的 baseline vs. 排程器 A/B 對照，是否就是 Richard 銜接後的第一個具體任務？
2. WINLAB 實體測試床的申請流程與時程。
3. Liu.pdf 已確認當背景知識即可，不強求找替代論文——但若之後 Prof. Ray 主動提到另一篇「Liu」的功耗論文，需要重新檢視。

## 驗證方式
- 步驟 2 完成的判準：analyze_week3_data.py 產出的時序圖與 FinalReport 既有結果（Idle ~5-6W, Load ~20-27W）數量級相符，且 QC checklist（markers 完整、Idle/Load 有明顯區隔、無感測器 dropout）全部通過。
- 步驟 5 的 verification report 完成後，可視為這個階段的產出物，作為之後跟 David 討論銜接 Track B 的依據。
