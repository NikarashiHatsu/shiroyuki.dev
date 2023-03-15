---
title: "How to Implement WhatsApp Text Formatting + Unicode Rendering in PHP (Part 1)"
date: 2023-03-15T19:39:10+07:00
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
    image: "/images/article/whatsapp-text-formatting-pada-php/thumbnail-part1.en.png" # image path/url
    alt: "Thumbnail" # alt text
    caption: "WhatsApp" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/NikarashiHatsu/shiroyuki.dev/tree/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

![Thumbnail](/images/article/whatsapp-text-formatting-pada-php/thumbnail-part1.en.png)

## Background
In the development of the Dumas Presisi, an application project that I handle, implementing WhatsApp communication through the Website is one of the features that we have long wanted to implement.

However, due to our lack of experience and mastery of knowledge, after 2 years of this project being launched by the National Police Chief on Wednesday, February 24, 2021, we were only able to implement this feature as a means of community consultation.

## Prototyping
Assisted by [TailwindCSS](https://tailwindcss.com/) and [DaisyUI](https://daisyui.com/),
we can implement this feature's mockup easily. Moreover, with
DaisyUI v2.51.4 update, the addition of the Chat Bubble feature is very helpful
of creating this mockup. Here is our first _screenshot_ mockup:

![First Mockup](/images/article/whatsapp-text-formatting-pada-php/ss1.png)

Very simple right? Nothing special at the moment. As additional information,
this feature was presented the next day to the leader. Of course, we hope
that there will be input and criticism from the leader on the features we are
building.

Everything went smoothly... until something we didn't expect happened.

## Issues
Issue which we didn't expect at all because it didn't appear during testing,
appeared during the presentation. No one is "hurt" by this bug, but we need to
get this fixed as soon as possible.

>"What's the problem that makes us feel that this issue should be resolved? After all, it's only plain text that is rendered?"

Haha, on the surface, it's true that what we're rendering is plain text, but if
the plain text looks like this, is it pleasing to the eye?

![Bug pada pesan](/images/article/whatsapp-text-formatting-pada-php/ss2.png)

The Dumas Presisi WhatsApp Hotline is the same as WhatsApp numbers in general,
we accept complaints, suggestions, even random broadcasts sent by the public.
So, all messages sent to our Hotline, we will accept them as they are in the
application.

After skimming through the message above, we mapped out 4 problems:
1. Line break
1. Formatting
1. Emoji
1. Link

Let's solve this problem one by one 🔥.

### 1. Unrendered line breaks
In PHP, for plain text messages like this, it's easy for us developers
to change `New Line` to `Break Line` HTML renderable.
That function is `nl2br()`, which is short for
`New line to <br/>`.

First, I created a `Service` named `WhatsAppService`, less
more, like this file:

```php
<?php

namespace App\Services;

class WhatsAppService
{
    public static function formatMessage(string $raw_message): string
    {
        return $raw_message;
    }
}
```

The service above still can't change `New Line` to `<br/>`, so we use the `nl2br`
function so that the message we receive can be formatted properly:

```php
<?php

namespace App\Services;

class WhatsAppService
{
    public static function format_message(string $raw_message): string
    {
        $nl2br_message = nl2br($raw_message);

        return $nl2br_message;
    }
}
```

After implementing the `nl2br` function, here is the output of the message that
already formatted:

![Screenshot pesan setelah kami menerapkan fungsi nl2br](/images/article/whatsapp-text-formatting-pada-php/ss3.png)

### 2. Formatting that doesn't render immediately
Reported from page [FAQ WhatsApp](https://faq.whatsapp.com/539178204879377)
related to Text Formatting, we get 4 different kinds of formatting
must be implemented:
- Bold, represented by a string enclosed by asterisks ( \*string\* )
- Italic, represented by strings enclosed by underscores ( \_string\_ )
- Strikethrough, represented by a string enclosed in tilde ( \~string\~ )
- Monospaced, represented by a string enclosed by three backticks ( \```string\``` )

I was thinking "how to replace the symbols with
HTML tags eh?" Several methods crossed my mind.

First, I can use `str_replace()` to change the symbol to
HTML tags. But I also remember that HTML tags must be closed. Based on
given the fact, I can't simply use `str_replace()`.

Second, I can use `preg_match()` to retrieve all symbols, then
turn it into an HTML tag. Same as the case above, the replaced HTML tag
should also be closed again. In addition, the problems to be encountered at the time
i used `preg_match()` it can only return_
`int` which indicates how many symbols are found in a message.

The Approach I did using RegEx was correct, it's just that I only
need to find the right function. After standing for a while, brewing coffee, and
drinking it, I found a workaround that works, `preg_replace()`.
_Long life coffee, our source of motivation_ ☕️☕️☕️.

`preg_replace()` is a function that looks for a pattern based on
the regular expression language we give, gets words based on patterns
that we provide, then change it according to what we want.

> Regular expressions or what we often refer to as RegEx are
the most alien language I've ever learnt. Not only difficult to understand, however
also we must understand some standard rules. I personally just can understand
how RegEx works after 1 month intensive study.

Curious how I implement it? We'll continue to the
[Part 2](../whatsapp-text-formatting-pada-php-part-2)! See you there 👋.

---

Thumbnail by <a href="https://unsplash.com/@asterfolio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Asterfolio</a> on <a href="https://unsplash.com/wallpapers/apps/whatsapp?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>