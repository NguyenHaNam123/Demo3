# PharmaCare AI — Hệ Thống Quản Lý Nhà Thuốc Tích Hợp AI
*(Dự án Chuyển Đổi & Kế Thừa Hạ Tầng Đạt Chuẩn GPP & Đạo Đức Y Tế)*

Hệ thống quản lý nhà thuốc chuẩn mực được xây dựng trên nền tảng **FastAPI**, **SQLAlchemy ORM** và giao diện hiện đại **Dark Glassmorphism**, tích hợp **Trí tuệ Nhân tạo (Google Gemini / OpenAI)** hỗ trợ Dược sĩ tra cứu thuốc, sinh báo cáo xử lý hàng cận hạn và hướng dẫn quy trình vận hành nội bộ (SOP).

---

## 1. Các Tính Năng Nổi Bật

### 🔐 Phân Quyền Hệ Thống RBAC 3 Vai Trò
- **Quản lý (Manager):** Toàn quyền hệ thống, xem báo cáo doanh thu, quản lý thuốc, quản lý lô kho, xem lịch sử hóa đơn.
- **Dược sĩ (Pharmacist):** Quản lý thuốc, nhập lô mới, kiểm tra hạn dùng (thẻ màu Đỏ/Vàng/Xanh), sử dụng trợ lý AI tóm tắt thuốc, sinh báo cáo đề xuất xử lý cận hạn và hỏi đáp quy trình SOP.
- **Thu ngân (Cashier):** Màn hình bán hàng POS chuyên biệt, tra cứu thuốc, kiểm tra tồn kho khả dụng, lập hóa đơn bán lẻ.

### 🛒 Nghiệp Vụ Bán Hàng POS & Thuật Toán FEFO
- **Nguyên tắc FEFO (First Expired, First Out):** Hệ thống tự động phân tích và ưu tiên xuất bán từ các lô có hạn sử dụng gần nhất trước.
- **Tự động chia tách nhiều lô (Multi-batch split):** Khi số lượng mua lớn hơn số lượng tồn của 1 lô, hệ thống tự động trừ tuần tự qua các lô tiếp theo.
- **Khóa chặn an toàn 100%:** Tuyệt đối không cho phép bán thuốc từ các lô đã quá hạn sử dụng (`expiry_date < today`).

### 📦 Quản Lý Lô Hàng & Cảnh Báo Kho Trực Quan
- Bảng danh sách lô gắn nhãn màu trực quan:
  - 🔴 **Đỏ:** Lô đã hết hạn sử dụng (cần niêm phong tiêu hủy).
  - 🟡 **Vàng:** Lô cận hạn sử dụng (< 60 ngày).
  - 🟢 **Xanh:** Lô an toàn (hạn dùng dài > 60 ngày).
  - ⚪ **Xám:** Lô đã hết hàng trong kho.
- Cảnh báo các mặt hàng có tồn kho khả dụng dưới ngưỡng tối thiểu (`min_stock_alert`).

### 🤖 Dịch Vụ AI Đạt Chuẩn Đạo Đức Y Tế
1. **AI Tóm tắt thông tin thuốc nội bộ:** Trích xuất nhanh 3 mục (Chỉ định, Liều dùng & Đường dùng, Lưu ý & Chống chỉ định). Luôn đính kèm cảnh báo: *"Cần hỏi ý kiến dược sĩ chuyên môn trước khi hướng dẫn khách hàng"*.
2. **AI Báo cáo cận hạn & Đề xuất xử lý:** Tự động tổng hợp danh sách lô rủi ro và đề xuất 3 nhóm giải pháp chuẩn GPP (tiêu hủy, đổi trả nhà cung cấp, hoặc ưu tiên xuất bán FEFO).
3. **AI Chatbot Quy trình Nội bộ (SOP):** Hướng dẫn nhân viên thực hiện chính xác quy trình nhập kho, kiểm kê, tiêu hủy và bán hàng. Tích hợp **Guardrail y tế an toàn**, tự động từ chối các câu hỏi chẩn đoán bệnh hay kê đơn điều trị ngoài luồng.
4. **Cơ chế Chịu lỗi & Fallback thông minh:** Tự động ngắt timeout 15s, chuyển sang bộ sinh dự phòng Rule-based Fallback khi chưa cấu hình API Key hoặc sự cố mạng, bảo đảm quầy thuốc không bao giờ bị gián đoạn.

---

## 2. Tài Khoản Mẫu Đăng Nhập (Demo Accounts)

