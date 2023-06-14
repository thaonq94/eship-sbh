# 📋 Client-Side API

## Cài đặt <a href="#cai-dat" id="cai-dat"></a>

Thêm một trong các đoạn mã sau vào trang web của đối tác. Đoạn mã với `polyfill` hỗ trợ các trình duyệt cũ hơn trong khi đoạn mã với đuôi `.min.js` đã được nén lại dành cho môi trường production.

Trong trường hợp bạn không chắc chắn, hãy sử dụng đoạn mã đầu tiên.

```html
<script src="https://api.d.etop.vn/dist/ecom/ecom-polyfill.js"></script>

<!-- without polyfill -->
<script src="https://api.d.etop.vn/dist/ecom/ecom.js"></script>

<!-- minified and polyfill -->
<script src="https://api.d.etop.vn/dist/ecom/ecom-polyfill.min.js"></script>

<!-- minified without polyfill -->
<script src="https://api.d.etop.vn/dist/ecom/ecom.min.js"></script>
```

## Ví dụ <a href="#vi-du" id="vi-du"></a>

```javascript
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

var component = etop.init({
  elem: "component",
  auth_token: auth_token,
  customize: {
    fromAddress: false,
    toAddress: false,
    orderFees: false,
    transportFees: true,
    submitButton: false,
  }
}).onAccessGranted(data => {
    console.log("Access granted", data);
    if (data.data) {
      localStorage.setItem("shop_id", data.shop_id);
    }
  })
  .onError((data) => {
    if (data.event !== "hello") {
      console.error(data.error.message, data.event);
    }
  })
  .onReady(() => {
    component.newOrder(order);
  });
```

## Khởi tạo <a href="#khoi-tao" id="khoi-tao"></a>

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

Lưu ý

Sử dụng `auth_token` do server cung cấp. Xin đừng nhầm lẫn với `api_key`, là thông tin cần được giữ bí mật và không bao giờ gửi cho client.

### etop.init() <a href="#etop-init" id="etop-init"></a>

* `init(options) -> instance`

Tham số:

