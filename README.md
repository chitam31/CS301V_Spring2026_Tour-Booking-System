# TOUR BOOKING SYSTEM

## Tổng quan dự án

Tour Booking System là hệ sinh thái đặt tour trực tuyến gồm hai phần chính:

- `tour-management`: Backend Spring Boot phục vụ API cho quản lý dịch vụ, khung giờ, giỏ hàng, thanh toán, đánh giá và AI chat.
- `tour-frontend`: Frontend React + Vite cung cấp giao diện tìm kiếm dịch vụ, giỏ hàng, checkout, trang quản lý nhà cung cấp, đánh giá và chatbot AI.
- `Database`: Tập hợp các script PostgreSQL để tạo bảng, dữ liệu mẫu và hàm hỗ trợ.

Dự án được phát triển cho môn CS301V, với nhóm thực hiện: Chí Tâm & Quốc Anh.

## Điểm nổi bật

- Hỗ trợ hai vai trò người dùng: `CUSTOMER` và `PROVIDER`.
- Quản lý dịch vụ tour động: tạo, cập nhật, ẩn, khôi phục và clone dịch vụ.
- Combo tour và tour con (sub-services) cho phép nhà cung cấp xây dựng gói dịch vụ.
- Quản lý slot ngày/giờ và kiểm tra tình trạng khả dụng.
- Giỏ hàng, áp dụng voucher, thanh toán giả lập và hủy booking.
- Hệ thống đánh giá/feedback cho từng tour và nhà cung cấp.
- Chatbot AI tích hợp Spring AI với Google Gemini.
- Upload hình ảnh qua Cloudinary.

## Kiến trúc chính

### Backend (`tour-management`)

- Java 21
- Spring Boot 4.0.5
- Spring Web MVC
- Spring Data JPA
- PostgreSQL
- Spring AI (Google Gemini)
- Lombok

### Frontend (`tour-frontend`)

- React 19
- Vite
- Tailwind CSS 4
- Zustand
- Axios
- React Router DOM
- Framer Motion
- Recharts

## Cấu trúc thư mục

```
CS301V_Final Project_TOUR BOOKING SYSTEM_Spring26_Final/
  Database/
    portgre_tour_tables.sql
    portgre_tour_samples.sql
    portgre_tour_utils.sql
  Diagrams/
  tour-frontend/tour-frontend/
    package.json
    src/
  tour-management/tour-management/
    pom.xml
    src/
    src/main/resources/application.properties
  CS301V_REPORT Final Project_Group Chí Tâm & Quốc Anh_Tour Booking System.pdf
  CS301V_SLIDE Final Project Software_Official.pdf
```

## Cài đặt môi trường

### 1. Cài đặt PostgreSQL

1. Cài đặt PostgreSQL.
2. Tạo database `tour_booking_db`.
3. Import các script trong `Database/` theo thứ tự:
   - `portgre_tour_tables.sql`
   - `portgre_tour_utils.sql`
   - `portgre_tour_samples.sql`

### 2. Cấu hình backend

Mở file:

- `tour-management/tour-management/src/main/resources/application.properties`

Cập nhật:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/tour_booking_db
spring.datasource.username=postgres
spring.datasource.password=YOUR_POSTGRES_PASSWORD_HERE
```

Nếu dùng API AI và Cloudinary, thay các thông tin sau bằng giá trị thực tế:

```properties
spring.ai.openai.api-key=YOUR_GOOGLE_GENAI_API_KEY
spring.ai.openai.base-url=https://generativelanguage.googleapis.com/v1beta/openai
spring.ai.openai.chat.model=gemini-2.5-flash-preview-05-20

cloudinary.cloud-name=YOUR_CLOUDINARY_CLOUD_NAME
cloudinary.api-key=YOUR_CLOUDINARY_API_KEY
cloudinary.api-secret=YOUR_CLOUDINARY_API_SECRET
```

> Lưu ý: file hiện tại chứa cấu hình mẫu; không dùng trực tiếp trong môi trường sản xuất.

### 3. Chạy backend

Trong thư mục `tour-management/tour-management/`:

- Windows:
  ```powershell
  .\mvnw.cmd spring-boot:run
  ```
- Linux / macOS:
  ```bash
  ./mvnw spring-boot:run
  ```

Backend sẽ lắng nghe mặc định tại `http://localhost:8080`.

### 4. Chạy frontend

Trong thư mục `tour-frontend/tour-frontend/`:

```bash
npm install
npm run dev
```

Frontend mặc định chạy trên `http://localhost:5173`.

## API chính

### Authentication

