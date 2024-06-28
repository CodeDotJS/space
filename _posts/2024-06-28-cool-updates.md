---
layout: post
title: "Cool Page ― Start & Ending"
tags: [thoughts, personal project]
---

I really wanted to have an [update page](../updates) where I could pull real-time data from various sources and show them all in one place, but for a long time, I circled around something terrible—trying to build everything all at once and then shove it down to the throat my site. It didn't work out because I never started working on things.

One of the things that I've experienced is that when I build something and expect it to be perfect when I'm done, I don't really finish the project. But I decided to change the way I do things, especially if it's an old idea. This was my old mindset, though; things have changed since, and now I do iterations over and over until I'm satisfied. This way, I'm able to put out the work and still have the motivation to work on it because I know what I built isn't even close to perfect and will require a lot of tweaking.

I'm writing this post because I'm now satisfied with my update page and I don't wish to add any further sections. In the future, I might add my walking/running metrics, but as of now, I'm considering it finalized. Currently, my update page shows:

1. Most recent commit I pushed on GitHub >> 📌 [`/public`](https://api.github.com/users/CodeDotJS/events/public) --- <small>pulled from GitHub API. I'm only parsing JSON to display data.</small>
2. Recently played song on Spotify >> 🦄 [`/listening`](https://ir8x.vercel.app/listening)
3. Last four posts made on my Instagram account >> 👟 [`/instagram`](https://ir8x.vercel.app/instagram)
4. Book reading progress from Goodreads >> 📚 [`/reading`](https://ir8x.vercel.app/reading)
5. Articles and videos I enjoyed — pulled from Pocket >> 🍎 [`/pocket`](https://ir8x.vercel.app/pocket)
6. Writing stats — total words I've written across my blog posts --- <small>using liquid's feature to process blog posts and count words.</small>
7. Quote I liked on Goodreads >> 🐧 [`/quote`](https://ir8x.vercel.app/quote)
8. Coding stats from WakaTime >> 🗽 [`/coding`](https://ir8x.vercel.app/coding)

The information is only meaningful to me, and I built these services for my own sake. Would I like to show it to anyone else? Well, the answer is no. However, since the site is public, I can't stop anyone from seeing it. Again, it's not for anyone else but me, and I'm happy that I've finished something I wanted to build for a long, long time. It was fun, a bit challenging, but overall, very exciting.

I'm satisfied with my work, and the page looks nice, so it's a win-win despite the hours spent building and fixing things. Unfortunately, I haven't made the code public because it contains sensitive data. In future, I might templatize the services so that anyone can use them, but I'm not sure when I'll do it. The current ones are highly personalized, so I'll need to make a bunch of changes and write a long-ass README to make it easy to use. Let's see when that happens.

*Adios* :dolphin:
