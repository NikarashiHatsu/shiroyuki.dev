---
title: "4 Reality"
date: 2023-02-06T12:44:20+07:00
tags: ["portfolio", "website"]
author: "Aghits Nidallah"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "A website for a Virtual YouTuber team based on Indonesia"
canonicalURL: "https://nikarashihatsu.github.io/portfolio/website/4reality"
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
    image: "/images/portfolio/website/4reality/thumbnail.png" # image path/url
    alt: "4 Reality Thumbnail Image" # alt text
    caption: "4 Reality Homepage" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/NikarashiHatsu/shiroyuki.dev/tree/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

![4 Reality Thumbnail](/images/portfolio/website/4reality/thumbnail.png)

# Overview

4 Reality is a Virtual YouTuber team (often called VTuber) based on Indonesia.
Named 4 Reality because there are 4 founders: `Kurokami Itsuki`, `Raihan Ikeda`,
`Karen`, and `Vyula`.

Along with the emergence of the 1st Generation 4 Reality, they also
conceptualized the Utaite team (which focuses on singing cover content), also
consisting of 4 characters: `Cyanpile`, `Aura Lily`, `Yua Deyanara`, and
`Miasviel`.

This website was built using:
- [Vue.js](https://vuejs.org/) v3 - using compiler [Vite](https://vitejs.dev/) v1
- [TailwindCSS v2](https://v2.tailwindcss.com/)
- [Vercel](https://vercel.com)


# Development

The development of this website is based on my love for one of the Utaite
characters, `Aura Lily`, a beautiful and charming fairy. After exploring the 4
Reality Discord server, I met `Karen` who also manages all the assets for this
team. With the initiative to make `Aura Lily` a wallpaper, I easily obtained her
character design.

Here is the design for the `Aura Lily` wallpaper which will then become the 4
Reality website development idea:

![Aura Lily Wallpaper Phone](/images/portfolio/website/4reality/draft-aura-lily-phone.png)

![Aura Lily Wallpaper Desktop](/images/portfolio/website/4reality/draft-aura-lily-desktop.png)

I used to love the concept of the Broken Grid, but the biggest challenge I will
face in development is what I call the stylesheet chaos. A condition where there
will be many redundant stylesheets and they tend to only be used once for a
specific character. Fortunately, I found a CSS framework that can handle this,
[TailwindCSS](https://tailwindcss.com/).

One can imagine how difficult it is to create a character profile page tha
contains a lot of information such as:
- Talent name
- Type of character played
- Social media
- Motto
- Used color palette
- Additional information

After the complex design of `Aura Lily` was finished, I then thought, "What if I
develop this website again?" See the statement "develop again", this statement
appeared because the 4 Reality website already existed at that time, but was far
from adequate.

A makeshift Bootstrap 4 template, combined with raw assets that were so large
they reached 10MB per image, affecting load time, and a very simple design
(due to the template, of course).

## Profile Section Dilemma

The next challenge is not about thinking about how to design the website, but
about how I can present 8 talents on one page without having to change pages.
Vue Dynamic Component was finally used to store each talent's information. So
the website visitor simply clicks on the icon of the talent they want to see and
the details of that talent will automatically appear.

Here is the design for the Profile Section:

![Character Section Phone](/images/portfolio/website/4reality/draft-character-section-phone.png)

![Character Section Desktop](/images/portfolio/website/4reality/draft-character-section-desktop.png)

However, of course, there is information that will not be visible if the design
of the Profile Section is in the form of scrolling. There is no information
"Which part is this talent from? Is he a VTuber or an Utaite? Then what if there
is a Generation 2, Generation 3, and so on?" Of course, this is a dilemma in
itself. I have 2 options:

1. Display the character information played by the talent in the profile
   details, with the risk of the design becoming imbalanced due to the addition
   of the word "Gen 1" or "Utaite" firmly.
1. Categorizing characters based on each talent's role, by creating "Tabs".

I decided to use option 2, the easiest option to implement and not changing the
existing design. Here is the view I took from the direct website.

![Character Section Updated](/images/portfolio/website/4reality/our-team.png)

## About the rest of the parts, how is it, Tsu?

> As context, _Tsu_ here is a nickname from the word _Hatsu_ (初 / はつ).
> My nickname at the time was _HatsuShiroyuki_ (初白雪) - The first Snow White

It's a simple question that `Karen` said when I finished working on the
Character Section, a simple question but it took me a lot of brainpower to think
about how to design the `Home`, `Vision and Mission`, `Contact Us`, and `Footer`
sections.

It's really not an easy task, because the first impression I give is the chaotic
yet balanced and visually pleasing Broken Grid nuance.

### Home

The H`ome section literally just displays an overview of the entire website.
After using my brain power for quite a while only for the Home section, I sent
this home section draft to `Karen` and `Itsuki`:

![Home Section Draft](/images/portfolio/website/4reality/home-section-draft.png)

There was no rejection, but more like a suggestion:

> How about under 4 Reality Team you add these words, Tsu:
> "Together Everyone Achieves More And There is no I in Team."
>
> — <cite>Itsuki</cite>

> At the same time, at the top there is a header, and the color should not be
> too black. Grayish is also okay.
>
> — <cite>Karen</cite> 

_Challenge accepted_, Ki, Ren. After making some adjustments, here is the design
I sent back to them:

![Home Section Final](/images/portfolio/website/4reality/home-section-final.png)

Everyone agreed with the Home Section design, and then moved on to the About Us
Section.

### About Us

This section is also a challenge in itself because we want to display 1 vision
and 3 missions, while at the same time telling briefly about their team.

I came up with an idea where I would use the top sub-section for About the Team,
and the bottom sub-section for Vision and Mission. Of course, this design was
not well received, and tended to destroy the Broken Grid nuance used:

![About Section Draft](/images/portfolio/website/4reality/about-section-draft.png)

Of course, I think you as a reader are equally annoyed "why like that?".
Technically, it will damage the theme, but how else, the brain is already stuck
💀. I take time to rest my brain for a few days, and also to find inspiration
for this section.

2 days have passed, and I sent this design:

![About Section Final](/images/portfolio/website/4reality/about-section-final.png)

The concept I created for the About Us Section is as follows:

1. The upper left corner contains 'V' or 'M', which means Vision or Mission.
1. The vision or mission content is not too long.
1. The Card arrangement will be sorted down, not zig-zag on the Tablet display.
1. The About 4 Reality on the left is in a `sticky` form, so it will
   automatically `scroll` down when the user also `scrolls` down. This is the
   main reason why the left side looks empty.

The design was accepted by all members of 4 Reality, and I immediately merged the existing design and then sliced the design into code.

## Slicing
Slicing is the longest stage, thinking about how the display can match each
viewport is not an easy thing. Knowledge of responsive web design will be very
useful at this stage.

For the Home Section, it is relatively easy, it only took a few hours to make it
responsive on all viewports.

The About Us Section is also relatively easy. Display the Cards in a zig-zag on
the right on the Desktop viewport, display it in order from top to bottom on the
Tablet viewport, and remove the sticky property on the Mobile viewport.

The Character Section is a heavy challenge from all sections.

1. I had to build a unique design for each Talent. There are also uniqu
   requests from some Talents, whether they want a 2-color option, or use a
   different name direction (check the `Aura Lily` detail as an example).
1. I had to categorize each Talent into the appropriate section.
1. I had to make sure that all designs can be seen well on all viewports, and
   again, they must be responsive.

The rest of the Contact and Social Media sections are simple sections that are
not too good, but still consistent with the color theme used.

## In conclussion

So far in my career in website development, 4 Reality is the most complex
website I have ever made. But the result of this hard work is well paid off
because the 4 Reality Website became the winner in 2 Wibucode events:

1. 1st Place Front-end 2021 Wibucode event on June 14, 2021

   ![Juara 1 Event Front End 2021 Wibucode](/images/portfolio/website/4reality/event-front-end-2021.jpeg)

   ![Sertifikat Juara 1 Event Front End 2021 Wibucode](/images/portfolio/website/4reality/certificate.png)

1. 2nd Place Tailwind CSS 2021 Wibucode event on December 13, 2021

   ![Juara 2 Event Tailwind CSS 2021 Wibucode](/images/portfolio/website/4reality/event-tailwindcss-2021.jpeg)

A small, proud achievement 😬.

Thank you to all parties involved in the development of this website. Especially
`Karen` who always gives positive feedback and builds, `Itsuki` and
`Nervia (staff)` who often provide sharp and spicy criticism, and `Aura Lily`
who is the main motivation behind the development of this website.
