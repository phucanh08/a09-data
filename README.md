# a09-data

Dữ liệu nhận diện khuôn mặt nhân viên: vector embedding và ảnh đại diện.

## Cấu trúc thư mục

```
a09-data/
├── employees.csv     # Danh sách nhân viên + vector embedding
├── avatars/          # Ảnh đại diện (.webp), 1 ảnh / nhân viên
│   ├── ETC001257.webp
│   ├── to-lam.webp
│   └── ...
└── README.md
```

## employees.csv

File CSV, mã hoá UTF-8, dòng đầu là header.

| Cột          | Kiểu   | Mô tả                                                                 |
|--------------|--------|----------------------------------------------------------------------|
| `PersonCode` | string | Mã định danh duy nhất của nhân viên. Cũng là tên file avatar tương ứng.|
| `FullName`   | string | Họ tên đầy đủ (có dấu tiếng Việt).                                     |
| `Vector`     | string | Vector embedding khuôn mặt, float32 512 chiều, mã hoá base64.          |

Ví dụ:

```csv
PersonCode,FullName,Vector
ETC001257,Lê Phúc Anh,U3iJviziwD5...==
to-lam,Tô Lâm,5MgHPi22fz/...=
```

### Vector embedding

- **Số chiều:** 512 (float32, little-endian)
- **Kích thước:** 2048 bytes → ~2732 ký tự sau khi mã hoá base64
- Dùng để so khớp khuôn mặt bằng khoảng cách cosine / euclidean.

Giải mã trong Python:

```python
import base64, numpy as np

vec = np.frombuffer(base64.b64decode(row["Vector"]), dtype=np.float32)
assert vec.shape == (512,)
```

## avatars/

- Định dạng `.webp`, mỗi nhân viên một ảnh.
- Tên file trùng với `PersonCode`: `avatars/{PersonCode}.webp`.
- Ánh xạ 1:1 với các dòng trong `employees.csv`.
