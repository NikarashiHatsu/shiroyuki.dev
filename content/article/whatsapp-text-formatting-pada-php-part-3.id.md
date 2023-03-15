---
title: "Cara Mengimplementasikan WhatsApp Text Formatting + Unicode Rendering Pada PHP (Part 3 - Akhir)"
date: 2023-03-16T00:22:00+07:00
tags: ["php", "laravel", "tutorial", "regex"]
author: "Aghits Nidallah"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "Mengimplementasikan Bold, Italic, Underline, Strikethrough, Monospace, dan merender Emoji berdasarkan pesan WhatsApp pada PHP"
canonicalURL: ""
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "/images/article/whatsapp-text-formatting-pada-php/thumbnail-part3.id.png" # image path/url
    alt: "Thumbnail" # alt text
    caption: "WhatsApp" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/NikarashiHatsu/shiroyuki.dev/tree/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

![Thumbnail](/images/article/whatsapp-text-formatting-pada-php/thumbnail-part3.id.png)

Selamat datang di Part 3 dari tutorial ini. Kita lanjut membahas tentang
mengubah UTF-16 dan UTF-32 ke bentuk UTF-8 _HTML Entity_ yang bisa dibaca oleh
_browser_.

### 3. Unicode UTF-16 dan UTF-32
Sebagai pengingat, saya berhasil menemukan pola RegEx yang tepat untuk pola
UTF-16 dan UTF-32 yaitu `/(\\\(u|U)[a-fA-F0-9]{4,8})/`. Masalah selanjutnya
adalah bagaimana cara mengubah kode unicode di bawah menjadi HTML Entity:

![Screenshot RegEx Fix](/images/article/whatsapp-text-formatting-pada-php/ss9.png)

Menggunakan fungsi `preg_replace()` secara langsung seperti ini tidak akan
berhasil karena kita hanya menambah `#&x` di awal dan `;` di akhir.
```php
<?php

namespace App\Services;

class WhatsAppService
{
    public static function format_message(string $raw_message): string
    {
        $nl2br_message = nl2br($raw_message);
        $bold = preg_replace('/\*(.*?)\*/', '<b>$1</b>', $nl2br_message);
        $italic = preg_replace('/\_(.*?)\_/', '<i>$1</i>', $bold);
        $strikethrough = preg_replace('/\~(.*?)\~/', '<strike>$1</strike>', $italic);
        $monospace = preg_replace('/\```(.*?)\```/', '<code>$1</code>', $strikethrough);
        $unicode = preg_replace('/(\\\(u|U)[a-fA-F0-9]{4,8})/', '#&x$1;', $monospace);

        return $unicode;
    }
}
```

Salah total karena jika dilihat, hasilnya akan seperti ini:

![Salah total](/images/article/whatsapp-text-formatting-pada-php/ss10.png)

_Dead end._ Saya _stuck_. Tidak bisa berpikir lebih jauh. Saya bangkit dari
_workspace_, jalan-jalan sedikit, nyeruput kopi yang sudah agak dingin, ambil
nafas, lalu lanjut ngoding lagi.

Permasalahan di atas membuat saya berpikir "pola yang saya buat harus menemukan
kata-kata yang sudah dispesifikasikan, lalu setiap kata-kata itu saya hapus
`\u`-nya, lalu tambahkan `#&x` di awal dan `;` di akhir".

Setelah _browsing_ sejenak, saya menemukan fungsi ajaib, `preg_replace_callback()`.
Langsung saya coba implementasikan ke `WhatsAppService`, hasilnya adalah sebagai
berikut:
```php
<?php

namespace App\Services;

class WhatsAppService
{
    public static function format_message(string $raw_message): string
    {
        $nl2br_message = nl2br($raw_message);
        $bold = preg_replace('/\*(.*?)\*/', '<b>$1</b>', $nl2br_message);
        $italic = preg_replace('/\_(.*?)\_/', '<i>$1</i>', $bold);
        $strikethrough = preg_replace('/\~(.*?)\~/', '<strike>$1</strike>', $italic);
        $monospace = preg_replace('/\```(.*?)\```/', '<code>$1</code>', $strikethrough);
        $unicode = preg_replace_callback(
            ['/(\\\(u|U)[a-fA-F0-9]{4,8})/'],
            function ($matches) {
                $code = preg_replace('/\\\u|\\\U/', '', $matches[0]);
                return "&#x$code;";
            },
            $monospace
        );

        return $unicode;
    }
}
```

Tebak apa yang terjadi? SUKSES BESAR!

![SUKSES BESAR](/images/article/whatsapp-text-formatting-pada-php/ss11.png)

