# 📋 Sử dụng

Trước khi bắt đầu, hãy kiểm tra bạn đã sử dụng đúng môi trường [Development](https://sandbox.apihub.vn/) hoặc [Production](https://api.apihub.vn/) và tạo một tài khoản theo hướng dẫn ở [đây](https://api-eship-dev.sobanhang.com/doc/partner/setup-account.html).

## Cấu hình <a href="#cau-hinh" id="cau-hinh"></a>

Sử dụng `api_key` được cung cấp và cấu hình như sau:

```sh
export API_KEY=<api_key>
export API_HOST=https://api.d.etop.vn
```

Hướng dẫn sử dụng các lệnh `export` và `curl` được cung cấp sẵn khi chạy bằng terminal trên hệ điều hành Linux hoặc Mac. Trong trường hợp bạn sử dụng môi trường khác, vui lòng thay thế bằng các thao tác tương đương.

## Cấu trúc API <a href="#cau-truc-api" id="cau-truc-api"></a>

#### Request <a href="#request" id="request"></a>

Một lời gọi API tiêu biểu như sau:

```sh
curl https://api.d.etop.vn/v1/partner.Misc/CurrentAccount \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $API_KEY" \
  -d '{}'
```

Tất cả request sử dụng giao thức HTTPS, method POST và truyền giá trị bằng body sử dụng định dạng dữ liệu `application/json`. Các header bắt buộc là `Content-Type` và `Authorization`.

#### Response <a href="#response" id="response"></a>

Response sử dụng định dạng dữ liệu `application/json` được set trong header `Content-Type`. Nếu request thành công, HTTP status code là `200`. Nếu request lỗi, HTTP status code có thể là `4xx` hoặc `5xx` với cấu trúc tương tự như sau:

```
{
  "code": "invalid_argument",
  "msg": "..."
}
```

## Kết nối tài khoản shop <a href="#ket-noi-tai-khoan-shop" id="ket-noi-tai-khoan-shop"></a>

[**POST /v1/partner.Shop/AuthorizeShop**](https://api.d.etop.vn/doc/ext/partner#operation/partner.Shop-AuthorizeShop)

Việc kết nối được thực hiện theo mô hình redirect. Đối tác cần chuẩn bị một địa chỉ url để nhận redirect sau khi shop đăng nhập thành công:

```sh
export REDIRECT_URL="https://example.com/redirect?token=87209412"
```

Đối tác nên đính kèm token hoặc thông tin shop của đối tác trong `redirect_url` để xác định được shop tương ứng khi nhận redirect.

Quá trình kết nối với shop:

1. Gửi request đến [/v1/partner.Shop/AuthorizeShop](https://api.d.etop.vn/doc/ext/partner#operation/partner.Shop-AuthorizeShop).
2. Redirect đến địa chỉ url được cung cấp bởi `auth_url`, chờ shop xác thực tài khoản và nhận redirect từ eTop.
3. Gửi lại request với `shop_id` nhận được.
4. Lưu `shop_id` và `auth_token` để sử dụng trong các API khác.

### Kết nối lần đầu <a href="#ket-noi-lan-dau" id="ket-noi-lan-dau"></a>

Trường hợp shop của đối tác đã từng kết nối với eTop và đối tác đã có thông tin `shop_id` thì bỏ qua bước này, di chuyển đến [bước sau](https://api-eship-dev.sobanhang.com/doc/partner/getting-started.html#ket-noi-lan-thu-hai-tro-di). Trong trường hợp shop của đối tác chưa từng kết nối với eTop, đối tác có thể gửi request như sau:

```sh
# Mặc định
curl https://api.d.etop.vn/v1/partner.Shop/AuthorizeShop \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $API_KEY" \
    -d '{
        "redirect_url": "'$REDIRECT_URL'"
    }'

# Hoặc cung cấp thông tin sẵn có (các mục là không bắt buộc)
curl https://api.d.etop.vn/v1/partner.Shop/AuthorizeShop \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $API_KEY" \
    -d '{
        "email": "<địa chỉ email>",
        "phone": "<số điện thoại>",
        "name": "<tên>",
        "redirect_url": "'$REDIRECT_URL'"
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
        "shop_id": "<id của shop do eTop cung cấp>",
        "redirect_url": "'$REDIRECT_URL'"
    }'
```

Đối tác vẫn cần cung cấp `redirect_url` vì có thể xảy ra tình huống shop cần đăng nhập lại. Lúc này `redirect_url` sẽ được sử dụng để eTop redirect về sau khi shop đăng nhập thành công.

### Xử lý kết quả <a href="#xu-ly-ket-qua" id="xu-ly-ket-qua"></a>

**200 OK `type=shop_request`**

```json
{
  "code": "ok",
  "msg": "Sử dụng mã này để hỏi quyền tạo đơn hàng với tư cách shop (có hiệu lực trong 15 phút)",
  "type": "shop_request",
  "auth_token": "request:Ut8zhsaQp_kwKi2u2gEch_9R3yiQvpFUnB5mE_MRfks",
  "expires_in": 900,
  "auth_url": "https://auth.d.etop.vn/login?token=request:Ut8zhsaQp_kwKi2u2gEch_9R3yiQvpFUnB5mE_MRfks"
}
```

Kết quả này nghĩa là shop sẽ được yêu cầu đăng nhập để xác nhận tài khoản. Kết quả trả về sẽ cung cấp một địa chỉ url bằng field `auth_url` để đối tác redirect shop đến và xác thực tài khoản.

Sau khi thực hiện xác thực tài khoản, eTop sẽ redirect về địa chỉ url đối tác cung cấp kèm với `shop_id`, theo cấu trúc: `$REDIRECT_URL?shop_id=<shop_id>`.

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

Kết quả này nghĩa là đối tác đã có thể tạo đơn hàng với tư cách shop. Hãy lưu lại `auth_token` ứng với tài khoản của shop để sử dụng tiếp tục.

{% hint style="info" %}
**Lưu ý:** cần lưu ý rằng kể cả khi shop đã từng kết nối với eTop, nhưng shop vẫn có thể được yêu cầu đăng nhập lại. Tình huống này xảy ra khi shop đã gỡ liên kết với tài khoản của đối tác. Lúc này, response sẽ có `type=shop_request` và `auth_url` được trả về, đối tác cần redirect về địa chỉ này để shop đăng nhập lại.
{% endhint %}

### Kiểm tra `shop_key` <a href="#kiem-tra-shop-key" id="kiem-tra-shop-key"></a>

Để kiểm tra `shop_key` nhận được là đúng, đối tác có thể gửi request đến [/v1/partner.Shop/CurrentShop](https://api.d.etop.vn/doc/ext/partner#operation/partner.Shop-CurrentShop):

```sh
export API_KEY=<api_key>
curl https://api.d.etop.vn/v1/partner.Shop/CurrentShop \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $SHOP_KEY" \
    -d '{}'
```

## Các lỗi thường gặp <a href="#cac-loi-thuong-gap" id="cac-loi-thuong-gap"></a>

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

Mã đơn hàng của bạn đã tồn tại ở eTop. Bạn có thể huỷ đơn cũ và thử lại. [Xem thêm](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#phan-biet-external-id-va-external-code)

### Tra cứu API <a href="#tra-cuu-api" id="tra-cuu-api"></a>

Tra cứu chi tiết API ở [đây](https://api.d.etop.vn/doc/ext/partner).

\
