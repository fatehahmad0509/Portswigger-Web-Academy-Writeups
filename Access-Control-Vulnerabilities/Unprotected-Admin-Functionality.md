# Unprotected Admin Functionality

**Difficulty:** Apprentice  
**Vulnerability:** Unprotected Admin Functionality  
**Platform:** PortSwigger Web Security Academy

## 1. Reconnaissance

İlk olarak hedef web sitesinin `robots.txt` dosyasını kontrol ettim.

Dosya içerisinde aşağıdaki içeriği buldum:

```text
User-agent: *
Disallow: /administrator-panel
```

Bu dosya, arama motoru botlarına `/administrator-panel` dizinini taramamaları gerektiğini söylüyordu.

Ancak aynı zamanda admin panelinin konumunu da açığa çıkarmış oldu.

## 2. Exploitation

`robots.txt` dosyasından öğrendiğim dizini kullanarak tarayıcı üzerinden aşağıdaki adrese gittim:

```text
/administrator-panel
```

Admin paneline herhangi bir kimlik doğrulaması yapmadan erişebildiğimi fark ettim.

Panel içerisinde kullanıcı yönetimi işlevlerini buldum.

## 3. Deleting the User

Admin panelini kullanarak `carlos` kullanıcısını sildim.

İşlem başarılı oldu ve lab tamamlandı.

## 4. Root Cause

Uygulamanın admin panelinde herhangi bir authentication veya authorization kontrolü bulunmuyordu.

Ayrıca `robots.txt` dosyası, hassas bir dizinin konumunu herkese açık şekilde belirtiyordu.

**Security Impact:** Yetkisiz kullanıcılar admin paneline erişerek yönetimsel işlemler gerçekleştirebilir.

## 5. Remediation

- Admin paneline erişim için güçlü authentication uygulanmalı.
- Her yönetimsel işlemde server-side authorization kontrolü yapılmalı.
- Hassas dizinlerin konumunu `robots.txt` üzerinden gizlemeye güvenilmemeli.
- Admin paneline erişim uygun erişim kontrolleriyle sınırlandırılmalı.
