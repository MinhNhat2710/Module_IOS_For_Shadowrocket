# Module iOS cho Shadowrocket — bản tự host

Fork đã audit của `dinhduyvinh09/AppPre` (trang gốc: module-ios.netlify.app).
Mọi script, config, mobileconfig đều được host từ repo này — không phụ thuộc
vào repo/domain/DNS của tác giả gốc.

## Cấu trúc

| Đường dẫn | Vai trò |
|---|---|
| `index.html` | Trang hướng dẫn + nút cài đặt (deploy bằng GitHub Pages) |
| `All_In_One.conf` / `.sgmodule` | Config + module tổng hợp |
| `js/` | Toàn bộ script chạy local trong Shadowrocket |
| `modules/` | Các module lẻ từng app |
| `assets/` | Ảnh hướng dẫn + 2 file `.mobileconfig` (DNS profile) |

## Triển khai (không cần domain, tồn tại lâu dài)

1. Push repo này lên GitHub của bạn (đã làm sẵn).
2. Bật GitHub Pages: **Settings → Pages → Source: Deploy from branch → main /(root)**.
   Trang sẽ có tại `https://<username>.github.io/Module_IOS_For_Shadowrocket/` —
   miễn phí, không hết hạn.
3. Module/config cài trực tiếp từ `raw.githubusercontent.com` — tồn tại theo
   repo, không phụ thuộc jsdelivr hay domain riêng.

## Việc cần làm MỘT LẦN trước khi dùng

### 1. Tạo NextDNS profile của riêng bạn (2 phút, free)

Hai file `assets/*.mobileconfig` cài DNS-over-HTTPS để chặn domain
analytics/thu hồi — bản gốc trỏ về NextDNS profile **của tác giả** (họ thấy
toàn bộ DNS query của bạn). Thay bằng của bạn:

1. Vào https://nextdns.io → đăng ký (free 300k query/tháng).
2. Vào tab **Denylist**, thêm các domain:
   `in.appcenter.ms`, `app-measurement.com`, `firebaselogging-pa.googleapis.com`,
   `mixpanel.com`, `api2.amplitude.com`, `appsflyersdk.com`, `ingest.sentry.io`,
   `crashlytics.com`
3. Copy DoH URL dạng `https://dns.nextdns.io/abcdef` trong tab Setup/Endpoints.
4. Mở `assets/LocketGold.mobileconfig` và `assets/locket.mobileconfig`, thay
   `https://dns.nextdns.io/CHANGE_ME` bằng URL đó rồi commit.

> Nếu lười: có thể bỏ qua hoàn toàn 2 mobileconfig — `All_In_One` đã có rule
> `REJECT` các domain trên, miễn là Shadowrocket VPN luôn bật.

### 2. Chứng chỉ MITM

`shadowrocket_basic.conf` bản gốc nhúng sẵn CA private key **dùng chung** —
đã xoá. Trong Shadowrocket: bấm config → **Giải mã HTTPS → Tạo chứng chỉ CA
mới** → cài & trust trong Settings như hướng dẫn trên trang. Mỗi máy một CA
riêng, không lộ key ra ngoài.

## Đã sửa so với bản gốc

- Repoint toàn bộ `script-path`, deep link `shadowrocket://install`, icon,
  homepage về repo này (bản gốc quảng cáo link mirror nhưng nút cài thực tế
  trỏ về repo tác giả).
- 4 module lẻ (Camera360, Snow, SODA, BeautyPlus) trước kéo script từ repo
  `ohoang7`, `chxm1023`, `vuong2023` → giờ dùng `js/` nội bộ.
- `assets/*.mobileconfig`: thay DoH của tác giả (`my.ublockdns.com/z451ak95`,
  `api.p12.vn`) bằng placeholder NextDNS của bạn.
- `shadowrocket_basic.conf`: xoá `ca-p12` + `ca-passphrase` nhúng sẵn.
- `[Rule]` thêm REJECT các domain analytics/telemetry.
- Sửa hostname MITM bị `, ,`.

## Lưu ý an toàn

- MITM chỉ decrypt các domain trong `hostname` (revenuecat, spotify, youtube,
  meitu, picsart...) — CA do Shadowrocket tự sinh trên máy bạn, traffic chỉ
  xử lý local, không gửi về đâu.
- Script chỉ sửa response trên thiết bị; đã audit: không có code gửi dữ liệu
  về server tác giả.
- Sub-Store (`sub.store`) là tool mã nguồn mở chạy local; script tải từ GitHub
  release chính chủ `sub-store-org`.
