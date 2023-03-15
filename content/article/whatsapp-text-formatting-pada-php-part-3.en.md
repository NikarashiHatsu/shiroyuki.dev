---
title: "How to Implement WhatsApp Text Formatting + Unicode Rendering in PHP (Part 3)"
date: 2023-03-16T00:22:00+07:00
tags: ["php", "laravel", "tutorial", "regex"]
author: "Aghits Nidallah"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "Implement Bold, Italic, Underline, Strikethrough, Monospace, and render Unicode based on WhatsApp messages in PHP"
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
    image: "/images/article/whatsapp-text-formatting-pada-php/thumbnail-part3.en.png" # image path/url
    alt: "Thumbnail" # alt text
    caption: "WhatsApp" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/NikarashiHatsu/shiroyuki.dev/tree/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

![Thumbnail](/images/article/whatsapp-text-formatting-pada-php/thumbnail-part3.en.png)

Welcome to Part 3 of this tutorial. We'll continue on about converting UTF-16
and UTF-32 to the UTF-8 HTML Entity form that browser can read.

### 3. Unicode UTF-16 dan UTF-32
As a reminder, I managed to find the proper RegEx pattern for UTF-16 and UTF-32
which is `/(\\\(u|U)[a-fA-F0-9]{4,8})/`. The next problem is how to convert the
unicode code below into HTML Entity:

![Screenshot RegEx Fix](/images/article/whatsapp-text-formatting-pada-php/ss9.png)

Using the `preg_replace()` function directly like this won't work because we're
just adding a `#&x` at the beginning and a `;` at the end.
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

Totally wrong because if you look at it, the result will be like this:

![Salah total](/images/article/whatsapp-text-formatting-pada-php/ss10.png)

Dead end. I'm stuck. Can't think any further. I got up from the workspace, took
a little walk, sipped my slightly cold coffee, took a breath, then continued
coding again.

The problem above made me think "the pattern that I created must find the words
that have been specified, then I remove the `\u` for each of those words, then
add `#&x` at the beginning and `;` at the end".

After doing some browsing for a while, I came across a pretty cool function,
`preg_replace_callback()`. Immediately I tried to implement it to
`WhatsAppService`, the result is as follows:

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

Guess what happened? BIG SUCCESS!

![SUKSES BESAR](/images/article/whatsapp-text-formatting-pada-php/ss11.png)

I can rest for a while. Finished cold coffee. Then lie down, straighten my back.
It doesn't stop there, there is one more problem that has not been resolved, namely

### 4. Links that can't be clicked like WhatsApp
Look at the message above, then look at the YouTube link, it's still in plain
text form, and can't be clicked. It's simple, the method is the same as [_formatting_ message in Part 2](../whatsapp-text-formatting-pada-php-part-2##2-formatting-that-doesnt-render-immediately),
directly wrap "Link Pattern" in an `<a>` tag.

Since it was late at night, and there were lots of RegEx cases for a link, I was
browsing and found the following RegEx pattern on [StackOverflow](https://stackoverflow.com/questions/3809401/what-is-a-good-regular-expression-to-match-a-url), here is the pattern:

`/(http(s)?:\/\/.)?(www\.)?[-a-zA-Z0-9@:%._\+~#\=]{2,256}\.[a-z]{2,6}\b([-a-zA-Z0-9@:%_\+.~#?&\/\=]*)/`.
A little explanation:
- `/(http(s)?:\/\/.)?(www\.)` finds out if the message starts with `http://`,
    `https://`, or directly `www.`.
- `(www\.)?[-a-zA-Z0-9@:%._\+~#\=]{2,256}\.[a-z]{2,6}` looks for the pattern
    `www.` followed by a valid URL character with a range of 2 to 256 characters.
    Followed by `tld` which has a range of 2 to 6 characters.
- `\b([-a-zA-Z0-9@:%_\+.~#?&\/\=]*)/` searches for valid URL patterns.

The result of the whole code is as follows:

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

...and ta-da! The result is like this:

![All Done](/images/article/whatsapp-text-formatting-pada-php/ss12.png)

## Kesimpulan
Regular expressions or RegEx, in my opinion is a very powerful and potential
aspect of programming. RegEx can help you guys solving very specific problems
like the one I'm facing right now.

Despite the difficulty of learning RegEx, I am freed from the possibility that
I will write hundreds of lines of code to create a Formatter for WhatsApp
messages. In addition, I also escaped the possibility of a long development
time due to my ignorance.

The point is, learn and keep learning. We will not know if this knowledge is
useful or not until we can solve a problem with the knowledge we get.

You can get the complete code in [my repository](https://github.com/NikarashiHatsu/whatsapp-message-formatter-php). Thank you so much for reading 👋!

`#StayCode` `#CoffeeIsMyInspiration` `#Coffee24/7`.

---

Thumbnail by <a href="https://unsplash.com/@asterfolio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Asterfolio</a> on <a href="https://unsplash.com/wallpapers/apps/whatsapp?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
