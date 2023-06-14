# 📋 Tạo tài khoản

## Kiểm tra môi trường

#### Development <a href="#development" id="development"></a>

* Đăng nhập: [https://eship-dev.sobanhang.com/register](https://eship-dev.sobanhang.com/register)
* API: [https://api-eship-dev.sobanhang.com/](https://api-eship-dev.sobanhang.com/)

#### Production <a href="#production" id="production"></a>

* Đăng nhập: [http://eship.sobanhang.com](http://eship.sobanhang.com/)
* API: [https://api-eship.sobanhang.com/](https://api-eship-dev.sobanhang.com/)

## Tạo tài khoản mới

Đăng nhập hoặc tạo một tài khoản mới tại [đây](https://eship-dev.sobanhang.com/register)

## Lấy thông tin `api_key`

[Liên hệ với eTop](https://api-eship-dev.sobanhang.com/doc/shop/setup-account.html#lien-he-voi-chung-toi) và gửi thông tin tài khoản của bạn để lấy `api_key`.

Để kiểm tra `api_key` là đúng, bạn có thể gửi request như sau:

```sh
export API_KEY=<api_key>
curl https://api.d.etop.vn/v1/shop.Misc/CurrentAccount \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $API_KEY" \
    -d '{}'
```

{% hint style="info" %}
**Lưu ý:** thông tin `api_key` cần được giữ bí mật và không nên được gửi cho người khác hoặc gửi cho client.
{% endhint %}

## Liên hệ với chúng tôi <a href="#lien-he-voi-chung-toi" id="lien-he-voi-chung-toi"></a>

Bạn cần sử dụng [Telegram](https://api-eship-dev.sobanhang.com/doc/contact-us.html#su-dung-telegram) và tham gia vào group hỗ trợ của eTop bằng liên kết sau: [https://t.me/joinchat/D0TcHVXurUO3Q3POHjNLaA](https://t.me/joinchat/D0TcHVXurUO3Q3POHjNLaA)
