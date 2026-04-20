# Kế Hoạch Triển Khai Lab 13 - Observability

Dựa trên yêu cầu của Lab 13, dưới đây là danh sách các công việc cần thực hiện và phân công cho nhóm 3 người, chia theo 3 Sprint.

## Sprint 1: Nền tảng Logging & Sanitize Data (PII)
**Mục tiêu:** Xây dựng hệ thống log có cấu trúc (structured JSON logging), có ID truy vết (Correlation ID) và tự động che giấu dữ liệu nhạy cảm (PII).

* **Hào (Backend/Middleware):** 
  * Xử lý phần **Correlation IDs**: Sửa file `app/middleware.py` để mỗi request đều sinh ra và đính kèm một `x-request-id` duy nhất.
  * Xử lý phần **Enrich Logs**: Cập nhật file `app/main.py` để đính kèm các thông tin ngữ cảnh (user, session, feature) vào mỗi log.
* **Tú (Security/Logging):** 
  * Xử lý phần **Sanitize Data (PII Scrubbing)**: Viết logic lọc dữ liệu cá nhân tại `app/pii.py` và tích hợp nó vào cấu hình log tại `app/logging_config.py`.
* **Hậu (QA & Testing):** 
  * Setup môi trường chạy app.
  * Chạy test bằng lệnh `python scripts/load_test.py`.
  * Dùng tool kiểm tra `python scripts/validate_logs.py` để đảm bảo code của Hào và Tú đạt chuẩn (Pass schema).

---

## Sprint 2: Langfuse Tracing & Thu thập Metrics
**Mục tiêu:** Cài đặt Langfuse tracing cho các component của AI agent và bắt đầu thu thập số liệu (metrics) để chuẩn bị cho dashboard.

* **Hào (Tracing):** 
  * Hoàn thiện cấu hình tracing trong `app/tracing.py`.
  * Gắn decorator `@observe` (của Langfuse) vào các pipeline cốt lõi trong `app/agent.py` và các mock tool (`app/mock_llm.py`, `app/mock_rag.py`) để đảm bảo hệ thống ghi nhận đủ các bước.
* **Tú (Metrics):** 
  * Xây dựng và hoàn thiện logic gom nhóm số liệu (in-memory metrics) trong `app/metrics.py`.
  * Chuẩn bị dữ liệu thô xuất ra để sử dụng cho bước vẽ dashboard tiếp theo.
* **Hậu (Incident Response & Tracing QA):** 
  * Giả lập lỗi bằng `python scripts/inject_incident.py --scenario rag_slow`.
  * Kiểm tra trên Langfuse để đảm bảo có **ít nhất 10 traces** hiển thị đầy đủ metadata.
  * Phân tích và viết nháp báo cáo Root Cause (Nguyên nhân gốc rễ) dựa trên log và trace để chuẩn bị cho báo cáo cuối cùng.

---

## Sprint 3: Alerting, Dashboard & Hoàn thiện Báo cáo (Blueprint)
**Mục tiêu:** Cấu hình cảnh báo tự động, hoàn thiện 6-panel dashboard và chuẩn bị báo cáo/demo lấy điểm.

* **Hào (Alerting & SLO):** 
  * Định nghĩa các Service Level Objectives (SLOs) trong file `config/slo.yaml`.
  * Cấu hình các rule cảnh báo (Alert Rules) trong file `config/alert_rules.yaml`. Test thử để đảm bảo các rule hoạt động đúng.
* **Tú (Dashboard):** 
  * Dựa vào dữ liệu từ Sprint 2 và tài liệu `docs/dashboard-spec.md`, thiết kế và cấu hình một **6-panel dashboard** (đầy đủ số liệu, đơn vị, threshold). Cố gắng làm đẹp để lấy điểm Bonus (+3đ).
* **Hậu (Documentation & Demo Lead):** 
  * Hoàn thiện báo cáo kỹ thuật vào `docs/blueprint-template.md`, bao gồm phần báo cáo cá nhân của cả nhóm và link evidence (Git Commits/PRs).
  * Chuẩn bị kịch bản Demo: App chạy mượt, show được Langfuse, show được Dashboard và giải thích cách giải quyết sự cố đã inject.

---

### Tiêu chí hoàn thành (Passing Criteria)
* Script `validate_logs.py` trả về ít nhất 80/100 điểm.
* Langfuse ghi nhận tối thiểu 10 traces live.
* Dashboard có đủ 6 panels.
* Báo cáo `blueprint-template.md` có đầy đủ thông tin của 3 thành viên và các bằng chứng git liên quan.
