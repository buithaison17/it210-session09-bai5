## 1. Sơ đồ luồng hoạt động (Data Flow)

Client (Browser)
   ↓  (Request kèm param: ?lang=vi hoặc ?lang=en)
Spring DispatcherServlet
   ↓
LocaleChangeInterceptor  (đánh chặn request, đọc param "lang")
   ↓
LocaleResolver (SessionLocaleResolver hoặc CookieLocaleResolver)
   ↓
Controller (xử lý logic nghiệp vụ)
   ↓
View (Thymeleaf/JSP sử dụng MessageSource để render đa ngôn ngữ)
   ↓
Response trả về Client (hiển thị đúng ngôn ngữ)

---

## 2. Công cụ "đánh chặn" URL để đổi ngôn ngữ

Spring sử dụng:
- LocaleChangeInterceptor

Cơ chế:
- Đây là một Interceptor trong Spring MVC
- Nó "đứng giữa" DispatcherServlet và Controller
- Khi request có param (mặc định là "lang"), nó sẽ:
  + Đọc giá trị param (ví dụ: vi, en)
  + Gọi LocaleResolver để set lại Locale tương ứng
- Nhờ đó không cần viết code xử lý đổi ngôn ngữ trong Controller

---

## 3. Lựa chọn LocaleResolver

### Option 1: SessionLocaleResolver
- Lưu locale trong HttpSession
- Mất khi:
  + Tắt trình duyệt
  + Session hết hạn

### Option 2: CookieLocaleResolver
- Lưu locale vào Cookie phía client
- Vẫn giữ được khi:
  + Tắt/mở lại trình duyệt
  + Session hết hạn

---

## 4. Quyết định lựa chọn

Chọn: CookieLocaleResolver

---

## 5. Lý do (dựa trên yêu cầu số 2 của Sếp)

Giả định yêu cầu số 2:
- Người dùng không phải chọn lại ngôn ngữ mỗi lần truy cập hệ thống

Phân tích:
- SessionLocaleResolver:
  + Không đáp ứng vì session bị mất khi đóng trình duyệt
- CookieLocaleResolver:
  + Đáp ứng vì lưu lâu dài trên client
  + Trải nghiệm người dùng tốt hơn (ghi nhớ lựa chọn)

Kết luận:
- CookieLocaleResolver phù hợp hơn vì đảm bảo tính "ghi nhớ lâu dài" và cải thiện UX
