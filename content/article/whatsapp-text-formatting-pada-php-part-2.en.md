---
title: "How to Implement WhatsApp Text Formatting + Unicode Rendering in PHP (Part 2)"
date: 2023-03-15T23:08:00+07:00
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
    image: "/images/article/whatsapp-text-formatting-pada-php/thumbnail-part2.en.png" # image path/url
    alt: "Thumbnail" # alt text
    caption: "WhatsApp" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/NikarashiHatsu/shiroyuki.dev/tree/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

![Thumbnail](/images/article/whatsapp-text-formatting-pada-php/thumbnail-part2.en.png)

Welcome to Part 2 of this tutorial. We will continue to discuss about
implementation of formatting using RegEx.

### 2. Formatting that doesn't render immediately

As a reminder, we need 4 kinds of formatting that must be implemented:
- Bold, represented by a string enclosed by asterisks ( \*string\* )
- Italic, represented by strings enclosed by underscores ( \_string\_ )
- Strikethrough, represented by a string enclosed in _tilde_ ( \~string\~ )
- Monospaced, represented by a string enclosed by three _backticks_ ( \```string\``` )

In RegEx, we must be able to translate an existing pattern into the RegEx
language. As the example below is a RegEx pattern that is used to get words or
sentences enclosed by asterisks, we will use this RegEx for _formatting_ **bold**:

`/\*(.*?)\*/` - Regular Expression Pattern for _formatting_ **bold** on WhatsApp.
Believe it or not, it's a valid pattern. How to read it? I will explain briefly.

- The `/` slashes at the start and end of the RegEx pattern are called `Delimiter`.
  This `delimiter` functions to limit the scope of the RegEx pattern to be read;
- `\*` after the initial slash and before the final slash indicates that there is
  an "asterisk" symbol at the beginning and at the end of a word or sentence;
- `()` in brackets serves as a "group" marker. Groups in RegEx function to create
  a "capture group" to extract a substring;
- `.` in RegEx PHP means to search for all characters, whether they are strings,
  integers or symbols, except for `line breaks`;
- `*` means match 0 or more characters in the pattern; as well as - `?` is lazy,
  which means the RegEx will search for as few characters as possible. By default,
  RegEx's quantifier is greedy that looks for as many characters as possible.

Confusing, right 😅? Indeed it is. However, here is the RegEx list I managed to
compile for formatting WhatsApp messages:

| Format type | RegEx Pattern |
|---|---|
| **Bold** | `/\*(.*?)\*/` |
| _Italic_ | `/\_(.*?)\_/` |
| ~~Strikethrough~~ | `/\~(.*?)\~/` |
| `Monospace` | `/\```(.*?)\```/` |

After getting these patterns, I implement them into the `WhatsAppService`:

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

        return $monospace;
    }
}
```

We'll see the results in the application

![Screenshot pesan setelah kami menerapkan RegEx formatting](/images/article/whatsapp-text-formatting-pada-php/ss4.png)

...aaaaaaaand it worked. An achievement that I think is extraordinarily cool.
A step for a more perfect application.

### 3. Unicode UTF-16 dan UTF-32
Unicode, which I reported from Wikipedia, is a technical standard designed to
allow text and symbols from all writing systems in the world to be displayed and
manipulated. However, not all Unicode sent can be parsed and rendered just like
that, there must be another conversion stage so that the desired characters can
be displayed.

UTF-8 is the encoding standard used on most websites and browsers. In general,
browsers and search engines will fail to process UTF-16, therefore UTF-16 is not
used in website technologies.

Okay, let's get straight to the main problem. An example of a case like this is
given:

![Screenshot permasalahan Unicode](/images/article/whatsapp-text-formatting-pada-php/ss5.png)

Based on previous experience, I thought of the following scenario:
1. Remove `\u` and `\U` in the plain text; then
1. Display the resulting text from the unicode into an HTML form by wrapping the
  result of replacing the result with `&#$1;`

Initially, I used RegEx as follows `/(\\\(u|U)[a-zA-Z0-9])/`, but after I tried
to check how this pattern works on [Regexr.com](https://regexr.com), apparently
there was an error in the pattern I made:

![Screenshot kesalahan pola](/images/article/whatsapp-text-formatting-pada-php/ss6.png)

Since at first I thought it was an emoji, I grabbed one of the random unicode
`0001f64f` and tried to google it. Here is the _screenshot_ that I took from the
[fileformat.com](https://www.fileformat.info/info/unicode/char/1f64f/index.htm) page.

![Screenshot fileformat](/images/article/whatsapp-text-formatting-pada-php/ss7.png)

"Sure enough, all I got were emojis!" I said to myself. After successfully
identifying the unicode as an emoji converted from UTF-32, I have to convert it
to UTF-8, or directly as Hexadecimal HTML Entity. I also changed the RegEx
pattern that I made to be like this:

```diff
- /(\\\(u|U)[a-zA-Z0-9])/
+ /(\\\(u|U)[a-zA-Z0-9]{4,8})/
```

Yes, I just added the prerequisite that the unicode characters I have to retrieve
must be 4 to 8 characters long. But what do I get? Pattern error, again 😅.

![Screenshot kesalahan pola, lagi](/images/article/whatsapp-text-formatting-pada-php/ss8.png)

If you see the match result in the first sentence, the RegEx pattern that I made
takes `\u2728RIZK`. `RIZK` is a pattern I don't want to pick up. What's the
solution?

In short, I changed the range of letters I was looking for. Just from `a-f` and `A-F`.
```diff
- /(\\\(u|U)[a-zA-Z0-9]{4,8})/
+ /(\\\(u|U)[a-fA-F0-9]{4,8})/
```

I also managed to find a suitable RegEx pattern that can be implemented in the application:

![Screenshot RegEx Fix](/images/article/whatsapp-text-formatting-pada-php/ss9.png)

So, the "final" RegEx pattern is something like this `/(\\\(u|U)[a-fA-F0-9]{4,8})/`.
Little explanation:
- The `/` slashes at the start and end of the RegEx pattern are called `Delimiter`.
  This `delimiter` functions to limit the scope of the RegEx pattern to be read;
- `\\\` three backslash are used to escape the backslash character in PHP;
- `()` in brackets serves as a "group" marker. The RegEx group functions to create
  a "capture group" to extract a substring. The group in the RegEx above contains
  `(u|U)` which means we receive a lowercase `u` or a capital `U` after the backslash;
  followed by
- `[a-fA-F0-9]` which means we accept lowercase letters `a` through `f`, uppercase
  `A` through `F`, then numbers `0` through `9`.
- `{4,8}` means we accept any character in the previous pre-requisite `[a-fA-F0-9]`
  with a length of 4 to 8 characters.

That's the story of how I got the UTF-16 and UTF-32 patterns. Then how do I
change the pattern that has been obtained so that it can be displayed in HTML
form? We'll continue to [Part 3](../whatsapp-text-formatting-pada-php-part-3),
I'll see you there 👋!

---

Thumbnail by <a href="https://unsplash.com/@asterfolio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Asterfolio</a> on <a href="https://unsplash.com/wallpapers/apps/whatsapp?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
