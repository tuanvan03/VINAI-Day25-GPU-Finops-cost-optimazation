# GPU FinOps Lab — Báo Cáo Kết Quả

**Sinh viên:** Đoàn Văn Tuấn — **MSSV:** 2A202600046

---

## Tổng quan

Bài lab mô phỏng quy trình **GPU FinOps** gồm:
- **Phần 1-7**: Tương tác với mock cluster (6 Docker services) qua tunnel cloudflared
- **Phần 8**: Train ResNet-18 thật trên Kaggle GPU, so sánh FP32 vs AMP
- **Phần 8.5**: Phân tích nâng cao — multi-GPU, forecasting, optimization

---

## Chi tiết từng Cell

### Cell 1 — Setup
- **Mô tả**: Cài đặt thư viện (requests, pandas, matplotlib, plotly)
- **Kết quả**: Môi trường Python sẵn sàng

### Cell 2 — Gateway URL
- **Mô tả**: Cấu hình URL tunnel kết nối tới Docker cluster local
- **Kết quả**: ✅ Kết nối thành công tới Gateway

### Cell 2.5 — Student Information
- **Mô tả**: Hiển thị header gradient xanh-tím chứa tên + MSSV
- **Kết quả**: Header hiển thị ở đầu mọi screenshot

### Cell 3 — View Cluster Nodes
- **Mô tả**: Lấy danh sách GPU nodes (4 nodes, 8 GPUs: T4, A100, V100, T4)
- **Kết quả**: Hiển thị utilization, memory, power, temperature từng GPU

### Cell 4 — Cluster Metrics Summary
- **Mô tả**: Metrics tổng hợp toàn cluster
- **Kết quả**: Tổng GPUs, busy/idle, avg utilization, power draw

### Cell 5 — Submit Workloads
- **Mô tả**: Submit 4 workloads (ResNet, BERT, Inference, LLM) với GPU preference khác nhau
- **Kết quả**: Workloads được assigned vào GPUs phù hợp

### Cell 6 — Record Billing
- **Mô tả**: Ghi nhận billing events (2 on-demand, 2 spot)
- **Kết quả**: Summary: total cost + savings + budget utilization

### Cell 7 — Spot Pricing
- **Mô tả**: Xem bảng giá spot instances (có biến động)
- **Kết quả**: Discount 55-80% so với on-demand

### Cell 8 — Request Spot Instances
- **Mô tả**: Gửi 3 spot requests với bid price khác nhau
- **Kết quả**: 2 granted, 1 rejected (bid quá thấp)

### Cell 9 — Simulate Preemption
- **Mô tả**: Mô phỏng cloud thu hồi spot instances
- **Kết quả**: Preempted instances + savings report (savings %)

### Cell 10 — Autoscaler Policy
- **Mô tả**: Xem và cập nhật policy (thresholds, cooldown, min/max nodes)
- **Kết quả**: Policy updated thành công

### Cell 11 — Autoscaler Evaluation
- **Mô tả**: Chạy 5 cycles evaluation, quan sát scale up/down decisions
- **Kết quả**: Các decision: scale_up, scale_down, no_action theo utilization

### Cell 12 — Cost Snapshots
- **Mô tả**: Chụp 5 snapshots chi phí theo thời gian
- **Kết quả**: Total cost, idle cost, waste % từng snapshot

### Cell 13 — Waste Report
- **Mô tả**: Phân tích waste từ idle GPUs
- **Kết quả**: Avg waste %, potential monthly savings, severity (HIGH/MEDIUM/LOW)

### Cell 14 — Optimization Recommendations
- **Mô tả**: Generate recommendations tự động (right-size, scale down, use spot, scheduling)
- **Kết quả**: 4 recommendations với priority + estimated savings

### Cell 15 — Dashboard View
- **Mô tả**: Dashboard tổng hợp từ Cost Tracker
- **Kết quả**: Cluster metrics, billing summary, spot savings, waste analysis

### Cell 16 — Cost Breakdown Visualization
- **Mô tả**: 3 biểu đồ: Cost by GPU type, Spot vs On-Demand, Budget Utilization
- **Kết quả**: File `finops_cost_breakdown.png`

### Cell 17 — Time-series Cost Tracking
- **Mô tả**: 10 snapshots liên tiếp, stackplot active vs idle cost + waste %
- **Kết quả**: File `finops_timeseries.png`

### Cell 18 — Full FinOps Workflow
- **Mô tả**: End-to-end workflow: check → submit → autoscale → snapshot → recommend → optimize → billing
- **Kết quả**: Hoàn thành 7 steps, total spend + savings

---

## Part 8: Real GPU Training (Kaggle)

