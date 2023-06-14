# 📋 Đồng bộ dữ liệu

**eShip** cung cấp hai cách để bạn có thể đồng bộ dữ liệu đầy đủ và nhanh chóng:

1. [Lấy toàn bộ dữ liệu theo trình tự thời gian](https://api-eship-dev.sobanhang.com/doc/shop/changes.html#lay-toan-bo-du-lieu-theo-trinh-tu-thoi-gian) (**polling**): Bạn gửi request để lấy về toàn bộ các dữ liệu trong lần đầu tiên đồng bộ. Sau đó bạn tiếp tục gửi request để lấy về các dữ liệu đã thay đổi (bỏ qua các dữ liệu không thay đổi).
2. [Nhận request ngay khi có thay đổi](https://api-eship-dev.sobanhang.com/doc/shop/changes.html#nhan-thay-doi) (**webhook**): Bạn đăng ký webhook để nhận được request từ eTop mỗi khi có dữ liệu được thay đổi.

## Lấy toàn bộ dữ liệu theo trình tự thời gian <a href="#lay-toan-bo-du-lieu-theo-trinh-tu-thoi-gian" id="lay-toan-bo-du-lieu-theo-trinh-tu-thoi-gian"></a>

Để đồng bộ dữ liệu, bạn gửi request để lấy về lần lượt toàn bộ dữ liệu theo trình tự thời gian. Sau đó bạn tiếp tục gửi request để lấy những dữ liệu đã thay đổi sau thời điểm gọi request vừa rồi.

Minh họa sử dụng [/v1/shop.Product/ListProducts](https://api.d.etop.vn/doc/ext/shop#operation/shop.Product-ListProducts).

### Lấy toàn bộ dữ liệu <a href="#lay-toan-bo-du-lieu" id="lay-toan-bo-du-lieu"></a>

**Lấy 100 đối tượng cũ nhất**

Bạn gửi request như bên dưới:

```json
{
  "paging": {
    "sort": "updated_at",
    "limit": 100
  }
}
```

Với `paging.sort` là `updated_at` để sắp xếp kết quả trả về theo trình tự _thời gian thay đổi_ từ cũ nhất đến mới nhất.

Bạn có thể để trống `paging.sort`, lúc này bạn sẽ nhận được các dữ liệu trả về theo trình tự _thời gian tạo_ từ cũ nhất đến mới nhất.

**Lấy 100 đối tượng tiếp theo**

Thực hiện bước vừa rồi, bạn sẽ nhận được kết quả có dạng:

```json
{
  "products": { ... },
  "paging": {
    "next": "<NEXT_CURSOR>",
    "prev": "<PREV_CURSOR>"
  }
}
```

Để lấy 100 phần tử tiếp theo, bạn gửi request như bên dưới, với `<NEXT_CURSOR>` là giá trị được trả về trong `paging.next`:

```json
{
  "paging": {
    "after": "<NEXT_CURSOR>",
    "limit": 100
  }
}
```

Cứ tiếp tục gửi request tương tự cho đến khi bạn nhận được toàn bộ dữ liệu về sản phẩm.

Khi sắp xếp theo thứ tự _thời gian thay đổi_ (`updated_at`) một sản phẩm có thể được trả về nhiều hơn một lần. Đó là khi sản phẩm được thay đổi trong khoảng thời gian giữa 2 lần gọi request. Bạn cần lưu trữ lại nội dung mới nhất được trả về của sản phẩm.

## Lấy các đối tượng mới thay đổi <a href="#lay-cac-doi-tuong-moi-thay-doi" id="lay-cac-doi-tuong-moi-thay-doi"></a>

Sau khi lấy toàn bộ dữ liệu, bạn tiếp tục gửi request với `<NEXT_CURSOR>` được trả về trong `paging.next` từ request trước đó:

```json
{
  "paging": {
    "after": "<NEXT_CURSOR>",
    "limit": 100
  }
}
```

Lúc này, kết quả trả về sẽ không có phần tử nào:

```json
{
  "products": [],
  "paging": {
    "next": "<NEXT_CURSOR>",
    "prev": "<PREV_CURSOR>"
  }
}
```

Tuy nhiên, nếu có sản phẩm được thay đổi (hoặc thêm mới) trong thời gian đó, thì kết quả trả về sẽ chứa các sản phẩm được thay đổi (hoặc thêm mới) đó. Bạn lưu trữ lại các sản phẩm được thay đổi và `<NEXT_CURSOR>` để tiếp tục sử dụng.

Bạn chỉ cần lấy về toàn bộ dữ liệu trong lần đầu tiên đồng bộ. Kể từ đó, bạn chỉ cần gửi request sử dụng `<NEXT_CURSOR>` được trả về của request trước đó để lấy về những đối tượng được thay đổi mới nhất (bỏ qua những đối tượng không thay đổi).

## Nhận thay đổi <a href="#nhan-thay-doi" id="nhan-thay-doi"></a>

Khi có sự thay đổi về dữ liệu, eTop sẽ gửi request kèm theo chi tiết các thay đổi đến địa chỉ url được bạn cung cấp. Bạn cần đăng ký webhook để nhận được các thay đổi này.

### Đăng ký webhook <a href="#dang-ky-webhook" id="dang-ky-webhook"></a>

[**POST /v1/shop.Webhook/CreateWebhook**](https://api.d.etop.vn/doc/ext/shop#operation/shop.Webhook-CreateWebhook)

Gửi request như sau để đăng ký webhook và bắt đầu nhận thay đổi từ eTop:

```json
curl https://api.d.etop.vn/v1/shop.Webhook/CreateWebhook \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $API_KEY" \
    -d '
{
	"url": "https://example.com/callback/28929172",
	"entities": ["order", "fulfillment"],
	"fields": [],
	"metadata": ""
}'
```

Các thay đổi từ sau thời điểm đăng ký webhook sẽ được eTop gửi đến địa chỉ url bạn cung cấp. Khi nhận được webhook, bạn cần trả về status `200` và body `ok` để báo là bạn đã nhận webhook thành công. Webhook sẽ được gửi lại sau một khoảng thời gian nếu gặp lỗi, và sẽ ngưng gửi nếu gặp lỗi liên tục sau một số lần nhất định (thời gian gửi lại tối đa là 24 giờ). Để khôi phục trạng thái, hãy xoá webhook cũ và đăng ký lại.

Xem thêm [cấu trúc dữ liệu được gửi qua webhook](https://api.d.etop.vn/doc/ext/shop#operation/shop.Webhook-GetChanges).

Bạn nên đính kèm token trong địa chỉ url của webhook để khi nhận được webhook, bạn có thể biết là do eTop gửi chứ không phải một ai khác.

Tạm thời danh sách `fields` chưa được hỗ trợ. Sẽ được bổ sung trong các phiên bản tiếp theo.

### Xem trạng thái webhook <a href="#xem-trang-thai-webhook" id="xem-trang-thai-webhook"></a>

[**POST /v1/shop.Webhook/GetWebhooks**](https://api.d.etop.vn/doc/ext/shop#operation/shop.Webhook-GetWebhooks)

Gửi request như sau để xem trạng thái hiện tại của webhooks:

```json
curl https://api.d.etop.vn/v1/shop.Webhook/GetWebhooks \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $API_KEY" \
    -d '{}'
```

Kết quả trả về sẽ tương tự như sau:

```json
{
  "webhooks": [
    {
      "id": "1056533068556534027",
      "entities": ["order"],
      "fields": [],
      "url": "https://example.com/callback/28929172",
      "metadata": "",
      "created_at": "2018-12-17T13:02:16Z",
      "states": {
        "state": "retry",
        "last_sent_at": "2018-12-27T09:57:54Z",
        "last_error": {
          "error": "unexpected status: 401",
          "resp_status": 401,
          "resp_body": "",
          "retried": 5,
          "sent_at": "2018-12-27T09:57:54Z"
        }
      }
    }
  ]
}
```

Field `states.state` cho biết trạng thái hiện tại của webhook:

| State | Ghi chú                                                                                                    |
| ----- | ---------------------------------------------------------------------------------------------------------- |
| ok    | Webhook đã được gửi thành công.                                                                            |
| retry | Webhook gần nhất gặp lỗi, và sẽ được gửi lại sau một khoảng thời gian. Xem chi tiết lỗi ở `last_error`.    |
| stop  | Webhook gặp lỗi quá nhiều lần và đã ngưng gửi. Để khôi phục trạng thái, hãy xoá webhook cũ và đăng ký lại. |

### Xoá webhook <a href="#xoa-webhook" id="xoa-webhook"></a>

[**POST /v1/shop.Webhook/DeleteWebhook**](https://api.d.etop.vn/doc/ext/shop#operation/shop.Webhook-DeleteWebhook)

Xoá webhook để ngưng nhận thay đổi từ **eShip**:

```json
curl https://api.d.etop.vn/v1/shop.Webhook/DeleteWebhook \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $API_KEY" \
    -d '{"id": "<id của webhook>"}'
```
