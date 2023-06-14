# 📋 Server-Side API

{% hint style="info" %}
Trang này trình bày các API sử dụng cho component. Truy cập vào [đây](https://api.d.etop.vn/doc/ext/partner) để tra cứu chi tiết toàn bộ API hoặc tải file [swagger.json](https://api.d.etop.vn/doc/ext/partner/swagger.json).
{% endhint %}

Để sử dụng component, server của đối tác chỉ cần sử dụng một API là [AuthorizeShop](https://api-eship-dev.sobanhang.com/doc/component/server-api.html#ket-noi) để kết nối. Toàn bộ quá trình khởi tạo, đăng nhập và xác thực được thực hiện bởi Component.

## Cấu hình <a href="#cau-hinh" id="cau-hinh"></a>

Sử dụng `api_key` được cung cấp và cấu hình như sau:

```sh
export API_KEY=<api_key>
export API_HOST=https://api.d.etop.vn
```

## Kết nối tài khoản shop <a href="#ket-noi-tai-khoan-shop" id="ket-noi-tai-khoan-shop"></a>

[**POST /v1/partner.Shop/AuthorizeShop**](https://api.d.etop.vn/doc/ext/partner#operation/partner.Shop-AuthorizeShop)

Đối tác có thể kết nối với shop theo hai bước như sau:

### Kết nối lần đầu <a href="#ket-noi-lan-dau" id="ket-noi-lan-dau"></a>

Trường hợp shop của đối tác đã từng kết nối với eTop và đối tác đã có thông tin shop\_id thì bỏ qua bước này, di chuyển đến [bước sau](https://api-eship-dev.sobanhang.com/doc/component/server-api.html#ket-noi-lan-thu-hai-tro-di). Trong trường hợp shop của đối tác chưa từng kết nối với eTop, đối tác có thể gửi request như sau:

```sh
# Mặc định
curl https://api.d.etop.vn/v1/partner.Shop/AuthorizeShop \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $API_KEY" \
    -d '{}'

# Hoặc cung cấp thông tin sẵn có (các mục là không bắt buộc)
curl https://api.d.etop.vn/v1/partner.Shop/AuthorizeShop \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $API_KEY" \
    -d '{
        "email": "<địa chỉ email>",
        "phone": "<số điện thoại>",
        "name": "<tên>"
    }'
```

### Kết nối lần thứ hai trở đi <a href="#ket-noi-lan-thu-hai-tro-di" id="ket-noi-lan-thu-hai-tro-di"></a>

Trong trường hợp shop của đối tác đã từng kết nối với eTop, đối tác cần có thông tin `shop_id`. Gửi request như sau:

```sh
# Sử dụng shop_id (bắt buộc)
curl https://api.d.etop.vn/v1/partner.Shop/AuthorizeShop \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $API_KEY" \
    -d '{
        "shop_id": "<id của shop do eTop cung cấp>"
    }'
```

{% hint style="info" %}
**Lưu ý**: nếu không cung cấp `shop_id`, shop sẽ luôn được yêu cầu đăng nhập lại. Để thuận tiện cho shop, đối tác cần lưu trữ `shop_id` để sử dụng.
{% endhint %}

### Xử lý kết quả <a href="#xu-ly-ket-qua" id="xu-ly-ket-qua"></a>

**200 OK `type=shop_request`**

```json
{
  "code": "ok",
  "msg": "Sử dụng mã này để hỏi quyền tạo đơn hàng với tư cách shop (có hiệu lực trong 15 phút)",
  "type": "shop_request",
  "auth_token": "request:Ut8zhsaQp_kwKi2u2gEch_9R3yiQvpFUnB5mE_MRfks",
  "expires_in": 900
}
```

Kết quả này nghĩa là shop sẽ được yêu cầu đăng nhập để xác nhận tài khoản. Đối tác gửi nguyên vẹn `auth_token` đến client.

**200 OK `type=shop_key`**

```json
{
  "code": "ok",
  "msg": "Sử dụng mã này để tạo đơn hàng với tư cách shop (có hiệu lực khi shop vẫn tiếp tục sử dụng dịch vụ của đối tác)",
  "type": "shop_key",
  "auth_token": "shop1052570182707778180:KgGTCsQHSzfKBt8la63c7E6NRqqnD5RVRMrb0aJwa8jZCEbGCmJ7Q01aVwyZeGeR",
  "expires_in": -1
}
```

Kết quả này nghĩa là đối tác đã có thể tạo đơn hàng với tư cách shop. Đối tác gửi nguyên vẹn `auth_token` đến client và lưu lại để sử dụng tiếp tục.

{% hint style="info" %}
**Lưu ý**: cần lưu ý rằng kể cả khi shop đã từng kết nối với eTop, nhưng shop vẫn có thể được yêu cầu đăng nhập lại. Tình huống này xảy ra khi shop đã gỡ liên kết với tài khoản của đối tác.
{% endhint %}

### Các lỗi thường gặp <a href="#cac-loi-thuong-gap" id="cac-loi-thuong-gap"></a>

**400 Bad Request**

```json
{
  "code": "invalid_argument",
  "msg": "..."
}
```

Vui lòng kiểm tra lại các giá trị cung cấp.

**401 Unauthorized**

```json
{
  "code": "unauthenticated",
  "msg": "..."
}
```

Vui lòng kiểm tra lại request đã bao gồm header `Authorization: <api_key>` đúng.

**403 Forbidden**

```json
{
    "code": "permission_denied",
    "msg": "...",
    "meta": {
        "reason": "Chỉ có thể sử dụng shop_id nếu shop đã từng đăng nhập qua hệ thống của đối tác."
    }
}
```

Shop chưa bao giờ đăng nhập vào eTop thông qua hệ thống của đối tác. Hãy thử lại với request mà không bao gồm `shop_id`.

{% hint style="info" %}
**Lưu ý**: trong trường hợp shop từng đăng nhập vào eTop thông qua hệ thống của đối tác, sau đó gỡ liên kết với đối tác, kết quả trả về vẫn là `200 OK` với yêu cầu shop đăng nhập và cấp quyền lại. Nên lỗi `permission_denied` chỉ xảy ra khi `shop_id` được cung cấp không chính xác.
{% endhint %}

**404 Not Found**

```json
{
    "code": "bad_route",
    "msg": "unexpected Content-Type: \"\""
}
```

Vui lòng kiểm tra lại path và header `Content-Type: application/json`.

**409 Conflict**

```json
{
  "code": "already_exists",
  "msg": "Mã đơn hàng external_id đã tồn tại. Vui lòng kiểm tra lại.",
  "meta": {
    "duplicated": "external_id"
  }
}
```

Mã đơn hàng của bạn đã tồn tại ở **eShip**. Bạn có thể huỷ đơn cũ và thử lại. [Xem thêm](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#phan-biet-external-id-va-external-code)

## Sử dụng các API khác <a href="#su-dung-cac-api-khac" id="su-dung-cac-api-khac"></a>

Đối tác có thể sử dụng `shop_key` có được ở trên để [huỷ đơn hàng](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#huy-don-hang) hoặc sử dụng `api_key` được cung cấp để [nhận thay đổi qua webhook](https://api-eship-dev.sobanhang.com/doc/partner/changes.html).