### Cell 19 — GPU Detection
- **Mô tả**: Cài torch + pynvml, detect GPU thật
- **Kết quả**: Tên GPU, memory, type, pricing, CUDA version

### Cell 20 — GPU Metrics Collection
- **Mô tả**: Hàm `get_gpu_metrics()` dùng pynvml + torch.cuda fallback + diagnostic test
- **Kết quả**: GPU util, memory, power, temperature đo được

### Cell 21 — Prepare Dataset & Model
- **Mô tả**: Tải CIFAR-10, tạo ResNet-18, định nghĩa `train_epoch_monitored()`
- **Kết quả**: Dataset sẵn sàng (50k train / 10k test)

### Cell 22 — Train FP32
- **Mô tả**: Train 3 epochs FP32 baseline với inline GPU monitoring
- **Kết quả**: Loss/accuracy từng epoch, total time, peak memory, avg util, power, temp, cost

### Cell 23 — Train AMP
- **Mô tả**: Train 3 epochs Mixed Precision (AMP)
- **Kết quả**: Metrics tương tự, thời gian nhanh hơn, memory thấp hơn

### Cell 24 — FP32 vs AMP Comparison
- **Mô tả**: So sánh speedup, cost saving, memory saving + extrapolation (1 day, 1 week, 1 month)
- **Kết quả**: File `real_gpu_comparison.png` + bảng comparison chi tiết

### Cell 25 — Report Real GPU Costs
- **Mô tả**: Gửi FP32 cost (on-demand) và AMP cost (spot) lên Gateway
- **Kết quả**: Billing summary tích hợp real GPU data vào FinOps dashboard

### Cell 26 — Real GPU Visualization
- **Mô tả**: 4 biểu đồ telemetry (utilization, memory, power, temperature) + cost per epoch
- **Kết quả**: File `real_gpu_telemetry.png` + `cost_per_epoch.png`

---

## Part 8.5: Advanced GPU Cost Optimization

### Cell 27 — Multi-GPU Cost Analysis
- **Mô tả**: Phân tích scaling efficiency (Amdahl's Law) cho 1, 2, 4, 8 GPUs
- **Kết quả**: Bảng so sánh + biểu đồ + xác định optimal GPU count
- **Charts**: `multi_gpu_scaling.png`

### Cell 28 — Project Cost Forecasting
- **Mô tả**: Dự báo chi phí đa phase với confidence intervals 95%
- **Kết quả**: Phase breakdown, best/expected/worst case, contingency buffer
- **Charts**: `project_forecast.png`

### Cell 29 — Optimization Opportunity Analysis
- **Mô tả**: Phân tích 5 optimization strategies theo priority score (savings / effort / risk)
- **Kết quả**: Ranked list + cumulative savings + priority matrix
- **Charts**: `optimization_roadmap.png`

### Cell 30 — Integrated Cost Dashboard
- **Mô tả**: Dashboard 6 panels tổng hợp tất cả phân tích Part 8.5
- **Kết quả**: Multi-GPU cost curve, scaling efficiency, forecast, pie chart, priority matrix, savings roadmap
- **Charts**: `advanced_finops_dashboard.png`

### Cell 31 — Challenge Exercise
- **Mô tả**: Thiết kế strategy optimization cho LLM Fine-tuning (8×A100, $5000 budget)
  - Step 1: Baseline cost = $5,872 ❌ Over budget
  - Step 2: Multi-GPU → tìm optimal config
  - Step 3-4: Chọn strategies (AMP, Spot, Early Stopping...)
  - Step 5-6: Forecast + verify constraints
- **Kết quả**: ✅ ALL CONSTRAINTS SATISFIED! (dưới budget, đúng deadline)
- **Charts**: `challenge_strategy.png`

---

## Danh sách Charts đã tạo

| File | Mô tả |
|------|-------|
| `finops_cost_breakdown.png` | Cost by GPU type, Spot vs On-Demand, Budget (Cell 16) |
| `finops_timeseries.png` | Time-series cost allocation + waste % (Cell 17) |
| `real_gpu_comparison.png` | FP32 vs AMP: time, cost, utilization (Cell 24) |
| `real_gpu_telemetry.png` | GPU Utilization, Memory, Power, Temperature (Cell 26) |
| `cost_per_epoch.png` | Cost per epoch FP32 vs AMP (Cell 26) |
| `multi_gpu_scaling.png` | Multi-GPU scaling efficiency (Cell 27) |
| `project_forecast.png` | Project cost forecast with CI (Cell 28) |
| `optimization_roadmap.png` | Optimization priority + roadmap (Cell 29) |
| `advanced_finops_dashboard.png` | 6-panel integrated dashboard (Cell 30) |
| `challenge_strategy.png` | Cost reduction journey + strategy (Cell 31) |
