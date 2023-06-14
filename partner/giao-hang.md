# 📋 Giao hàng

{% hint style="info" %}
Trang này trình bày từng bước sử dụng các API cần thiết để tạo và huỷ đơn hàng. Truy cập vào [đây](https://api.d.etop.vn/doc/ext/shop) để tra cứu chi tiết toàn bộ API hoặc tải file [swagger.json](https://api.d.etop.vn/doc/ext/shop/swagger.json).
{% endhint %}

Sử dụng `shop_key` có được ở [đây](https://api-eship-dev.sobanhang.com/doc/partner/getting-started.html) để gửi request:

```sh
export SHOP_KEY="<shop_key tương ứng với shop cần tạo đơn>"
```

## Lấy thông tin dịch vụ giao hàng <a href="#lay-thong-tin-dich-vu-giao-hang" id="lay-thong-tin-dich-vu-giao-hang"></a>

[**POST /v1/partner.Shipping/GetShippingServices**](https://api.d.etop.vn/doc/ext/partner#operation/partner.Shipping-GetShippingServices)

```sh
curl https://api.d.etop.vn/v1/partner.Shipping/GetShippingServices \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $SHOP_KEY" \
    -d '
{
  "pickup_address": {
    "province": "Đà Nẵng",
    "district": "Liên Chiểu"
  },
  "shipping_address": {
    "province": "Hồ Chí Minh",
    "district": "Quận 2"
  },
  "chargeable_weight": 3000,
  "length": 10,
  "width": 10,
  "height": 100,
  "basket_value": 10000000,
  "cod_amount": 290000,
  "include_insurance": true
}'
```

#### Request <a href="#request" id="request"></a>

| Field Name                 | Type   | Ghi chú                                                                                                                                    |
| -------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| pickup\_address.province   | string | Địa chỉ gửi hàng (tỉnh/thành). [Xem thêm](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#nhap-dia-chi)                      |
| pickup\_address.district   | string | Địa chỉ gửi hàng (quận/huyện). [Xem thêm](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#nhap-dia-chi)                      |
| shipping\_address.province | string | Địa chỉ giao hàng (tỉnh/thành). [Xem thêm](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#nhap-dia-chi)                     |
| shipping\_address.district | string | Địa chỉ giao hàng (quận/huyện). [Xem thêm](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#nhap-dia-chi)                     |
| chargeable\_weight         | int    | **(g)** [Khối lượng tính phí của đơn hàng](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#khoi-luong-tinh-phi-cua-don-hang) |
| gross\_weight              | int    | **(g)** Khối lượng thực của đơn hàng                                                                                                       |
| length                     | int    | **(cm)** Kích thước đơn hàng (chiều dài), dùng để quy đổi khối lượng khi tính phí.                                                         |
| width                      | int    | **(cm)** Kích thước đơn hàng (chiều rộng), dùng để quy đổi khối lượng khi tính phí.                                                        |
| height                     | int    | **(cm)** Kích thước đơn hàng (chiều cao), dùng để quy đổi khối lượng khi tính phí.                                                         |
| basket\_value              | int    | Giá trị đơn hàng (chưa bao gồm phí và giảm giá), dùng để tính mức phí bảo hiểm.                                                            |
| cod\_amount                | int    | Giá trị tiền thu hộ (COD), dùng để tính mức phí thu hộ (đối với Viettel Post).                                                             |

#### Response <a href="#response" id="response"></a>

```json
{
  "services": [
    {
      "code": "8JSG1RAN",
      "name": "Nhanh",
      "fee": 133000,
      "carrier": "ghtk",
      "estimated_pickup_at": "2018-12-14T11:00:00Z",
      "estimated_delivery_at": "2018-12-15T11:00:00Z"
    }
  ]
}
```

Kết quả trả về sẽ bao gồm nhiều gói cước của các đơn vị vận chuyển. Bạn chọn một gói cước, sử dụng `carrier`, `code` và `fee` tương ứng để tạo đơn hàng.

## Tạo và xác nhận đơn hàng <a href="#tao-va-xac-nhan-don-hang" id="tao-va-xac-nhan-don-hang"></a>

[**POST /v1/partner.Shipping/CreateAndConfirmOrder**](https://api.d.etop.vn/doc/ext/partner#operation/partner.Shipping-CreateAndConfirmOrder)

Với `carrier`, `shipping_service_code` và `shipping_service_fee` có được từ [bước trước](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#lay-thong-tin-dich-vu-giao-hang), bạn có thể tạo một đơn hàng mẫu với request như sau (đây là các mục tối thiểu cần có để tạo đơn hàng):

```sh
curl https://api.d.etop.vn/v1/partner.Shipping/CreateAndConfirmOrder \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $SHOP_KEY" \
    -d '
{
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
}'
```

### Order <a href="#order" id="order"></a>

| Field Name        | Type                                                                                              | Ghi chú                                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| external\_id      | string                                                                                            | ID đơn hàng của bạn. [Xem thêm](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#phan-biet-external-id-va-external-code)                   |
| external\_code    | string                                                                                            | Mã đơn hàng của bạn. [Xem thêm](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#phan-biet-external-id-va-external-code)                   |
| external\_url     | string                                                                                            | Địa chỉ URL đơn hàng trên hệ thống của bạn                                                                                                              |
| customer\_address | [Address](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#address)                  | Địa chỉ khách hàng                                                                                                                                      |
| shipping\_address | [Address](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#address)                  | Địa chỉ giao hàng                                                                                                                                       |
| lines             | Array<[OrderLine](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#orderline)>       | Danh sách sản phẩm trong đơn hàng                                                                                                                       |
| total\_items      | int                                                                                               | <p>Tổng số lượng sản phẩm trong đơn hàng<br><br><em>=SUMlines(quantity)</em></p>                                                                        |
| basket\_value     | int                                                                                               | <p>Giá trị đơn hàng (chưa bao gồm phí và giảm giá), dùng để tính mức phí bảo hiểm<br><br><em>=SUMlines(quantity * retail_price)</em></p>                |
| order\_discount   | int                                                                                               | Giảm giá trên đơn hàng (chưa bao gồm giảm giá trên sản phẩm)                                                                                            |
| total\_discount   | int                                                                                               | <p>Giảm giá trên đơn hàng (đã bao gồm giảm giá trên sản phẩm)<br><br><em>=SUMlines(quantity * (retail_price - payment_price)) + order_discount</em></p> |
| total\_fee        | int                                                                                               | <p>Tổng phí trên đơn hàng<br><br><em>=SUMfee_lines(amount)</em></p>                                                                                     |
| fee\_lines        | Array<[OrderFeeLine](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#orderfeeline)> | Danh sách phí trên đơn hàng                                                                                                                             |
| total\_amount     | int                                                                                               | <p>Tổng giá trị đơn hàng (đã bao gồm phí và giảm giá)<br><br><em>=basket_value - total_discount + total_fee</em></p>                                    |
| order\_note       | string                                                                                            | Ghi chú đơn hàng                                                                                                                                        |
| shipping          | [OrderShipping](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#ordershipping)      | Nội dung giao hàng                                                                                                                                      |

### **Phân biệt `external_id` và `external_code`**

Các mục `external_id` và `external_code` là không bắt buộc khi tạo đơn hàng. Nếu được cung cấp, các mã này sẽ được hiển thị trên giao diện của người dùng (ưu tiên hiển thị `external_code`). Ràng buộc:

* `external_id` cần là duy nhất trên hệ thống của bạn.
* `external_code` cần là duy nhất _trong phạm vi một shop_ trên hệ thống của bạn.

Thông báo lỗi trả về khi mã được cung cấp không thoả mãn ràng buộc là [already\_exists](https://api-eship-dev.sobanhang.com/doc/partner/getting-started.html#\_409-conflict).

Nếu bạn chỉ có một loại mã và mã đó là duy nhất trên toàn hệ thống của bạn, hãy sử dụng `external_id`.

### OrderShipping <a href="#ordershipping" id="ordershipping"></a>

| Field Name              | Type                                                                                 | Ghi chú                                                                                                                                                    |
| ----------------------- | ------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| pickup\_address         | [Address](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#address)     | Địa chỉ lấy hàng                                                                                                                                           |
| return\_address         | [Address](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#address)     | Địa chỉ trả hàng (nếu để trống sẽ sử dụng địa chỉ lấy hàng)                                                                                                |
| shipping\_service\_name | string                                                                               | Tên gói cước giao hàng. Xem [danh sách](https://api.d.etop.vn/doc/ext/partner#operation/partner.Shipping-GetShippingServices)                              |
| shipping\_service\_code | string                                                                               | Mã gói cước giao hàng. Xem [danh sách](https://api.d.etop.vn/doc/ext/partner#operation/partner.Shipping-GetShippingServices)                               |
| shipping\_service\_fee  | int                                                                                  | Phí dịch vụ giao hàng (đây là số tiền shop cần trả). Xem [danh sách](https://api.d.etop.vn/doc/ext/partner#operation/partner.Shipping-GetShippingServices) |
| carrier                 | string                                                                               | Đơn vị vận chuyển                                                                                                                                          |
| include\_insurance      | boolean                                                                              | Bao gồm bảo hiểm trên đơn hàng (mặc định: `true`)                                                                                                          |
| try\_on                 | string<[TryOn](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#tryon)> | Ghi chú xem hàng. Bao gồm `try`, `open`, `none`.                                                                                                           |
| shipping\_note          | string                                                                               | Ghi chú giao hàng                                                                                                                                          |
| cod\_amount             | int                                                                                  | Số tiền thu hộ shop cần thu của khách (COD)                                                                                                                |
| chargeable\_weight      | int                                                                                  | **(g)** [Khối lượng tính phí của đơn hàng](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#khoi-luong-tinh-phi-cua-don-hang)                 |
| gross\_weight           | int                                                                                  | **(g)** Khối lượng thực của đơn hàng                                                                                                                       |
| length                  | int                                                                                  | **(cm)** Kích thước đơn hàng (chiều dài), dùng để quy đổi khối lượng khi tính phí.                                                                         |
| width                   | int                                                                                  | **(cm)** Kích thước đơn hàng (chiều rộng), dùng để quy đổi khối lượng khi tính phí.                                                                        |
| height                  | int                                                                                  | **(cm)** Kích thước đơn hàng (chiều cao), dùng để quy đổi khối lượng khi tính phí.                                                                         |

**Khối lượng tính phí của đơn hàng:**

* _chargeable\_weight = MAX(gross\_weight, volumetric\_weight)_

Trong đó:

* Khối lượng thực của đơn hàng: _gross\_weight(g)_
* Khối lượng thể tích: _volumetric\_weight(g) = length(cm) \* width(cm) \* height(cm) / 5 (cm³/g)_

### OrderFeeLine <a href="#orderfeeline" id="orderfeeline"></a>

| Field Name | Type                                                                                               | Ghi chú                                        |
| ---------- | -------------------------------------------------------------------------------------------------- | ---------------------------------------------- |
| type       | string<[OrderFeeType](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#orderfeetype)> | Loại phí. Bao gồm: `shipping`, `tax`, `other`. |
| name       | string                                                                                             | Tên phí                                        |
| code       | string                                                                                             | Mã phí (nhập tuỳ ý)                            |
| desc       | string                                                                                             | Mô tả                                          |
| amount     | int                                                                                                | Số tiền                                        |

### OrderFeeType <a href="#orderfeetype" id="orderfeetype"></a>

| Type     | Ghi chú                                  |
| -------- | ---------------------------------------- |
| shipping | Số tiền phí giao hàng shop thu của khách |
| tax      | Thuế trên đơn hàng                       |
| other    | Phí khác                                 |

### OrderLine <a href="#orderline" id="orderline"></a>

Mô tả một dòng trong đơn hàng

| Field Name     | Type                                                                                        | Ghi chú                                                                         |
| -------------- | ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| product\_name  | string                                                                                      | Tên sản phẩm                                                                    |
| quantity       | int                                                                                         | Số lượng sản phẩm trong đơn hàng                                                |
| list\_price    | int                                                                                         | Giá trên bao bì (không bắt buộc)                                                |
| retail\_price  | int                                                                                         | Giá bán lẻ của sản phẩm (trước giảm giá, sử dụng để tính bảo hiểm cho đơn hàng) |
| payment\_price | int                                                                                         | Giá phải trả của sản phẩm trong đơn hàng (sau giảm giá)                         |
| image\_url     | string                                                                                      | Hình ảnh sản phẩm (không bắt buộc)                                              |
| attributes     | Array<[Attribute](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#attribute)> | Danh sách thuộc tính của sản phẩm                                               |

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

### Attribute <a href="#attribute" id="attribute"></a>

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

| Field Name | Type   | Ghi chú                                                                                                    |
| ---------- | ------ | ---------------------------------------------------------------------------------------------------------- |
| full\_name | string | Tên                                                                                                        |
| phone      | string | Số điện thoại                                                                                              |
| email      | string | Địa chỉ email                                                                                              |
| province   | string | Tên tỉnh/thành phố. [Xem thêm](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#nhap-dia-chi) |
| district   | string | Tên quận/huyện. [Xem thêm](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#nhap-dia-chi)     |
| ward       | string | Tên phường/xã. [Xem thêm](https://api-eship-dev.sobanhang.com/doc/partner/shipping.html#nhap-dia-chi)      |
| address1   | string | Địa chỉ (dòng 1)                                                                                           |
| address2   | string | Địa chỉ (dòng 2)                                                                                           |

#### **Nhập địa chỉ**

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

## Lấy thông tin đơn hàng và đơn giao hàng <a href="#lay-thong-tin-don-hang-va-don-giao-hang" id="lay-thong-tin-don-hang-va-don-giao-hang"></a>

[**POST /v1/partner.Shipping/GetOrder**](https://api.d.etop.vn/doc/ext/partner#operation/partner.Shipping-GetOrder)

Lấy thông tin đơn hàng và đơn giao hàng bằng `id`, `code` hoặc `external_id`.

```shell
curl https://api.d.etop.vn/v1/partner.Shipping/GetOrder \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $SHOP_KEY" \
    -d '{"id": "<id của đơn hàng>"}'
```

## Huỷ đơn hàng <a href="#huy-don-hang" id="huy-don-hang"></a>

[**POST /v1/partner.Shipping/CancelOrder**](https://api.d.etop.vn/doc/ext/partner#operation/partner.Shipping-CancelOrder)

Huỷ đơn hàng bằng `id`, `code` hoặc `external_id`. Bạn cần cung cấp lý do huỷ.

```sh
curl https://api.d.etop.vn/v1/partner.Shipping/CancelOrder \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $SHOP_KEY" \
    -d '{"id": "<id của đơn hàng>", "cancel_reason": "Đơn hàng TEST"}'
```