* `options` Object
  * `elem` String | HTMLDivElement Vị trí component sẽ được thêm vào trang web
  * `auth_token` String Mã được cung cấp bởi server
  * `customize` Object Bật tắt các field nhập trên giao diện của component. Xem [ComponentOption](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#componentoption)
* `instance` Object Cung cấp các API để điều khiển component và tạo đơn hàng. Xem [Component API](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#component-api)

## Component API <a href="#component-api" id="component-api"></a>

Các hàm dùng để giao tiếp với component hỗ trợ cả mô hình [callback](https://developer.mozilla.org/en-US/docs/Glossary/Callback\_function) và mô hình [promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Promise) giúp bạn có thể lựa chọn cách sử dụng phù hợp.

### .newOrder() <a href="#neworder" id="neworder"></a>

* `newOrder(callback)`
* `newOrder() -> Promise<>`
* `newOrder(inputData, callback)`
* `newOrder(inputData) -> Promise<>`

Tham số:

* `inputData` Object Xem [Order](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#order)
* `callback` Function
  * `err` Error
* `Promise<>` Promise

Chuẩn bị đơn hàng mới. Xoá các nội dung cũ đã nhập.

```javascript
component.newOrder();
```

Chuẩn bị đơn hàng mới đồng thời nhập nội dung

```javascript
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

component.newOrder(order);
```

### .updateOrder() <a href="#updateorder" id="updateorder"></a>

* `updateOrder(inputData, callback)`
* `updateOrder(inputData) -> Promise<order>`

Tham số:

* `inputData` Object Xem [Order](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#order)
* `callback` Function
  * `err` Error
  * `order` Object Xem [Order](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#order)
* `Promise<order>` Promise
  * `order` Object Xem [Order](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#order)

Sửa giá trị hiện tại của đơn hàng. Chỉ cần truyền các mục cần sửa. Các mục để trống sẽ được giữ nguyên.

{% hint style="info" %}
Chỉ có thể gọi sau `newOrder()` và trước khi `submitOrder()` thành công. Nếu `submitOrder()` không thành công, bạn có thể gọi `updateOrder()` để sửa nội dung trước khi `submitOrder()` lại.
{% endhint %}

```javascript
// Sửa giá trị mục 'shipping.pickup_address'
component.updateOrder({
  'shipping': {
    'pickup_address': {
      'province': 'Ho chi minh',
      'district': 'Quan 10'
    }
  }
}, (err, order) => console.log('Updated order', order, err));
```

### .getOrderData() <a href="#getorderdata" id="getorderdata"></a>

* `getOrderData(callback)`
* `getOrderData() -> Promise`

Lấy dữ liệu hiện tại.

```javascript
component.getOrderData((error, order) => console.log('Form data', order, error));
```

### .getShippingServices() <a href="#getshippingservices" id="getshippingservices"></a>

* `getShippingServices(callback)`
* `getShippingServices() -> Promise`

Lấy thông tin các gói giao hàng hiện tại.

```javascript
component.getShippingServices((error, services) => console.log('Shipping services', services, error));
```

### .submitOrder() <a href="#submitorder" id="submitorder"></a>

* `submitOrder(callback)`
* `submitOrder() -> Promise`

Xác nhận đơn hàng.

```
component.submitOrder((err, order) => console.log('Created order successfully', order, err));
```

## Thuộc tính <a href="#thuoc-tinh" id="thuoc-tinh"></a>

### .initialized <a href="#initialized" id="initialized"></a>

* `.initialized` Boolean

Trạng thái component đã khởi tạo (nhưng có thể chưa sẵn sàng).

### .ready <a href="#ready" id="ready"></a>

* `.ready` Boolean

Trạng thái component đã sẵn sàng để tạo đơn hàng. Bạn có thể gọi `.newOrder()` để bắt đầu tạo đơn hàng.

## Event <a href="#event" id="event"></a>

### .onReady() <a href="#onready" id="onready"></a>

Trạng thái component đã sẵn sàng để tạo đơn hàng. Bạn có thể gọi `.newOrder()` để bắt đầu tạo đơn hàng.

```javascript
component.onReady(() => {
  component.newOrder();
});
```

### .onAccessGranted() <a href="#onaccessgranted" id="onaccessgranted"></a>

Quyền được cấp sau khi đăng nhập. Bạn cần lưu trữ `shop_id` vào server để sử dụng trong những lần truy cập tiếp theo.

```
component.onAccessGranted((data) => {
  console.log('Access granted', data.shop_id);
})
```

### .onOrderCreated() <a href="#onordercreated" id="onordercreated"></a>

Một đơn hàng vừa được tạo.

```javascript
component.onOrderCreated(order => {
  console.log('Order created', order);
});
```

### .onError() <a href="#onerror" id="onerror"></a>

Nhận lỗi báo ra từ component.

```javascript
component.onError((error) => console.error('error', error));
```

## Cấu trúc dữ liệu <a href="#cau-truc-du-lieu" id="cau-truc-du-lieu"></a>

### ComponentOption <a href="#componentoption" id="componentoption"></a>

| Field Name    | Type    | Default | Notes                                                                                                                                                                             |
| ------------- | ------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| fromAddress   | boolean | false   | Khi bật, field `pickup_address` của [Order](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#order) sẽ được nhập trên giao diện (và không nhập được bằng api).   |
| toAddress     | boolean | false   | Khi bật, field `customer_address` của [Order](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#order) sẽ được nhập trên giao diện (và không nhập được bằng api). |
| orderFees     | boolean | false   | Bật/tắt các field thông tin đơn hàng.                                                                                                                                             |
| transportFees | boolean | true    | Bật/tắt các field thông tin vận chuyển.                                                                                                                                           |
| submitButton  | boolean | false   | Bật/tắt nút `Tạo đơn hàng` trên form.                                                                                                                                             |

### Order <a href="#order" id="order"></a>

| Field Name        | Type                                                                                                  | Ghi chú                                                                                                                                                 |
| ----------------- | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| external\_id      | string                                                                                                | ID đơn hàng của bạn. [Xem thêm](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#phan-biet-external-id-va-external-code)               |
| external\_code    | string                                                                                                | Mã đơn hàng của bạn. [Xem thêm](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#phan-biet-external-id-va-external-code)               |
| external\_url     | string                                                                                                | Địa chỉ URL đơn hàng trên hệ thống của bạn                                                                                                              |
| customer\_address | [Address](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#address)                  | Địa chỉ khách hàng                                                                                                                                      |
| shipping\_address | [Address](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#address)                  | Địa chỉ giao hàng                                                                                                                                       |
| lines             | Array<[OrderLine](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#orderline)>       | Danh sách sản phẩm trong đơn hàng                                                                                                                       |
| total\_items      | int                                                                                                   | <p>Tổng số lượng sản phẩm trong đơn hàng<br><br><em>=SUMlines(quantity)</em></p>                                                                        |
| basket\_value     | int                                                                                                   | <p>Giá trị đơn hàng (chưa bao gồm phí và giảm giá), dùng để tính mức phí bảo hiểm<br><br><em>=SUMlines(quantity * retail_price)</em></p>                |
| order\_discount   | int                                                                                                   | Giảm giá trên đơn hàng (chưa bao gồm giảm giá trên sản phẩm)                                                                                            |
| total\_discount   | int                                                                                                   | <p>Giảm giá trên đơn hàng (đã bao gồm giảm giá trên sản phẩm)<br><br><em>=SUMlines(quantity * (retail_price - payment_price)) + order_discount</em></p> |
| total\_fee        | int                                                                                                   | <p>Tổng phí trên đơn hàng<br><br><em>=SUMfee_lines(amount)</em></p>                                                                                     |
| fee\_lines        | Array<[OrderFeeLine](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#orderfeeline)> | Danh sách phí trên đơn hàng                                                                                                                             |
| total\_amount     | int                                                                                                   | <p>Tổng giá trị đơn hàng (đã bao gồm phí và giảm giá)<br><br><em>=basket_value - total_discount + total_fee</em></p>                                    |
| order\_note       | string                                                                                                | Ghi chú đơn hàng                                                                                                                                        |
| shipping          | [OrderShipping](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#ordershipping)      | Nội dung giao hàng                                                                                                                                      |

**Phân biệt `external_id` và `external_code`**

Các mục `external_id` và `external_code` là không bắt buộc khi tạo đơn hàng. Nếu được cung cấp, các mã này sẽ được hiển thị trên giao diện của người dùng (ưu tiên hiển thị `external_code`). Ràng buộc:

* `external_id` cần là duy nhất trên hệ thống của bạn.
* `external_code` cần là duy nhất _trong phạm vi một shop_ trên hệ thống của bạn.

Thông báo lỗi trả về khi mã được cung cấp không thoả mãn ràng buộc là _Mã đơn hàng external\_id đã tồn tại. Vui lòng kiểm tra lại._

Nếu bạn chỉ có một loại mã và mã đó là duy nhất trên toàn hệ thống của bạn, hãy sử dụng `external_id`.

### OrderShipping <a href="#ordershipping" id="ordershipping"></a>

| Field Name              | Type                                                                                     | Ghi chú                                                                                                                                                     |
| ----------------------- | ---------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| pickup\_address         | [Address](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#address)     | Địa chỉ lấy hàng                                                                                                                                            |
| return\_address         | [Address](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#address)     | Địa chỉ trả hàng (nếu để trống sẽ sử dụng địa chỉ lấy hàng)                                                                                                 |
| shipping\_service\_name | string                                                                                   | Tên gói cước giao hàng. Xem [danh sách](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#getshippingservices)                              |
| shipping\_service\_code | string                                                                                   | Mã gói cước giao hàng. Xem [danh sách](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#getshippingservices)                               |
| shipping\_service\_fee  | int                                                                                      | Phí dịch vụ giao hàng (đây là số tiền shop cần trả). Xem [danh sách](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#getshippingservices) |
| carrier                 | string                                                                                   | Đơn vị vận chuyển                                                                                                                                           |
| include\_insurance      | boolean                                                                                  | Bao gồm bảo hiểm trên đơn hàng (mặc định: `true`)                                                                                                           |
| try\_on                 | string<[TryOn](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#tryon)> | Ghi chú xem hàng. Bao gồm `try`, `open`, `none`.                                                                                                            |
| shipping\_note          | string                                                                                   | Ghi chú giao hàng                                                                                                                                           |
| cod\_amount             | int                                                                                      | Số tiền thu hộ shop cần thu của khách (COD)                                                                                                                 |
| chargeable\_weight      | int                                                                                      | **(g)** [Khối lượng tính phí của đơn hàng](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#khoi-luong-tinh-phi-cua-don-hang)              |
| gross\_weight           | int                                                                                      | **(g)** Khối lượng thực của đơn hàng                                                                                                                        |
| length                  | int                                                                                      | **(cm)** Kích thước đơn hàng (chiều dài), dùng để quy đổi khối lượng khi tính phí.                                                                          |
| width                   | int                                                                                      | **(cm)** Kích thước đơn hàng (chiều rộng), dùng để quy đổi khối lượng khi tính phí.                                                                         |
| height                  | int                                                                                      | **(cm)** Kích thước đơn hàng (chiều cao), dùng để quy đổi khối lượng khi tính phí.                                                                          |

**Khối lượng tính phí của đơn hàng:**

* _chargeable\_weight = MAX(gross\_weight, volumetric\_weight)_

Trong đó:

* Khối lượng thực của đơn hàng: _gross\_weight(g)_
* Khối lượng thể tích: _volumetric\_weight(g) = length(cm) \* width(cm) \* height(cm) / 5 (cm³/g)_

### OrderFeeLine <a href="#orderfeeline" id="orderfeeline"></a>

| Field Name | Type                                                                                                   | Ghi chú                                        |
| ---------- | ------------------------------------------------------------------------------------------------------ | ---------------------------------------------- |
| type       | string<[OrderFeeType](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#orderfeetype)> | Loại phí. Bao gồm: `shipping`, `tax`, `other`. |
| name       | string                                                                                                 | Tên phí                                        |
| code       | string                                                                                                 | Mã phí (nhập tuỳ ý)                            |
| desc       | string                                                                                                 | Mô tả                                          |
| amount     | int                                                                                                    | Số tiền                                        |

### OrderFeeType <a href="#orderfeetype" id="orderfeetype"></a>

| Type     | Ghi chú                                  |
| -------- | ---------------------------------------- |
| shipping | Số tiền phí giao hàng shop thu của khách |
| tax      | Thuế trên đơn hàng                       |
| other    | Phí khác                                 |

### OrderLine <a href="#orderline" id="orderline"></a>

Mô tả một dòng trong đơn hàng

| Field Name     | Type                                                                                            | Ghi chú                                                                         |
| -------------- | ----------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| product\_name  | string                                                                                          | Tên sản phẩm                                                                    |
| quantity       | int                                                                                             | Số lượng sản phẩm trong đơn hàng                                                |
| list\_price    | int                                                                                             | Giá trên bao bì (không bắt buộc)                                                |
| retail\_price  | int                                                                                             | Giá bán lẻ của sản phẩm (trước giảm giá, sử dụng để tính bảo hiểm cho đơn hàng) |
| payment\_price | int                                                                                             | Giá phải trả của sản phẩm trong đơn hàng (sau giảm giá)                         |
| image\_url     | string                                                                                          | Hình ảnh sản phẩm (không bắt buộc)                                              |
| attributes     | Array<[Attribute](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#attribute)> | Danh sách thuộc tính của sản phẩm                                               |

Ví dụ một đơn hàng với các phiên bản sản phẩm:

* Giày cao gót - xanh 30cm, số lượng 2
* Giày cao gót - đỏ 32cm, số lượng 3

Khi đó các dòng sản phẩm có thể được mô tả như sau: (cùng một sản phẩm với các phiên bản khác nhau cần được biểu diễn bằng các dòng riêng biệt)

```json
[
  {
    "product_name": "Giày cao gót",
    "quantity": 2,
    "list_price": 500000,
    "retail_price": 450000,
    "payment_price": 300000,
    "attributes": [
      {"name": "Màu", "value": "xanh"},
      {"name": "Size", "value": "30cm"}
    ]
  },
  {
    "product_name": "Giày cao gót",
    "quantity": 3,
    "list_price": 550000,
    "retail_price": 490000,
    "payment_price": 340000,
    "attributes": [
      {"name": "Màu", "value": "đỏ"},
      {"name": "Size", "value": "32cm"}
    ]
  }
]
```

### nAttribute <a href="#attribute" id="attribute"></a>

Thuộc tính sản phẩm (còn gọi là `variant`)

| Field Name | Ghi chú            |
| ---------- | ------------------ |
| name       | Tên thuộc tính     |
| value      | Giá trị thuộc tính |

Ví dụ một sản phẩm như _Giày cao gót_ có thể có các phiên bản:

* Màu xanh, size 30cm
* Màu xanh, size 32cm
* Màu đỏ, size 30cm
* Màu đỏ, size 32cm

Khi đó thuộc tính của phiên bản đầu tiên (màu xanh, size 30cm) có thể được mô tả như sau:

```json
[
  {"name": "Màu", "value": "xanh"},
  {"name": "Size", "value": "30cm"}
]
```

### Address <a href="#address" id="address"></a>

| Field Name | Type   | Ghi chú                                                                                                        |
| ---------- | ------ | -------------------------------------------------------------------------------------------------------------- |
| full\_name | string | Tên                                                                                                            |
| phone      | string | Số điện thoại                                                                                                  |
| email      | string | Địa chỉ email                                                                                                  |
| province   | string | Tên tỉnh/thành phố. [Xem thêm](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#nhap-dia-chi) |
| district   | string | Tên quận/huyện. [Xem thêm](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#nhap-dia-chi)     |
| ward       | string | Tên phường/xã. [Xem thêm](https://api-eship-dev.sobanhang.com/doc/component/client-api.html#nhap-dia-chi)      |
| address1   | string | Địa chỉ (dòng 1)                                                                                               |
| address2   | string | Địa chỉ (dòng 2)                                                                                               |

**Nhập địa chỉ**

Khi nhập thông tin cho các mục `province`, `district` và `ward`, bạn có thể gửi tên khu vực một cách linh hoạt như sau:

* `Hà Nội`, `Thành phố Hà Nội`, `TP. Hà Nội`, `ha noi`, `tp ha noi`
* `P10`, `Phường 10`, `Q3`, `Quận 3`

Xem [danh sách khu vực](https://api.d.etop.vn/doc/ext/partner#operation/partner.Misc-GetLocationList)

### TryOn <a href="#tryon" id="tryon"></a>

Ghi chú xem hàng

| Type | Ghi chú                 |
| ---- | ----------------------- |
| none | Không cho xem hàng      |
| open | Cho xem hàng, không thử |
| try  | Cho thử hàng            |

\
