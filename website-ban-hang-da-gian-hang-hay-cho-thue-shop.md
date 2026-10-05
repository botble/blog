---
title: "Website bán hàng đa gian hàng hay nền tảng cho thuê shop? Chọn nhầm là làm lại từ đầu"
slug: website-ban-hang-da-gian-hang-2026
description: "Làm web giống Shopee và làm web giống Haravan là hai sản phẩm khác nhau, không phải hai mức độ của cùng một thứ. Bài này phân biệt rõ ba mô hình, chi phí thật của từng cái, và mã nguồn Laravel tương ứng, mua trực tiếp bằng chuyển khoản VND."
categories:
  - Buyer Guides
  - Ecommerce
tags:
  - website-ban-hang-da-gian-hang
  - multi-vendor
  - multi-tenant
  - ecommerce-saas
  - laravel
  - martfury
  - botble
image: https://botble.com/storage/news/website-ban-hang-da-gian-hang-hero.jpg
status: published
is_featured: false
---

# Website bán hàng đa gian hàng hay nền tảng cho thuê shop?

![Ba mô hình: một shop, chợ đa gian hàng, và nền tảng cho thuê shop](https://botble.com/storage/news/website-ban-hang-da-gian-hang-hero.jpg)

Mình là Sang, tác giả bộ mã nguồn Botble. Câu hỏi mình nhận nhiều nhất từ khách Việt là *"em muốn làm web bán hàng đa gian hàng"*. Hỏi lại một câu thì ra hai nhóm người hoàn toàn khác nhau, và họ cần hai sản phẩm khác nhau.

Câu hỏi đó là: **tiền chảy từ ai sang ai?**

Mua nhầm ở đây không phải mất vài triệu. Nó là làm xong rồi phát hiện kiến trúc không chở nổi mô hình kinh doanh, và phải làm lại.

## Ba mô hình, đừng gộp chúng lại

**1. Một shop của bạn.** Bạn bán hàng của bạn. Khách vào, mua, bạn giao. Không có người bán nào khác.

**2. Chợ đa gian hàng.** Nhiều người bán cùng đăng hàng lên **một website duy nhất**. Khách vào một nơi, thấy hàng của mọi shop, mua chung một giỏ. Bạn ăn **hoa hồng trên mỗi đơn**. Đây là Shopee, Lazada, Tiki.

**3. Nền tảng cho thuê shop.** Mỗi người bán có **website riêng, tên miền riêng**, khách của họ không biết tới bạn. Bạn thu **tiền thuê bao hàng tháng**. Đây là Haravan, Sapo, Shopify.

Mô hình 2 và 3 nhìn từ ngoài giống nhau: đều "nhiều người bán". Nhưng chúng khác nhau ở chỗ quan trọng nhất.

| | Chợ đa gian hàng | Nền tảng cho thuê shop |
|---|---|---|
| Khách hàng cuối là của ai | **Của bạn** | **Của người bán** |
| Có bao nhiêu website | Một | Mỗi người bán một cái |
| Bạn kiếm tiền bằng | Hoa hồng mỗi đơn | Thuê bao hàng tháng |
| Doanh thu phụ thuộc | Lượng giao dịch | Số shop đang trả tiền |
| Người bán bỏ đi thì | Mất nguồn hàng, giữ được khách | Mất luôn doanh thu đó |
| Ví dụ | Shopee, Lazada | Haravan, Sapo, Shopify |

Dòng đầu tiên là dòng quyết định. **Ở mô hình chợ, khách là của bạn.** Người bán đến rồi đi, tệp khách vẫn ở lại. Ở mô hình cho thuê shop thì ngược lại: khách là của người bán, bạn chỉ bán phần mềm. Hai cách kiếm tiền đó dẫn tới hai cách làm sản phẩm, hai cách marketing, hai cách định giá.

Nên trước khi xem bất kỳ mã nguồn nào, hãy trả lời: **một năm nữa, bạn muốn gửi hoá đơn cho ai?**

## Nếu bạn chọn chợ đa gian hàng

Phần này dễ hơn. Bạn cần một website thương mại điện tử có thêm tầng người bán: đăng ký bán hàng, duyệt shop, duyệt sản phẩm, giỏ hàng tách đơn theo shop, hoa hồng, ví và rút tiền, đánh giá theo shop.

Hai lựa chọn Laravel của tụi mình:

- **Shofy** — $59, mua trực tiếp **$41.30** (khoảng 1,03 triệu ở tỷ giá 25.000đ). Trên CodeCanyon đang có 1.209 lượt bán, 4.96 sao từ 76 đánh giá.
- **MartFury** — $349, mua trực tiếp **$244.30** (khoảng 6,1 triệu). 1.119 lượt bán, 4.81 sao từ 74 đánh giá.

Chênh lệch giá là thật và nó phản ánh phạm vi: MartFury là bộ marketplace đầy đủ hơn. Nếu bạn mới bắt đầu và chưa chắc mô hình chạy được, Shofy rẻ hơn sáu lần và vẫn là chợ đa gian hàng đúng nghĩa. Mình nói thẳng vậy dù bán cả hai.

## Nếu bạn chọn nền tảng cho thuê shop

Đây là phần người ta đánh giá thấp, vì cái nhìn thấy được — giao diện cửa hàng — lại là phần dễ nhất. Dưới đây là thứ nằm bên dưới.

**Tách dữ liệu giữa các shop.** Shop A tuyệt đối không được thấy đơn hàng của shop B. Một câu truy vấn thiếu điều kiện lọc là rò rỉ dữ liệu. Và không chỉ cơ sở dữ liệu: file tải lên, cache, job trong hàng đợi đều phải tách. Hai shop cùng đặt tên logo là `logo.png` mà không tách thư mục thì đè nhau.

**Khởi tạo shop tự động.** Khách bấm đăng ký, hệ thống phải tạo cơ sở dữ liệu, chạy migration, tạo dữ liệu mẫu, tạo tài khoản quản trị, gắn tên miền phụ, gửi email — đáng tin cậy, và không bắt người ta ngồi chờ hai phút.

**Thu tiền thuê bao.** Gói cước, dùng thử, nâng hạ gói, thẻ lỗi, nhắc nợ, thời gian ân hạn. Thứ bạn thật sự phải xây là một máy trạng thái cho vòng đời của shop. Và ở Việt Nam, bạn còn cần đường **chuyển khoản ngân hàng**: xuất hoá đơn, đối soát tay, bật shop bằng tay. Phần lớn khách Việt trả theo cách đó.

**Tên miền riêng.** Người bán chán `shop-cua-toi.nentang.vn` nhanh hơn bạn tưởng. Phải xác minh quyền sở hữu tên miền, xin chứng chỉ SSL, và định tuyến một tên miền lạ về đúng shop.

**Bảng điều khiển của bạn.** Bao nhiêu shop đang sống, doanh thu định kỳ bao nhiêu, ai sắp hết hạn, shop nào tạo lỗi. Và một cách đăng nhập hộ khách để hỗ trợ mà không phải hỏi mật khẩu.

Năm nhóm việc, mỗi nhóm vài tuần. Và chưa nhóm nào là phần thương mại điện tử — sản phẩm, giỏ hàng, thanh toán, vận chuyển, thuế — thứ duy nhất khách hàng của bạn thật sự đánh giá.

## Ecommerce SaaS: bộ mã nguồn cho mô hình thứ ba

[Ecommerce SaaS](https://marketplace.botble.com/ecommerce-saas) là toàn bộ danh sách trên, đã làm sẵn. Laravel 13, PHP 8.3+, mỗi shop một cơ sở dữ liệu MySQL riêng, file tách theo shop, cache có tiền tố riêng. Thanh toán qua Stripe, kèm đường offline cho chuyển khoản. 20 giao diện cửa hàng, tên miền riêng có xác minh, bảng điều khiển vận hành, API và webhook.

**Nói trước phần không đẹp: đây là sản phẩm mới.** Trên CodeCanyon nó mới có **8 lượt bán** và chưa có đánh giá nào. Mua nó là làm khách hàng sớm. Có người thấy chấp nhận được ở mức giá này, có người không, cả hai đều hợp lý. Mình để con số đó ở đây thay vì giấu xuống cuối.

**Giá: $69, mua trực tiếp $48.30** — khoảng 1,2 triệu ở tỷ giá 25.000đ. Trả một lần, không thuê bao.

## Chi phí thật: máy chủ, và nó không phải hosting rẻ tiền

Đây là phần quyết định mô hình này có khả thi với bạn không, nên mình để trước phần mua bán.

| Cần gì | Vì sao | Hosting chia sẻ có không |
|---|---|---|
| MySQL có quyền `CREATE`/`DROP DATABASE` | mỗi shop là một cơ sở dữ liệu | gần như không bao giờ |
| DNS wildcard và SSL wildcard | mỗi shop có tên miền phụ ngay lập tức | hiếm |
| Worker hàng đợi, hoặc khởi tạo đồng bộ | tạo shop là việc nặng | không chạy tiến trình dài |
| Cron chạy các lệnh định kỳ | thu tiền, đo dung lượng, email vòng đời, kiểm tên miền | thường chỉ một cron |

**Một VPS là mức sàn thực tế.** Nếu kế hoạch của bạn là tải lên cPanel thì đây là sản phẩm sai, và mình thà bạn biết điều đó ở đây còn hơn sau khi trả tiền.

## Mua trực tiếp: rẻ hơn 30% và chuyển khoản được

Mua từ [marketplace.botble.com](https://marketplace.botble.com) rẻ hơn CodeCanyon 30%, vì đi thẳng thì không mất phần chia cho sàn.

Quan trọng hơn với người Việt: **trả được bằng chuyển khoản ngân hàng VND, không cần thẻ quốc tế.** Nhiều người mua trên CodeCanyon phải nhờ người khác quẹt thẻ hộ, hoặc mua qua trung gian và trả thêm phí. Không cần như vậy.

Cùng một mã nguồn, cùng một tác giả, cùng giấy phép, cùng cập nhật trọn đời và sáu tháng hỗ trợ.

| Sản phẩm | Mô hình | CodeCanyon | Mua trực tiếp |
|---|---|---:|---:|
| Shofy | Chợ đa gian hàng | $59 | **$41.30** |
| MartFury | Chợ đa gian hàng | $349 | **$244.30** |
| Ecommerce SaaS | Cho thuê shop | $69 | **$48.30** |

Quy đổi VND chỉ để tham khảo, ở tỷ giá 25.000đ/USD. Giá niêm yết là USD.

## FAQ

### Đa gian hàng và cho thuê shop khác nhau chỗ nào?

Chợ đa gian hàng là nhiều người bán trên **một** website, bạn ăn hoa hồng mỗi đơn, khách hàng cuối là của bạn — giống Shopee. Nền tảng cho thuê shop là mỗi người bán có **website riêng** với tên miền riêng, bạn thu tiền thuê bao hàng tháng, khách hàng cuối là của họ — giống Haravan hay Shopify.

### Tôi muốn làm web giống Shopee thì mua gì?

Shofy ($41.30 mua trực tiếp) hoặc MartFury ($244.30). Cả hai đều là mã nguồn Laravel cho chợ đa gian hàng, có đăng ký người bán, duyệt sản phẩm, hoa hồng và rút tiền.

### Tôi muốn làm web giống Haravan thì mua gì?

Ecommerce SaaS ($48.30 mua trực tiếp). Nó tạo cho mỗi khách một website riêng với cơ sở dữ liệu riêng, kèm hệ thống gói cước và thu tiền thuê bao.

### Có chạy được trên hosting chia sẻ không?

Chợ đa gian hàng thì thường được. Nền tảng cho thuê shop thì gần như không, vì nó cần quyền tạo và xoá cơ sở dữ liệu, DNS wildcard và SSL wildcard. Hãy tính một VPS.

### Trả tiền bằng chuyển khoản ngân hàng được không?

Được. Mua trực tiếp tại marketplace.botble.com trả được bằng chuyển khoản VND, không cần thẻ quốc tế, và vẫn rẻ hơn CodeCanyon 30%.

### Có sẵn tiếng Việt không?

Có, cả ba sản phẩm đều có file ngôn ngữ tiếng Việt và cấu hình được VND làm tiền tệ. Bạn nên mở bản demo và chuyển sang tiếng Việt để tự kiểm tra trước khi mua.

### Mua một lần hay trả hàng tháng?

Trả một lần. Không có thuê bao. Giá đã gồm sáu tháng hỗ trợ và cập nhật trọn đời, dùng cho một tên miền chạy thật.

### Ecommerce SaaS mới quá thì có nên mua không?

Nó mới có 8 lượt bán trên CodeCanyon và chưa có đánh giá. Nếu bạn cần một sản phẩm đã được nhiều người dùng kiểm chứng thì nên cân nhắc. Nếu bạn ổn với việc là khách hàng sớm ở mức giá này thì cứ mở demo và tự đánh giá mã nguồn.

## Nên làm gì tiếp

- Xem thử [Ecommerce SaaS bản chạy thật](https://saas.botble.com), bảng điều khiển vận hành để mở
- [Shofy](https://marketplace.botble.com/portfolio/shofy) và [MartFury](https://marketplace.botble.com/portfolio/martfury) nếu bạn chọn mô hình chợ
- Bản tiếng Anh đầy đủ hơn về mặt kỹ thuật: [build your own Shopify alternative](https://botble.com/build-your-own-shopify-alternative-a-self-hosted-multi-tenant-store-platform-on-laravel)
- [Source code website bất động sản Laravel](https://botble.com/source-code-website-bat-dong-san-laravel-homzen-mua-truc-tiep-bang-chuyen-khoan-vnd), nếu bạn đang tìm mảng bất động sản

Làm một việc trước khi mua bất cứ thứ gì: viết ra một câu, rằng một năm nữa bạn sẽ gửi hoá đơn cho ai, và vì cái gì. Câu trả lời đó chọn sản phẩm giúp bạn, nhanh hơn mọi bảng so sánh, kể cả bảng trong bài này.
