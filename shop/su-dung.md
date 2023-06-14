# 📋 Sử dụng

{% hint style="info" %}
Trước khi bắt đầu, hãy kiểm tra bạn đã sử dụng đúng môi trường [Development](https://sandbox.apihub.vn/) hoặc [Production](https://api.apihub.vn/) và tạo một tài khoản theo hướng dẫn ở [đây](https://api-eship-dev.sobanhang.com/doc/shop/setup-account.html).
{% endhint %}

## Cấu hình

Sử dụng `api_key` được cung cấp và cấu hình như sau:

```shell
export API_KEY=<api_key>
export API_HOST=https://api.d.etop.vn
```

> Hướng dẫn sử dụng các lệnh `export` và `curl` được cung cấp sẵn khi chạy bằng terminal trên hệ điều hành Linux hoặc Mac. Trong trường hợp bạn sử dụng môi trường khác, vui lòng thay thế bằng các thao tác tương đương.

## Cấu trúc API

### Request <a href="#request" id="request"></a>

Một lời gọi API tiêu biểu như sau:

```sh
curl https://api.d.etop.vn/v1/shop.Misc/CurrentAccount \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $API_KEY" \
  -d '{}'
```

Tất cả request sử dụng giao thức HTTPS, method POST và truyền giá trị bằng body sử dụng định dạng dữ liệu `application/json`. Các header bắt buộc là `Content-Type` và `Authorization`.

### Response <a href="#response" id="response"></a>

Response sử dụng định dạng dữ liệu `application/json` được set trong header `Content-Type`. Nếu request thành công, HTTP status code là `200`. Nếu request lỗi, HTTP status code có thể là `4xx` hoặc `5xx` với cấu trúc tương tự như sau:

```json
{
  "code": "invalid_argument",
  "msg": "..."
}
```

### Các lỗi thường gặp

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

\
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

Mã đơn hàng của bạn đã tồn tại ở eTop. Bạn có thể huỷ đơn cũ và thử lại. [Xem thêm](https://api-eship-dev.sobanhang.com/doc/shop/shipping.html#phan-biet-external-id-va-external-code)\


### Tra cứu API <a href="#tra-cuu-api" id="tra-cuu-api"></a>

Tra cứu chi tiết API ở [đây](https://api.d.etop.vn/doc/ext/shop).