| Vai trò | Tên đăng nhập | Mật khẩu | Họ và tên | Phạm vi quyền |
|---|---|---|---|---|
| **Quản lý (Manager)** | `manager` | `manager123` | Nguyễn Văn Quản | Toàn quyền hệ thống & Báo cáo doanh thu |
| **Dược sĩ (Pharmacist)** | `pharmacist` | `pharm123` | Trần Thị Lan | Quản lý thuốc, Lô hạn dùng, Trợ lý AI |
| **Thu ngân (Cashier)** | `cashier` | `cashier123` | Lê Văn Hùng | Màn hình POS bán lẻ, Lịch sử hóa đơn |

---

## 3. Cấu Trúc Thư Mục Dự Án

```text
├── database.py                 # SQLAlchemy ORM: 8 models chuẩn GPP & kết nối SQLite
├── schemas.py                  # Pydantic schemas cho API request/response
├── main.py                     # Entry point FastAPI, CORS & Static files mount
├── seed_data.py                # Script khởi tạo CSDL & nạp dữ liệu mẫu y tế
├── requirements.txt            # Danh sách thư viện phụ thuộc
├── .env.example                # File mẫu cấu hình biến môi trường
├── static/                     # Giao diện Frontend phục vụ trực tiếp qua FastAPI
│   ├── index.html              # Giao diện SPA (Login, POS, Lô kho, Thuốc, AI, Dashboard)
│   ├── css/style.css           # Giao diện Dark Glassmorphism, POS grid, thẻ màu hạn dùng
│   └── js/app.js               # Controller xử lý RBAC, giỏ hàng FEFO, API AI & Chatbot
├── services/                   # Module logic AI Service độc lập
│   ├── ai_service.py           # Kết nối LLM (Gemini/OpenAI), Timeout, Error handling, Fallback
│   └── prompt_templates.py     # Quản lý tập trung System Prompts & Guardrails an toàn
├── routers/                    # Các Router API Backend
│   ├── auth.py                 # Xác thực Session token & Phân quyền RBAC
│   ├── categories.py           # CRUD Danh mục nhóm thuốc
│   ├── medicines.py            # CRUD Thuốc, Tra cứu nâng cao, Tính tồn khả dụng
│   ├── batches.py              # Nhập lô, Cập nhật lô & API Cảnh báo kho
│   ├── pos.py                  # API Bán hàng POS xuất kho theo FEFO
│   ├── reports.py              # Thống kê doanh thu & Tổng quan kho
│   ├── suppliers.py            # CRUD Nhà cung cấp, tìm kiếm & ràng buộc kho
│   └── ai.py                   # API Tóm tắt thuốc, Báo cáo hạn dùng, Chatbot SOP
├── tests/                      # Bộ kiểm thử tự động (Pytest)
│   ├── test_pos_fefo.py        # Test luồng bán hàng FEFO, chặn hàng quá hạn, các ca biên
│   ├── test_suppliers.py       # Test CRUD nhà cung cấp, phân quyền RBAC & ràng buộc lô hàng
│   └── test_ai_service.py      # Test AI Service: khuyết dữ liệu, guardrails, fallback
└── docs/
    └── ai_evidence/            # Tài liệu minh chứng chu trình phát triển AI (SDLC)
        ├── prompt_templates.md # Danh mục các prompts chuẩn mực tách biệt khỏi code
        ├── prompt_iterations.md# Nhật ký 3 vòng thử nghiệm & tinh chỉnh prompt
        └── ai_ethics_report.md # Báo cáo kiểm soát đạo đức, bảo mật API key và an toàn y tế
```

---

## 4. Hướng Dẫn Cài Đặt & Chạy Ứng Dụng

### Bước 1: Chuẩn Bị Môi Trường Python
Đảm bảo máy tính đã cài đặt Python 3.10 hoặc 3.11+.

```bash
# Tạo môi trường ảo (Virtual Environment)
python -m venv venv

# Kích hoạt môi trường ảo:
# Trên Windows:
.\venv\Scripts\activate
# Trên Linux/macOS:
source venv/bin/activate

# Cài đặt các gói thư viện phụ thuộc:
pip install -r requirements.txt
```

### Bước 2: Cấu Hình Biến Môi Trường (.env)
Sao chép file `.env.example` thành `.env`:
```bash
# Trên Windows PowerShell:
Copy-Item .env.example .env

# Trên Linux/macOS:
cp .env.example .env
```
Mở file `.env` và điền `GEMINI_API_KEY` (hoặc `OPENAI_API_KEY`) của bạn nếu muốn sử dụng mô hình AI trực tuyến.
*(Lưu ý: Nếu chưa có API Key, hệ thống vẫn hoạt động mượt mà nhờ cơ chế **Rule-based Fallback Generator** an toàn).*

