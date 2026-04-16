# Mã nguồn Mermaid cho các biểu đồ UML - Quản lý Ký túc xá

Dưới đây là mã nguồn Mermaid cho tất cả các biểu đồ đã được vẽ. Bạn có thể sao chép các đoạn mã này và dán vào [Mermaid Live Editor](https://mermaid.live/) để chỉnh sửa hoặc hiển thị trực tiếp trong các trình xem Markdown hỗ trợ Mermaid (như GitHub, Obsidian, VS Code).

---

## 1. Biểu đồ Use Case
```mermaid
graph LR
    subgraph "Hệ thống Quản lý Ký túc xá"
        UC1(Đăng nhập)
        UC2(Đăng xuất)
        UC3(Xem danh sách phòng)
        UC4(Đăng ký phòng)
        UC5(Hủy đăng ký)
        UC6(Xem trạng thái đăng ký)
        UC7(Thanh toán hóa đơn)
        UC8(Xem hóa đơn)
        UC9(Báo hỏng)
        UC10(Quản lý phòng)
        UC11(Duyệt đăng ký)
        UC12(Quản lý sinh viên)
        UC13(Quản lý hóa đơn)
        UC14(Xử lý báo hỏng)
        UC15(Kiểm tra phòng trống)
        UC16(Xem danh sách đăng ký)
        UC17(Xác thực thanh toán)

        UC4 -.->|include| UC3
        UC4 -.->|include| UC15
        UC7 -.->|include| UC8
        UC7 -.->|include| UC17
        UC11 -.->|include| UC16
    end

    SV((Sinh viên))
    QL((Quản lý))

    SV --> UC1
    SV --> UC2
    SV --> UC3
    SV --> UC4
    SV --> UC5
    SV --> UC6
    SV --> UC7
    SV --> UC8
    SV --> UC9

    QL --> UC1
    QL --> UC2
    QL --> UC10
    QL --> UC11
    QL --> UC12
    QL --> UC13
    QL --> UC14
```

---

## 2. Biểu đồ Lớp (Class Diagram)
```mermaid
classDiagram
    class SinhVien {
        +String maSinhVien
        +String hoTen
        +Date ngaySinh
        +String lop
        +String trangThai
        +dangKyPhong() void
        +thanhToan() boolean
        +baoHong() void
    }
    class Phong {
        +String maPhong
        +int sucChua
        +int soLuongHienTai
        +String tinhTrang
        +kiemTraChoTrong() boolean
        +capNhatTinhTrang() void
    }
    class DangKy {
        +String maDangKy
        +Date ngayDangKy
        +String trangThai
        +taoDangKy() void
        +huyDangKy() void
    }
    class HoaDon {
        +String maHoaDon
        +double soTien
        +Date ngayLap
        +String trangThai
        +taoHoaDon() void
        +thanhToan() boolean
    }
    class BaoHong {
        +String maBaoHong
        +String noiDung
        +Date ngayBao
        +String trangThai
        +guiBaoHong() void
        +capNhatTrangThai() void
    }
    class QuanLy {
        +String maQuanLy
        +String hoTen
        +duyetDangKy() void
        +quanLyPhong() void
    }

    SinhVien "1" -- "n" DangKy : Association
    DangKy "n" -- "1" Phong : Association
    DangKy "1" -- "1" HoaDon : Association
    SinhVien "1" -- "n" HoaDon : Association
    SinhVien "1" -- "n" BaoHong : Association
    QuanLy "1" -- "n" DangKy : Duyệt
    Phong "1" -- "n" BaoHong : Có thể bị hỏng
```

---

## 3. Biểu đồ Tuần tự (Sequence Diagram)

### Đăng ký phòng
```mermaid
sequenceDiagram
    actor SinhVien
    participant UI as Giao diện
    participant HeThong
    participant Phong
    participant DangKy
    actor QuanLy

    SinhVien->>UI: chonDangKyPhong()
    UI->>HeThong: guiYeuCauDangKy()
    HeThong->>Phong: kiemTraPhongTrong()
    Phong-->>HeThong: traKetQua()
    HeThong-->>UI: hienThiDanhSachPhong()
    SinhVien->>UI: xacNhanDangKy()
    UI->>HeThong: guiXacNhanDangKy()
    HeThong->>DangKy: taoDangKy()
    HeThong->>QuanLy: guiYeuCauDuyet()
    QuanLy->>HeThong: duyetDangKy()
    HeThong->>DangKy: capNhatTrangThai()
    HeThong-->>UI: thongBaoKetQua()
    UI-->>SinhVien: hienThiKetQua()
```

### Báo hỏng
```mermaid
sequenceDiagram
    actor SinhVien
    participant UI as Giao diện
    participant HeThong
    participant BaoHong
    actor QuanLy

    SinhVien->>UI: guiBaoHong()
    UI->>HeThong: guiThongTinBaoHong()
    HeThong->>BaoHong: taoBaoHong()
    HeThong->>QuanLy: thongBaoSuCo()
    QuanLy->>HeThong: xuLyBaoHong()
    HeThong->>BaoHong: capNhatTrangThai()
    HeThong-->>UI: thongBaoKetQua()
    UI-->>SinhVien: hienThiKetQua()
```

---

## 4. Biểu đồ Hoạt động (Activity Diagram)

### Đăng ký phòng
```mermaid
stateDiagram-v2
    [*] --> Đăng_nhập
    Đăng_nhập --> Chọn_Đăng_ký_phòng
    Chọn_Đăng_ký_phòng --> Xem_danh_sách_phòng
    Xem_danh_sách_phòng --> Chọn_phòng
    Chọn_phòng --> Kiểm_tra_phòng_trống

    state "Decision: Còn chỗ?" as decision
    Kiểm_tra_phòng_trống --> decision
    
    decision --> Xác_nhận_đăng_ký : [Còn chỗ]
    Xác_nhận_đăng_ký --> Gửi_yêu_cầu_đăng_ký
    Gửi_yêu_cầu_đăng_ký --> Lưu
    Lưu --> [*]

    decision --> Thông_báo_Hết_chỗ : [Hết chỗ]
    Thông_báo_Hết_chỗ --> [*]
```

### Thanh toán hóa đơn
```mermaid
stateDiagram-v2
    [*] --> Chọn_Thanh_toán
    Chọn_Thanh_toán --> Hiển_thị_hóa_đơn
    Hiển_thị_hóa_đơn --> Thực_hiện_Thanh_toán
    Thực_hiện_Thanh_toán --> Cập_nhật_trạng_thái
    Cập_nhật_trạng_thái --> Xác_nhận_giao_dịch

    state "Decision: Kết quả?" as decision
    Xác_nhận_giao_dịch --> decision
    
    decision --> Thành_công : [Thành công]
    Thành_công --> Cập_nhật_Đã_thanh_toán
    Cập_nhật_Đã_thanh_toán --> [*]

    decision --> Thất_bại : [Thất bại]
    Thất_bại --> Thông_báo_lỗi
    Thông_báo_lỗi --> [*]
```

---

## 5. Biểu đồ Trạng thái (State Diagram)

### Trạng thái Đăng ký
```mermaid
stateDiagram-v2
    [*] --> Khởi_tạo
    Khởi_tạo --> Chờ_duyệt
    Chờ_duyệt --> Đã_duyệt : Duyệt
    Chờ_duyệt --> Từ_chối : Từ chối
    Đã_duyệt --> [*]
    Từ_chối --> [*]
```

### Trạng thái Hóa đơn và Báo hỏng
```mermaid
stateDiagram-v2
    state "Trạng thái Hóa đơn" as HoaDon {
        [*] --> Chưa_thanh_toán
        Chưa_thanh_toán --> Đã_thanh_toán : Thanh toán thành công
        Đã_thanh_toán --> [*]
    }

    state "Trạng thái Báo hỏng" as BaoHong {
        [*] --> Mới_tạo
        Mới_tạo --> Đang_xử_lý : Quản lý tiếp nhận
        Đang_xử_lý --> Hoàn_thành : Sửa xong
        Hoàn_thành --> [*]
    }
```
