# Bài 4: Lưu trữ dữ liệu đơn giản với Docker Volume

## 1. Mục tiêu
Tạo một Named Volume trong Docker để lưu trữ và duy trì tính toàn vẹn, bền vững (persistence) của dữ liệu giữa các lần chạy của các Container khác nhau ngay cả khi Container bị xoá.

---

## 2. Các bước thực hiện

### Bước 1: Tạo Named Volume
Tạo một volume mới có tên là `easy-volume`:
```bash
docker volume create easy-volume
```

*(Tùy chọn) Kiểm tra danh sách volume trên hệ thống để xác nhận volume đã được tạo thành công:*
```bash
docker volume ls
```

---

### Bước 2: Ghi dữ liệu vào Volume bằng Container thứ nhất
Khởi chạy một container từ image `alpine`, gắn kết (mount) volume `easy-volume` vào đường dẫn `/data` trong container, ghi dòng chữ `Luu tru du lieu Docker` vào tệp `/data/test.txt`. Sau khi thực hiện xong lệnh, container sẽ tự động bị hủy nhờ cờ `--rm`:

```bash
docker run --rm -v easy-volume:/data alpine sh -c "echo 'Luu tru du lieu Docker' > /data/test.txt"
```

---

### Bước 3: Đọc lại dữ liệu bằng Container thứ hai (Kiểm tra)
Khởi chạy một container `alpine` hoàn toàn mới và khác, gắn kết cùng volume `easy-volume` vào thư mục `/data` để đọc lại nội dung tệp `/data/test.txt`:

```bash
docker run --rm -v easy-volume:/data alpine cat /data/test.txt
```

---

## 3. Kết quả mong đợi

Khi chạy lệnh ở Bước 3, terminal sẽ xuất ra đúng chuỗi văn bản:
```text
Luu tru du lieu Docker
```

---

## 4. Giải thích cơ chế hoạt động của Docker Volume

1. **Named Volume (`easy-volume`):**
   - Vùng lưu trữ được Docker quản lý độc lập với vòng đời của các Container (trên Linux thường nằm tại `/var/lib/docker/volumes/`).
2. **Tham số `-v easy-volume:/data`:**
   - Mount named volume `easy-volume` vào thư mục đích `/data` bên trong container.
3. **Cờ `--rm`:**
   - Tự động xóa container sau khi tiến trình chạy xong.
   - Mặc dù container thứ nhất đã bị hủy hoàn toàn, dữ liệu trong tệp `/data/test.txt` vẫn được giữ nguyên vẹn trên `easy-volume`. Container thứ hai khởi chạy gắn cùng volume vẫn có thể truy cập và đọc dữ liệu này bình thường.

---

## 5. Dọn dẹp môi trường (Optional)
Sau khi hoàn thành bài tập, xóa volume để giải phóng dung lượng:
```bash
docker volume rm easy-volume
```
