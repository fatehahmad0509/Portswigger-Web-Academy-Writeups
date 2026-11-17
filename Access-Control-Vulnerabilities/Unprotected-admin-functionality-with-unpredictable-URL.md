# Lab: Unprotected admin functionality with unpredictable URL

**Difficulty:** Apprentice  
**Vulnerability:** Unprotected Admin Functionality

## Solution

İlk olarak gizli admin panelinin URL adresini bulmak için uygulamanın arayüzünde gezindim. Giriş bölümünde hata tetiklemeyi de denedim ancak herhangi bir sonuç elde edemedim.

Bunun üzerine sayfanın kaynak kodunu incelemeye karar verdim.

Kaynak kodunda aşağıdaki JavaScript bölümünü buldum:

```javascript
<script>
var isAdmin = false;
if (isAdmin) {
   var topLinksTag = document.getElementsByClassName("top-links")[0];
   var adminPanelTag = document.createElement('a');
   adminPanelTag.setAttribute('href', '/admin-nzkq17');
   adminPanelTag.innerText = 'Admin panel';
   topLinksTag.append(adminPanelTag);
   var pTag = document.createElement('p');
   pTag.innerText = '|';
   topLinksTag.appendChild(pTag);
}
</script>
```

Kod içerisinde `isAdmin` değişkeninin `false` olarak ayarlandığını ve admin paneli bağlantısının `/admin-nzkq17` olduğunu gördüm.

Bu nedenle admin panelinin URL adresinin şu olduğunu tespit ettim:

`/admin-nzkq17`

Ardından bu adrese doğrudan erişmeyi denedim. Admin paneli başarıyla açıldı.

Son olarak `carlos` kullanıcısını sildim ve labı başarıyla tamamladım.

## What I Learned

- Sayfa kaynağındaki JavaScript kodları hassas bilgileri açığa çıkarabilir.
- Gizli bir URL kullanmak, admin panelini yetkisiz erişime karşı korumaz.
- Yönetici panellerinde mutlaka sunucu taraflı yetkilendirme kontrolü yapılmalıdır.