- `POST /api/auth/register/customer` - Đăng ký khách hàng.
- `POST /api/auth/register/provider` - Đăng ký nhà cung cấp.
- `POST /api/auth/login` - Đăng nhập.

### Services

- `GET /api/services/all` - Lấy danh sách dịch vụ đang hoạt động.
- `GET /api/services/search?q=...` - Tìm kiếm dịch vụ.
- `GET /api/services/{id}` - Lấy chi tiết dịch vụ.
- `POST /api/services/create` - Tạo dịch vụ mới.
- `PUT /api/services/{id}?providerId=...` - Cập nhật dịch vụ.
- `DELETE /api/services/{id}?providerId=...` - Xóa mềm dịch vụ.
- `PATCH /api/services/{id}/restore?providerId=...` - Khôi phục dịch vụ.
- `POST /api/services/{parentId}/add-member/{serviceId}?providerId=...` - Thêm dịch vụ con vào combo.
- `GET /api/services/provider/{providerId}` - Lấy dịch vụ của nhà cung cấp.
- `GET /api/services/{id}/sub-services` - Lấy sub-services của một combo.
- `GET /api/services/provider/{providerId}/dashboard` - Dữ liệu dashboard cho provider.

### Booking & Cart

- `GET /api/bookings/slots?serviceId=...&date=YYYY-MM-DD` - Kiểm tra khung giờ trống.
- `POST /api/bookings/add-item` - Thêm mục vào giỏ hàng.
- `DELETE /api/bookings/items/{itemId}` - Xóa mục khỏi giỏ hàng.
- `GET /api/bookings/cart/{customerId}` - Lấy giỏ hàng hiện tại.
- `POST /api/bookings/{bookingId}/voucher?voucherCode=...` - Áp dụng mã giảm giá.
- `POST /api/bookings/{bookingId}/pay?method=...` - Thanh toán.
- `POST /api/bookings/{bookingId}/cancel` - Hủy đơn hàng.
- `GET /api/bookings/customer/{customerId}` - Lịch sử đặt tour khách hàng.
- `GET /api/bookings/provider/{providerId}/items` - Lấy đơn của provider.
- `POST /api/bookings/items/{itemId}/confirm?providerId=...` - Provider xác nhận.
- `POST /api/bookings/items/{itemId}/reject?providerId=...` - Provider từ chối.

### Feedback

- `POST /api/feedbacks/submit?itemId=...&rating=...` - Gửi review.
- `GET /api/feedbacks/average/{serviceId}` - Lấy điểm trung bình.
- `GET /api/feedbacks/service/{serviceId}` - Lấy review theo dịch vụ.
- `GET /api/feedbacks/provider/{providerId}` - Lấy review theo provider.

### AI Chatbot

- `GET /api/ai/chat?message=...&userId=...&role=...` - Trò chuyện với AI.

## Luồng chức năng chính

### Khách hàng (CUSTOMER)

1. Đăng ký / đăng nhập.
2. Tìm kiếm dịch vụ, xem chi tiết và đánh giá.
3. Chọn ngày giờ, thêm tour vào giỏ hàng.
4. Áp dụng voucher, thanh toán và xem lịch sử đơn hàng.
5. Đánh giá dịch vụ sau khi sử dụng.

### Nhà cung cấp (PROVIDER)

1. Đăng ký / đăng nhập.
2. Tạo và quản lý dịch vụ, bao gồm combo và dịch vụ con.
3. Quản lý slot trống cho từng dịch vụ.
4. Xác nhận hoặc từ chối đơn hàng của khách.
5. Xem dashboard thống kê và review từ khách hàng.

### Chatbot AI

- Tích hợp `Spring AI` với `Google Gemini` để trả lời câu hỏi liên quan đặt tour.
- Hỗ trợ lưu và làm mới bộ nhớ hội thoại khi xảy ra lỗi.

## Ghi chú quan trọng

- Package backend hiện đang dùng `com.tourbooking.tour_management` do `com.tourbooking.tour-management` không hợp lệ trong Java.
- Môi trường `application.properties` hiện dùng `ddl-auto=update`; chỉ dùng khi phát triển, kiểm tra kỹ trước khi đưa vào production.
- CORS đã được cấu hình cho `http://localhost:5173`.

## Chạy build production

### Backend

```bash
cd tour-management/tour-management
./mvnw clean package
```

### Frontend

```bash
cd tour-frontend/tour-frontend
npm run build
```

Sau khi build, frontend sẽ nằm trong `dist/` và backend tạo file JAR trong `target/`.

## Liên hệ

- Nhóm: Chí Tâm & Quốc Anh
- Dự án: Tour Booking System cho học phần CS301V