### Bước 3: Khởi Tạo CSDL & Nạp Dữ Liệu Mẫu (Seed Data)
Chạy script để tự động tạo 8 bảng CSDL và nạp dữ liệu y tế mẫu (3 tài khoản RBAC, 6 nhóm thuốc, 4 nhà cung cấp, 12 thuốc thực tế, 17 lô hàng đủ trạng thái An toàn/Cận date/Quá hạn và 4 quy trình SOP):

```bash
python seed_data.py
```

### Bước 4: Khởi Chạy Hệ Thống (Backend & Frontend)
Hệ thống sử dụng cơ chế tích hợp, Frontend được phục vụ trực tiếp qua FastAPI:

```bash
python -m uvicorn main:app --reload --port 8000
```

Hoặc triển khai nhanh bằng **Docker Compose**:
```bash
docker-compose up --build -d
```

Sau khi chạy thành công, truy cập:
- 🌐 **Giao diện Ứng dụng:** [http://localhost:8000](http://localhost:8000)
- 📖 **Tài liệu API Swagger UI:** [http://localhost:8000/docs](http://localhost:8000/docs)
- 📑 **Tài liệu API ReDoc:** [http://localhost:8000/redoc](http://localhost:8000/redoc)

---

## 5. Chạy Kiểm Thử Tự Động (Automated Testing)

Dự án bao gồm bộ Unit & Integration tests hoàn chỉnh kiểm tra toàn bộ luồng nghiệp vụ FEFO và các dịch vụ AI:

```bash
# Chạy toàn bộ test suite bằng pytest
python -m pytest tests/ -v
```

Kết quả mong đợi: **19/19 tests PASSED 100%**:
- `test_fefo_deducts_nearest_expiry_batch_first`: Trừ đúng lô có hạn dùng gần nhất.
- `test_fefo_multi_batch_split_deduction`: Cắt trừ tự động qua nhiều lô khi mua số lượng lớn.
- `test_block_expired_batch_direct_selection`: Chặn đứng hành vi xuất bán lô đã quá hạn.
- `test_block_when_all_batches_are_expired`: Chặn bán khi toàn bộ thuốc trong kho đã hết hạn.
- `test_fefo_exact_quantity_depletion`: Chuyển trạng thái `exhausted` khi lô về 0.
- `test_insufficient_stock_rejection`: Từ chối khi mua vượt quá tồn kho khả dụng.
- `test_invalid_quantity_validation_error`: Chặn số lượng $\le 0$ bằng validation.
- `test_get_suppliers_list`: Tra cứu danh sách nhà cung cấp và đếm số lô liên kết.
- `test_create_supplier_success`: Dược sĩ/Quản lý thêm mới nhà cung cấp thành công.
- `test_create_supplier_duplicate_name_error`: Chặn tạo trùng tên nhà cung cấp.
- `test_update_supplier_success`: Cập nhật thông tin nhà cung cấp an toàn.
- `test_delete_supplier_with_batches_blocked`: Chặn xóa nhà cung cấp đang có lô hàng trong kho.
- `test_delete_supplier_without_batches_success`: Xóa thành công nhà cung cấp khi không còn lô liên kết.
- `test_cashier_cannot_create_or_delete_supplier`: Phân quyền RBAC chặn Thu ngân tạo/xóa nhà cung cấp.
- `test_ai_medicine_summary_with_missing_data`: Xử lý mượt mà dữ liệu thuốc bị thiếu.
- `test_procedure_chatbot_in_scope_success`: Trả lời chính xác câu hỏi quy trình SOP.
- `test_procedure_chatbot_out_of_scope_rejection`: Kích hoạt Guardrail từ chối câu hỏi kê đơn y khoa.
- `test_ai_service_fallback_on_api_error`: Kích hoạt Fallback khi API timeout hoặc mất mạng.
- `test_ai_service_fallback_on_empty_response`: Xử lý an toàn khi API trả về rỗng.

---

## 6. Tài Liệu Minh Chứng SDLC & AI Evidence
Chi tiết các minh chứng chu trình phát triển phần mềm được lưu trữ trong thư mục `docs/`:
- [Tài liệu Phân tích & Thiết kế Hệ thống KT1](docs/KT1_Phan_tich_thiet_ke.md): Báo cáo đặc tả 10 tiêu chí chuẩn GPP, sơ đồ Use Case, ERD 8 bảng và Kiến trúc 4 tầng.
- [Prompt Templates](docs/ai_evidence/prompt_templates.md): Đặc tả cấu trúc prompt và guardrails y tế.
- [Prompt Iterations Log](docs/ai_evidence/prompt_iterations.md): Nhật ký 3 vòng thử nghiệm và tinh chỉnh prompt.
- [AI Ethics & Safety Report](docs/ai_evidence/ai_ethics_report.md): Báo cáo an toàn thông tin, bảo mật API key và đạo đức y tế.
#   D e m o 3  
 #   D e m o 3  
 