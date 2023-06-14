# 📋 Phụ lục

## Mô hình <a href="#mo-hinh" id="mo-hinh"></a>

![](https://api-eship-dev.sobanhang.com/doc/assets/img/status.b1e83be3.svg)

* Status³: Z, P, N
* Status⁴: Z, P, N, S
* Status⁵: Z, P, N, S, NS

### Giá trị <a href="#gia-tri" id="gia-tri"></a>

| Status | Nội dung                 | Tài khoản³     | Giao hàng⁵   | Đơn hàng⁵                 | Đối soát⁴          |
| ------ | ------------------------ | -------------- | ------------ | ------------------------- | ------------------ |
| Z      | Zero                     | Chưa kích hoạt | Mới tạo      | Mới tạo                   | Không cần đối soát |
| P      | Positive                 | Đã kích hoạt   | Đã giao hàng | Đã hoàn thành và đối soát | Đã đối soát        |
| N      | Negative                 | Đã tạm ngưng   | Đã huỷ       | Đã huỷ                    | -                  |
| S      | Superposition            | -              | Đang xử lý   | Đang xử lý                | Đang chờ đối soát  |
| NS     | Negatively Superposition | -              | Đã trả hàng  | Đã trả hàng và đối soát   | -                  |

## Trạng thái giao hàng <a href="#trang-thai-giao-hang" id="trang-thai-giao-hang"></a>

![](https://api-eship-dev.sobanhang.com/doc/assets/img/shipping\_state.c2bc72ba.svg)

| ShippingState | ShippingStatus | Ghi chú                                                                |
| ------------- | -------------- | ---------------------------------------------------------------------- |
| default       | Z              | Mới tạo (chưa thông báo đơn vị vận chuyển, hoặc gặp lỗi khi thông báo) |
| created       | Z              | Mới tạo (đã thông báo đơn vị vận chuyển)                               |
| picking       | S              | Đang lấy hàng                                                          |
| holding       | S              | Đang luân chuyển hoặc lưu kho                                          |
| returning     | S              | Đang trả hàng                                                          |
| returned      | NS             | Đã trả hàng                                                            |
| delivering    | S              | Đang giao hàng                                                         |
| delivered     | P              | Đã giao hàng                                                           |
| unknown       | S              | Không xác định                                                         |
| undeliverable | S              | Đơn hàng bị mất hoặc bị giữ lại                                        |
| cancelled     | N              | Đã huỷ                                                                 |

Deprecated: ~~`confirmed`~~, ~~`processing`~~
