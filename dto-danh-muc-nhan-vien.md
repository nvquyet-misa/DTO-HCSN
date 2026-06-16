# Member DTO — Chuẩn dữ liệu Cán bộ nhân viên & Người dùng

> **Mục đích:** Định nghĩa chuẩn dữ liệu thống nhất để các subapp (SalaGov, Mimosa, QLTS, ...) 
> gửi dữ liệu thành viên về AMIS Platform.  
> Các subapp **tự map** trường nội bộ sang DTO này trước khi gửi.  
> Platform sẽ nhận đúng format này và không thực hiện thêm bước mapping.

---

## 1. Cấu trúc DTO

```json
{
  "EmployeeCode":         "string",
  "FullName":             "string",
  "FirstName":            "string",
  "LastName":             "string",
  "Gender":               "int | null",
  "Birthday":             "datetime | null",
  "Address":              "string | null",
  "Email":                "string | null",
  "Mobile":               "string | null",

  "OrganizationUnitID":   "guid | null",
  "OrganizationUnitName": "string | null",

  "JobPositionID":        "guid | null",
  "JobPositionName":      "string | null",

  "JobTitleID":           "guid | null",
  "JobTitleName":         "string | null",

  "StaffTypeID":          "guid | null",
  "StaffTypeName":        "string | null",

  "HireDate":             "datetime | null",
  "TerminationDate":      "datetime | null",
  "EmployeeStatus":       "int | null",

  "ReportToID":           "guid | null",
  "ReportToName":         "string | null",

  "IsStaff":              "boolean",
  "IsUser":               "boolean",

  "MISAIDEmail":          "string | null",
  "MISAIDMobile":         "string | null",
  "MISAID":               "guid | null",

  "Avatar":            "string | null",

  "SourceApp":            "string"
  "RoleType": "int"
}
```

---

## 2. Mô tả chi tiết từng trường

### 2.1 Thông tin định danh

| Trường | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `EmployeeCode` | string, max 42 | Có (nếu IsStaff=true) | Mã cán bộ nhân viên. Phải là duy nhất trong cùng tenant. Không chứa ký tự đặc biệt ngoài `.`, `-`, `_`. |

### 2.2 Thông tin cá nhân

| Trường | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `FullName` | string, max 100 | **Có** | Họ và tên đầy đủ. Ví dụ: `"Nguyễn Văn An"` |
| `FirstName` | string | Không | Họ và tên đệm. Ví dụ: `"Nguyễn Văn"` |
| `LastName` | string | Không | Tên. Ví dụ: `"An"` |
| `Gender` | int | Không | `0` = Nữ, `1` = Nam, `null` = Không xác định |
| `Birthday` | datetime (ISO 8601) | Không | Ngày sinh. Ví dụ: `"1990-05-15T00:00:00"` |
| `Address` | string | Không | Địa chỉ thường trú |
| `Email` | string | Không | Email cá nhân (không phải email tài khoản). Ví dụ: `"an.nv@gmail.com"` |
| `Mobile` | string | Không | Số điện thoại di động cá nhân. Ví dụ: `"0912345678"` |

### 2.3 Thông tin tổ chức

| Trường | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `OrganizationUnitID` | guid | Không | ID phòng ban trên Platform (nếu đã biết). **Ưu tiên dùng trường này.** |
| `OrganizationUnitName` | string | Không | Tên phòng ban. Lưu denormalize để hiển thị nhanh. |

> **Lưu ý:** Nếu subapp chỉ có `OrganizationUnitCode` (như QHNS/BudgetCode của iGOV), điền vào `OrganizationUnitCode`. Platform sẽ tự resolve sang `OrganizationUnitID`.

### 2.4 Chức danh & Chức vụ

| Trường | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `JobPositionID` | guid | Không | ID chức danh |
| `JobPositionName` | string | Không | Tên chức danh. Ví dụ: `"Bác sĩ"`, `"Kế toán viên"` |
| `JobTitleID` | guid | Không | ID chức vụ |
| `JobTitleName` | string | Không | Tên chức vụ. Ví dụ: `"Trưởng phòng"`, `"Chuyên viên"` |

> **Lưu ý HCSN:** Trong hệ thống HCSN (iGOV/iHOS):
> - `JobPosition` = **Chức danh** (ví dụ: Bác sĩ, Y tá, Kế toán trưởng)  
> - `JobTitle` = **Chức vụ** (ví dụ: Trưởng phòng, Phó giám đốc)

### 2.5 Loại CBNV & Trạng thái

| Trường | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `StaffTypeID` | guid | Không | ID loại CBNV trên Platform (biên chế, hợp đồng, thử việc...). |
| `StaffTypeName` | string | Không | Tên loại CBNV. Lưu denormalize. |
| `HireDate` | datetime (ISO 8601) | Không | Ngày vào đơn vị. Ví dụ: `"2020-01-15T00:00:00"` |
| `TerminationDate` | datetime (ISO 8601) | Không | Ngày nghỉ việc / kết thúc hợp đồng. `null` nếu đang làm việc. |
| `EmployeeStatus` | int | Không | `0` = Đang làm việc, `1` = Đã nghỉ việc, `2` = Tạm nghỉ. Mặc định: `0`. |

### 2.6 Quản lý trực tiếp

| Trường | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `ReportToID` | guid | Không | UserID của người quản lý trực tiếp trên Platform. |
| `ReportToName` | string | Không | Tên người quản lý. Lưu denormalize. |

### 2.7 Flags — Phân loại CBNV / NSD

| Trường | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `IsStaff` | boolean | **Có** | `true` = là cán bộ nhân viên (có thông tin nhân sự). |
| `IsUser` | boolean | **Có** | `true` = có tài khoản đăng nhập MISA ID. |

> Một người vừa là CBNV vừa là NSD → `IsStaff: true, IsUser: true`.  
> Chỉ là NSD (không có hồ sơ nhân sự) → `IsStaff: false, IsUser: true`.

### 2.8 Tài khoản MISA ID (chỉ khi IsUser = true)

| Trường | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `MISAIDEmail` | string | Có (nếu IsUser) | Email tài khoản MISA ID. Dùng để đăng nhập. Phải đúng định dạng email. |
| `MISAIDMobile` | string | Không | Số điện thoại tài khoản MISA ID. Định dạng: bắt đầu `0`, 10–11 chữ số. |
| `MISAID` | guid | Không | GUID tài khoản MISA ID |

### 2.9 Avatar

| Trường | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `Avatar` | string | Không | URL công khai trỏ đến ảnh đại diện. Platform sẽ tải ảnh về và lưu vào MISA Storage. |

> **Lưu ý về Avatar:**
>
> - Subapp **không thể nhúng file ảnh** (binary/base64) trực tiếp vào DTO này.
> - Nếu có ảnh: cung cấp `Avatar` — phải là URL công khai có thể tải về (không cần auth). Platform sẽ tải và lưu vào MISA Storage sau khi Getting Started hoàn thành.

### 2.10 Metadata

| Trường | Kiểu | Bắt buộc | Mô tả |
|---|---|---|---|
| `SourceApp` | string | **Có** | Mã ứng dụng nguồn gửi dữ liệu. Xem bảng giá trị hợp lệ bên dưới. |

**Giá trị hợp lệ cho `SourceApp`:**

| Giá trị | Ứng dụng |
|---|---|
| `SALAGOV` | MISA SalaGov |
| `MIMOSA` | MISA Mimosa |
| `QLTS` | MISA Quản lý Tài sản |


**RoleType**: 1: Quản trị ứng dụng, 2: user thường
