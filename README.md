# AI Digital Integrated Circuits Project

TSMC 28nm 기반에서 AI accelerator 설계를 대상으로  
Synthesis → STA → Post-Simulation → PNR까지  
full ASIC backend flow를 수행하며 PPA trade-off를 분석한 프로젝트.



## Key Contributions
- Hierarchical design (Multiplier → MAC → Systolic Array)
- Frequency sweep 기반 PPA 분석 및 최적 operating point 도출
- STA + post-synthesis simulation을 통한 timing 검증
- ICC2 기반 PNR 최적화 (placement, power grid, density tuning)



## Project Breakdown
### Assignment 1 — RCA 기반 8-bit Multiplier 합성
#### What I Did
- Design Compiler를 이용하여 RCA 기반 8-bit multiplier 합성
- clock period를 변경하며 frequency sweep 수행
- timing, power, area report를 기반으로 PPA 분석
#### Problem
- 단순히 최대 주파수를 선택하면 power/area가 급격히 증가
- 설계 목표가 없는 상태에서 optimal point 정의가 필요
#### Approach
- slack ≥ 0인 valid region만 고려
- frequency–power–area 관계를 그래프로 분석
- 기하평균 기반 PPA score 도입 (performance bias 포함)
#### Result
- timing을 만족하는 최대 주파수 범위 도출
- PPA trade-off 상 **knee point (≈2.0~2.4GHz)** 구간 확인
- 성능 vs 자원 간 균형이 필요한 설계 특성 확인

---

### Assignment 2 — MAC (Multiply-Accumulate) 모듈 합성

#### What I Did
- MAC(Multiply-Accumulate) 구조를 hierarchical synthesis로 구성
- PrimeTime을 이용한 STA 수행
- frequency별 timing, power, area 분석
#### Problem
- slack 기반 판단은 실제 PPA trade-off를 반영하지 못함
- 성능/전력/면적 간 균형 기준이 필요
#### Approach
- frequency, power, area를 정규화
- 동일 가중치 기반 PPA score 정의
- 변화율 분석 (Δarea, Δpower) 통해 민감도 평가
#### Result
- 단순 timing 기준이 아닌 **정량적 PPA metric 기반 최적 주파수 도출**
- performance-centric vs balanced optimization 차이 확인

---

### Assignment 3 — Systolic Array 합성 및 Post-Synthesis Simulation

#### What I Did
- SDF back-annotation 기반 timing simulation 수행
- STA 결과와 실제 simulation 결과 비교
#### Problem
- STA에서 timing met이어도 실제 동작이 보장되지 않음
- setup/hold violation이 실제 functional failure로 이어질 수 있음
#### Approach
- synthesized netlist + SDF delay를 반영한 simulation 수행
- console 결과뿐 아니라 waveform까지 분석
- “correct” 출력이지만 glitch 발생하는 케이스 제거
#### Result
- STA + simulation을 모두 만족하는 **실제 max frequency 도출**
- timing violation → functional failure 연결 관계 검증
- timing-aware validation 필요성 확인

---

### Assignment 4 — Physical Design (Place & Route)

#### What I Did
- ICC2 기반 place-and-route 수행
- floorplan, power network, placement density 직접 조정
#### Problem
- 초기 1.4GHz netlist에서 setup/hold violation 동시 발생
- buffer insertion 없이 hold violation 해결 필요
#### Approach
**1. Frequency Selection**
- setup violation 회피를 위해 0.9GHz로 downscale

**2. Floorplanning**
- core size를 synthesis area 기반으로 계산
- square 구조로 routing imbalance 최소화

**3. Power Network Optimization**
- power strap width: 0.5 → 0.4
- spacing: 30 → 40
- IR drop 증가 → short path delay 증가 → hold 완화

**4. Placement Density Tuning**
- PREROUTE_PLACEMENT_MAX_DENSITY 조정
- routing congestion 완화 및 RC delay 증가

**5. IO Distribution**
- single-side → multi-side 배치로 routing 균형 개선
#### Result
- hold slack: **-0.0001 → +0.0003**
- buffer insertion 없이 timing closure 달성
- 총 16회 iteration을 통한 PPA 최적화
#### Key Insight
- hold violation은 “너무 빠른 경로” 문제
- physical parameter 조정만으로 timing 해결 가능
- power grid / placement / routing이 timing에 직접 영향



## Key Results
- Timing closure achieved at **0.9GHz**
- Hold slack: **-0.0001 → +0.0003 개선**
- 16 iterations of PNR optimization



## Key Insights
- Timing 문제는 logical이 아니라 **physical issue일 수 있음**
- IR drop / routing / density가 timing에 직접 영향
- STA와 실제 동작 간 차이를 반드시 검증해야 함



## 📋Tech Stack
- Synopsys Design Compiler
- Synopsys PrimeTime
- Synopsys ICC2
- Verilog
- TSMC 28nm
