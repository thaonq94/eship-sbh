# 📋 Sử dụng

Trước khi bắt đầu, hãy kiểm tra bạn đã sử dụng đúng môi trường [Development](https://sandbox.apihub.vn/) hoặc [Production](https://api.apihub.vn/) và tạo một tài khoản theo hướng dẫn ở [đây](https://api-eship-dev.sobanhang.com/doc/component/setup-account.html).

## Cài đặt <a href="#cai-dat" id="cai-dat"></a>

#### Server <a href="#server" id="server"></a>

Sử dụng `api_key` được cung cấp và cấu hình như sau:

```sh
export API_KEY=<api_key>
export API_HOST=https://api.d.etop.vn
```

Hướng dẫn sử dụng các lệnh `export` và `curl` được cung cấp sẵn khi chạy bằng terminal trên hệ điều hành Linux hoặc Mac. Trong trường hợp bạn sử dụng môi trường khác, vui lòng thay thế bằng các thao tác tương đương.

#### Client <a href="#client" id="client"></a>

Thêm đoạn mã sau vào trang web:

```html
<script src="https://api.d.etop.vn/dist/ecom/ecom-polyfill.js"></script>
```

Xem thêm các lựa chọn khác ở [đây](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#cai-dat) nếu bạn muốn sử dụng script _minified_ hoặc không bao gồm _polyfill_.

## Kết nối tài khoản shop (server) <a href="#ket-noi-tai-khoan-shop-server" id="ket-noi-tai-khoan-shop-server"></a>

[**POST /v1/partner.Shop/AuthorizeShop**](https://api.d.etop.vn/doc/ext/partner#operation/partner.Shop-AuthorizeShop)

Đối tác có thể kết nối với shop theo hai bước như sau:

#### Kết nối lần đầu <a href="#ket-noi-lan-dau" id="ket-noi-lan-dau"></a>

Trường hợp shop của đối tác đã từng kết nối với eTop và đối tác đã có thông tin shop\_id thì bỏ qua bước này, di chuyển đến [bước sau](https://api-eship-dev.sobanhang.com/doc/component/getting-started.html#ket-noi-lan-thu-hai-tro-di). Trong trường hợp shop của đối tác chưa từng kết nối với eTop, đối tác có thể gửi request như sau:

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

#### Kết nối lần thứ hai trở đi <a href="#ket-noi-lan-thu-hai-tro-di" id="ket-noi-lan-thu-hai-tro-di"></a>

Trong trường hợp shop của đối tác đã từng kết nối với eTop, đối tác cần có thông tin `shop_id`. Gửi request như sau:

```json
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

Lưu ý

Cần lưu ý rằng kể cả khi shop đã từng kết nối với eTop, nhưng shop vẫn có thể được yêu cầu đăng nhập lại. Tình huống này xảy ra khi shop đã gỡ liên kết với tài khoản của đối tác.

### Các lỗi thường gặp <a href="#cac-loi-thuong-gap" id="cac-loi-thuong-gap"></a>

Xem chi tiết ở [Server-Side API](https://api-eship-dev.sobanhang.com/doc/component/server-api.html#cac-loi-thuong-gap)

## Khởi tạo component (client) <a href="#khoi-tao-component-client" id="khoi-tao-component-client"></a>

Tạo một thẻ `<div>` cho Component trên trang web:

```html
<body>
  <div id="component"></div>
</body>
```

Và sử dụng đoạn mã sau để khởi tạo component:

```javascript
var etopComponent = etop.init({
  elem: 'etop-component',
  auth_token: '<token của shop được cung cấp bởi server>',
  customize: {
    fromAddress: false,
    toAddress: false,
    orderFees: false,
    transportFees: true,
    submitButton: false
  }
})
  .onReady(() => console.log('Đã sẵn sàng để tạo đơn hàng'))
  .onAccessGranted((data) => console.log('Lưu shop_id vào server', data.shop_id))
  .onError((error) => console.log('Lỗi', error))
```

Shop sẽ nhận được yêu cầu đăng nhập và cần xác nhận trước khi có thể bắt đầu tạo đơn hàng qua trang web của bạn. Nếu shop đăng nhập thành công, bạn sẽ nhận được thông báo qua hàm `.onReady()`, ngược lại callback của hàm `.onError()` sẽ được gọi.

Lưu ý

Sử dụng `auth_token` do server cung cấp. Xin đừng nhầm lẫn với `api_key`, là thông tin cần được giữ bí mật và không bao giờ gửi cho client.

## Tạo đơn hàng (client) <a href="#tao-don-hang-client" id="tao-don-hang-client"></a>

```json
var order = {
  "customer_address": {
    "full_name": "Khách hàng TEST",
    "phone": "0123456789",
    "province": "Hồ Chí Minh",
    "district": "Quận 2",
    "ward": "Phường An Phú",
    "address1": "Địa chỉ TEST của khách hàng"
  },
  "shipping_address": {
    "full_name": "Khách hàng TEST",
    "phone": "0123456789",
    "province": "Hồ Chí Minh",
    "district": "Quận 2",
    "ward": "Phường An Phú",
    "address1": "Địa chỉ TEST của khách hàng"
  },
  "lines": [
    {
      "product_name": "Sản phẩm mẫu",
      "quantity": 10,
      "list_price": 1200000,
      "retail_price": 1000000,
      "payment_price": 1000000,
      "attributes": [
        {"name": "Màu", "value": "xanh"},
        {"name": "Size", "value": "30cm"}
      ]
    }
  ],
  "total_items": 10,
  "basket_value": 10000000,
  "order_discount": 10000,
  "total_discount": 10000,
  "total_amount": 10070000,
  "total_fee": 80000,
  "order_note": "Ghi chú đơn hàng",
  "fee_lines": [
    {"type": "shipping", "amount": 80000}
  ],
  "shipping": {
    "chargeable_weight": 3000,
    "cod_amount": 290000,
    "carrier": "ghtk",
    "shipping_service_code": "8JSG1RAN",
    "shipping_service_fee": 133000,
    "shipping_note": "Đơn hàng TEST, xin huỷ giúp",
    "include_insurance": true,
    "try_on": "open",
    "pickup_address": {
      "full_name": "Người dùng TEST",
      "phone": "0123459876",
      "province": "Đà Nẵng",
      "district": "Quận Liên Chiểu",
      "ward": "Hòa Khánh Nam",
      "address1": "Địa chỉ TEST lấy hàng"
    }
  }
}

// tạo đơn hàng nháp
component.newOrder(order).catch((err) => console.error('Lỗi', error))

// bổ sung các giá trị cần thiết
component.updateOrder({
  "shipping": {
    "pickup_address": {
      "province": "Ho chi minh",
      "district": "Quan 10"
    }
  }
}).catch((err) => console.log('Lỗi', error))

// xác nhận đơn hàng
component.submitOrder().then(
  (res) => console.log('Đã tạo đơn hàng'),
  (err) => console.error('Lỗi', error)
)
```

Lưu ý

Bạn chỉ có thể tạo đơn hàng mới khi component đã sẵn sàng. Hãy dùng hàm lắng nghe sự kiện `.onReady()` hoặc kiểm tra thuộc tính `.ready` trước khi bạn bắt đầu tạo đơn hàng.

Tất cả các lỗi khi giao tiếp với component sẽ được gửi qua `.onError()`. Bạn có thể bỏ qua đoạn `.catch()` trong ví dụ trên.

## Lưu trữ `shop_id` (client và server) <a href="#luu-tru-shop-id-client-va-server" id="luu-tru-shop-id-client-va-server"></a>

Sau khi nhận được `shop_id`, bạn cần lưu trữ lại tương ứng với tài khoản shop của bạn. Nó cần thiết để shop có thể sử dụng trong lần tiếp theo và không phải đăng nhập lại.

```javascript
component.onAccessGranted((data) => {
  console.log('Lưu shop_id vào server', data.shop_id)
})
```

Chúc mừng bạn đã tạo đơn hàng thành công!
