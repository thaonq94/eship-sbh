# 📋 Tạo tài khoản

## Kiểm tra môi trường <a href="#kiem-tra-moi-truong" id="kiem-tra-moi-truong"></a>

#### Development <a href="#development" id="development"></a>

* Đăng nhập: [shop.sandbox.etop.vn/login](https://shop.sandbox.etop.vn/login)
* Tài liệu: [sandbox.apihub.vn/doc](https://sandbox.apihub.vn/doc)
* API: [sandbox.apihub.vn](https://sandbox.apihub.vn/)

#### Production <a href="#production" id="production"></a>

* Đăng nhập: [etop.vn/login](https://etop.vn/login)
* Tài liệu: [api.apihub.vn/doc](https://api.apihub.vn/doc)
* API: [api.apihub.vn](https://api.apihub.vn/)

## Tạo tài khoản mới <a href="#tao-tai-khoan-moi" id="tao-tai-khoan-moi"></a>

Đăng nhập hoặc tạo một tài khoản mới tại [đây](https://shop.d.etop.vn/login).

## Lấy thông tin `api_key` <a href="#lay-thong-tin-api-key" id="lay-thong-tin-api-key"></a>

[Liên hệ với eShip](https://api-eship-dev.sobanhang.com/doc/component/setup-account.html#lien-he-voi-chung-toi) và gửi thông tin tài khoản của bạn để lấy `api_key`.

Để kiểm tra `api_key` là đúng, bạn có thể gửi request như sau:

```sh
export API_KEY=<api_key>
curl https://api.d.etop.vn/v1/partner.Misc/CurrentAccount \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $API_KEY" \
    -d '{}'
```

Lưu ý

Thông tin `api_key` cần được giữ bí mật và không nên được gửi cho người khác hoặc gửi cho client.

## Liên hệ với chúng tôi <a href="#lien-he-voi-chung-toi" id="lien-he-voi-chung-toi"></a>

Bạn cần sử dụng [Telegram](https://api-eship-dev.sobanhang.com/doc/contact-us.html#su-dung-telegram) và tham gia vào group hỗ trợ của eTop bằng liên kết sau: [https://t.me/joinchat/D0TcHVXurUO3Q3POHjNLaA](https://t.me/joinchat/D0TcHVXurUO3Q3POHjNLaA)