Saya bisa istirahat untuk sejenak. Menghabiskan kopi yang sudah dingin. Lalu
rebahan, meluruskan punggung. Tidak berhenti sampai situ, ada 1 masalah lagi
yang belum diselesaikan yaitu

### 4. Link yang tidak bisa diklik layaknya WhatsApp
Lihat pesan di atas, lalu lihat pada link YouTube-nya, masih berbentuk
_plain text_, dan tidak bisa diklik. Simpel saja, caranya sama seperti
[_formatting_ pesan pada Part 2](../whatsapp-text-formatting-pada-php-part-2#2-formatting-yang-tidak-ter-render-secara-langsung),
langsung _wrap_ "Pola Link" pada sebuah tag `<a>`.

Karena waktu sudah larut malam, dan banyak sekali kasus RegEx untuk sebuah _link_,
maka saya _browsing_ dan menemukan pola RegEx berikut pada [StackOverflow](https://stackoverflow.com/questions/3809401/what-is-a-good-regular-expression-to-match-a-url), berikut adalah polanya:

`/(http(s)?:\/\/.)?(www\.)?[-a-zA-Z0-9@:%._\+~#\=]{2,256}\.[a-z]{2,6}\b([-a-zA-Z0-9@:%_\+.~#?&\/\=]*)/`.
Sedikit penjelasan:
- `/(http(s)?:\/\/.)?(www\.)` mencari apakah pesan tersebut dimulai dengan `http://`,
   `https://`, atau langsung `www.`.
- `(www\.)?[-a-zA-Z0-9@:%._\+~#\=]{2,256}\.[a-z]{2,6}` mencari apakah pola `www.`
   dilanjutkan dengan karakter URL yang valid dengan rentang 2 sampai dengan 256
   karakter. Dilanjutkan dengan `tld` yang memiliki rentang 2 sampai dengan 6
   karakter.
- `\b([-a-zA-Z0-9@:%_\+.~#?&\/\=]*)/` mencari pola URL yang valid.

Hasil dari keseluruhan kode adalah sebagai berikut:

```php
<?php

namespace App\Services;

class WhatsAppService
{
    public static function format_message(string $raw_message): string
    {
        $nl2br_message = nl2br($raw_message);
        $bold = preg_replace('/\*(.*?)\*/', '<b>$1</b>', $nl2br_message);
        $italic = preg_replace('/\_(.*?)\_/', '<i>$1</i>', $bold);
        $strikethrough = preg_replace('/\~(.*?)\~/', '<strike>$1</strike>', $italic);
        $monospace = preg_replace('/\```(.*?)\```/', '<code>$1</code>', $strikethrough);

        $url = preg_replace(
            '/(http(s)?:\/\/.)?(www\.)?[-a-zA-Z0-9@:%._\+~#\=]{2,256}\.[a-z]{2,6}\b([-a-zA-Z0-9@:%_\+.~#?&\/\=]*)/',
            '<a class="text-blue-500" href="$0" target="_blank">$0</a>',
            $monospace
        );

        $unicode = preg_replace_callback(
            ['/(\\\(u|U)[a-fA-F0-9]{4,8})/'],
            function ($matches) {
                $code = preg_replace('/\\\u|\\\U/', '', $matches[0]);
                return "&#x$code;";
            },
            $url
        );

        return $unicode;
    }
}
```

...dan _ta-da_! Hasilnya adalah seperti ini:

![All Done](/images/article/whatsapp-text-formatting-pada-php/ss12.png)

## Kesimpulan
Ekspresi Regular atau RegEx, menurut saya adalah sebuah aspek dalam pemrograman
yang sangat kuat dan potensial. RegEx dapat membantu teman-teman dalam
memecahkan masalah yang sangat spesifik seperti yang saya hadapi sekarang.

Terlepas dari sulitnya belajar RegEx, saya terbebas dari kemungkinan bahwa
saya akan menulis ratusan baris kode demi membuat sebuah _Formatter_ untuk
pesan WhatsApp. Selain itu, saya juga terlepas dari kemungkinan waktu
pengembangan yang panjang karena ketidaktahuan saya.

Intinya, belajar dan terus belajar. Kita tidak akan tahu ilmu tersebut berguna
atau tidak sampai kita bisa menyelesaikan suatu permasalahan dengan ilmu yang
kita dapatkan.

Kode lengkap bisa Anda dapatkan pada [repository saya](https://github.com/NikarashiHatsu/whatsapp-message-formatter-php). Terima kasih banyak sudah membaca 👋!

`#StayCode` `#CoffeeIsMyInspiration` `#Coffee24/7`.

---

Thumbnail oleh <a href="https://unsplash.com/@asterfolio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Asterfolio</a> dari <a href="https://unsplash.com/wallpapers/apps/whatsapp?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>