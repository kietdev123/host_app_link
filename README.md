# 🔗 Android App Links với GitHub Pages

Hướng dẫn chi tiết cách host file `assetlinks.json` cho Android App Links sử dụng GitHub Pages.

## 📋 Mục lục

- [Giới thiệu](#giới-thiệu)
- [Cấu hình GitHub Pages](#cấu-hình-github-pages)
- [Thiết lập App Links](#thiết-lập-app-links)
- [Testing](#testing)
- [Troubleshooting](#troubleshooting)

## 🎯 Giới thiệu

Android App Links cho phép website mở trực tiếp app Android tương ứng thay vì mở trong browser. Điều này tạo ra trải nghiệm mượt mà hơn cho người dùng.

## ⚙️ Cấu hình GitHub Pages

### Bước 1: Enable GitHub Pages

1. Vào repository settings
2. Scroll xuống phần "Pages"
3. Chọn source: "Deploy from a branch"
4. Chọn branch: `main` hoặc `master`
5. Folder: `/ (root)`
6. Click "Save"

### Bước 2: Kiểm tra domain

Sau khi enable, domain của bạn sẽ là:
```
https://[username].github.io/[repository-name]/
```

## 🔧 Thiết lập App Links

### Bước 1: Cập nhật assetlinks.json

Mở file `.well-known/assetlinks.json` và cập nhật:

```json
[
  {
    "relation": ["delegate_permission/common.handle_all_urls"],
    "target": {
      "namespace": "android_app",
      "package_name": "YOUR_PACKAGE_NAME",
      "sha256_cert_fingerprints": [
        "YOUR_SHA256_FINGERPRINT"
      ]
    }
  }
]
```

### Bước 2: Lấy SHA256 Fingerprint

#### Cho debug build:
```bash
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

#### Cho release build:
```bash
keytool -list -v -keystore your-release-key.keystore
```

#### Từ Google Play Console:
1. Vào App signing
2. Copy SHA-256 certificate fingerprint

### Bước 3: Cấu hình Android App

Thêm intent filter vào `AndroidManifest.xml`:

```xml
<activity
    android:name=".MainActivity"
    android:exported="true">
    
    <!-- Intent filter cho App Links -->
    <intent-filter android:autoVerify="true">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="https"
              android:host="yourdomain.github.io" />
    </intent-filter>
    
</activity>
```

## 🧪 Testing

### 1. Kiểm tra file assetlinks.json

Truy cập: `https://yourdomain.github.io/.well-known/assetlinks.json`

### 2. Google Digital Asset Links Tool

Sử dụng tool của Google để verify:
https://developers.google.com/digital-asset-links/tools/generator

### 3. Test trên device

```bash
adb shell am start \
    -W -a android.intent.action.VIEW \
    -d "https://yourdomain.github.io/app/test" \
    com.yourpackage.name
```

### 4. Chrome Intent Test

Mở Chrome trên Android và truy cập:
```
intent://yourdomain.github.io/app/test#Intent;scheme=https;package=com.yourpackage.name;end
```

## 🔍 Troubleshooting

### Lỗi thường gặp:

1. **File assetlinks.json không accessible**
   - Kiểm tra GitHub Pages đã enable chưa
   - Verify đường dẫn: `/.well-known/assetlinks.json`

2. **App không mở**
   - Kiểm tra SHA256 fingerprint
   - Verify package name
   - Kiểm tra intent filter trong AndroidManifest.xml

3. **Domain verification failed**
   - Đảm bảo `android:autoVerify="true"`
   - Kiểm tra assetlinks.json format
   - Wait 24h sau khi upload app lên Play Store

### Debug commands:

```bash
# Kiểm tra app links verification
adb shell dumpsys package domain-preferred-apps

# Reset app links verification
adb shell pm set-app-links --user 0 com.yourpackage.name 2 yourdomain.github.io
```

## 📱 Example URLs

Các URL pattern có thể handle:

- `https://yourdomain.github.io/app/product/123`
- `https://yourdomain.github.io/app/user/profile`
- `https://yourdomain.github.io/app/category/electronics`

## 📚 Tài liệu tham khảo

- [Android App Links Official Guide](https://developer.android.com/training/app-links)
- [Digital Asset Links](https://developers.google.com/digital-asset-links)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)

---

## 🤝 Đóng góp

Nếu bạn tìm thấy lỗi hoặc muốn cải thiện hướng dẫn, hãy tạo issue hoặc pull request! 