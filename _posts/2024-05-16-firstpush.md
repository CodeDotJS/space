---
layout: post
title: "Logic of First Commit"
tags: [github, scraping, git, logic]
---

---

*Try it out [here.](https://firstpush.vercel.app)*
{: .center}

---

I was doomscrolling on Instagram and stumbled upon this reel where the *reeler* mentioned ---

> Apparently, it only took about a day before Git was tracking itself. That means you can go and get the official Git repository from GitHub. You can `git clone` `git`, check out the initial commits of `git`, and view the very initial commit from Linus Torvalds in 2005. He described Git as "the information manager from hell. <br><br> If we open up the Git repository and check out the README file from the first ever commit, we get to see the true meaning of "git." It's a mispronunciation of "get" – an insult, especially if you're speaking to someone from England. The full phrase is "Global Information Tracker: if you're in a good mood, and 'godamn idiotic truckload of shit' when it breaks.

I thought, why not build something that shows the first commit ever created on a GitHub repository? The idea was fairly simple, but it took around a week to finish because I started with enthusiasm and got bored after a few hours of working on it.

After much procrastination, I finally finished building [FirstPush](https://firstpush.vercel.app), which works quite nicely in most cases. It should have been a one-day or a few-hour project, but I lost my enthusiasm after figuring out the logic, which was to find the first commit made on a repository.

<img style="border-radius:10px" src="{{site.baseurl}}/assets/images/posts/firstpush.png" alt="">
{: .center}

*Attaching an old screenshot because of accidental 6969. Nice!*
{: .center}

---

<p align="center">{% include date.html custom="21 June, 2024" %}</p>

I think I started this post a month ago or something, but left it for no specific reason. So, I'm continuing from where I left off. I'd like to finish it today. No matter how many yawns I yawn.

---

## Initial Logic - Pagination
{: .center}

In order to find the first commit, you have to visit the last page of the "Commit" section of the repository and scroll down to the bottom if necessary.

- I'm going to use [react](https://github.com/facebook/react/) as an example.

To view commits made on React's repository, we can visit `https://github.com/facebook/react/commits/main/` and simply click on the "Next" button until we reach the end of the page. React has over 18,920 commits, so how many times do you need to click in order to reach the last page?

- *The answer is 541, but who's gonna do that?*

Let's move forward.

If you copy the link associated with the "Next" button, you'll get -

```
https://github.com/facebook/react/commits/main/?after=0f568418d2c9cd3d5f32eeb40d7305211bd17ccd+34
```

Two things are important here:

1. `?after=0f568418d2c9cd3d5f32eeb40d7305211bd17ccd` — This is an `oid`, which stands for object identifier. It is a unique identifier assigned to every object within a repository. It changes if new commits get added to the branch, shifting the reference point for pagination.

2. `+34` — You can't paginate simply using `?page=1` or `?page=N`, so this number and the `+` sign are what we use to move to the next page.

The plus sign can be confusing as it suggests addition, but it actually indicates the number of commits to skip after the specified SHA to start the next page. The initial commit history page `/commits/<branch>` shows 35 commits per page, and when the number of commits exceeds 35, you'll see pagination.

Let's say you have pushed 36 commits to a repository. To jump to the second page, you would follow the URL:

`/repo/commits/main?after=sha+34`

So, why `+34`? The index starts at `0`. Thus, we're skipping 35 commits and moving to the next page, which will have one more commit to show.


## Second Base
{: .center}

There are two things you can do to find the first commit created in a repository in one go:

### 1. Calculate the offset to reach the end of the commit history

- Total Commits on React's Repository: `18920`
- Commits Per Page: `35`
- __Offset__ = *Total Commits* __minus__ *Commits Per Page*
- *Which is* --- __18920 - 35__ = __`18885`__

Now, if we take the previous URL:

```
https://github.com/facebook/react/commits/main/?after=0f568418d2c9cd3d5f32eeb40d7305211bd17ccd+34
```

and replace `+34` with the new offset `+18885`, we can get to the final page of the commit history:

```
https://github.com/facebook/react/commits/main/?after=0f568418d2c9cd3d5f32eeb40d7305211bd17ccd+18885
```

You might have to scroll down to see the first commit pushed to the repository.

### 2. Skip `N` number of commits to reach the end of the commit history

The previous step was to get to the last page of the commit history to find the first commit pushed to the repository, but there's a way to get to the page that has nothing but the first commit. All it takes is finding the right number of commits to pass as the offset.

- Let's assume the total number of commits pushed to a repository is `100`. Using it as the offset in `?after=sha+100` would give you nothing because you've skipped all the commits.

- How about `99`? As we know that the commit index starts at 0 rather than 1, if we pass `+99` as the offset, we're going to get an empty page. How so? Because we're skipping all the commits again.

But in order to get the first commit, we have to skip 2 commits fewer than the total number of commits.

So, if we have 100 commits pushed to a repository and you wish to find the earliest one, all you have to do is pass `98` as the offset.

For React's case, the total number of commits is `18920`, so we'll use `+18918` as the offset after the `sha`, and that will take us to the first commit that was pushed to React.

<img style="width: 70%" src="{{site.baseurl}}/assets/images/posts/first-push.png" alt="">
{: .center}

## Third Base
{: .center}

In this approach, we can rely on GitHub's `REST` API to get to the desired destination through pagination and finding the last index of the Array. The response you get is an array of objects.

```
https://api.github.com/repos/facebook/react/commits?
```

- Pagination happens if the commit count is above thirty commits, so the usual and the only way to reach the end of the commit history page is:

```js
Math.ceil(Total Commits / 30)


```js
Math.ceil(Total Commits / 30)
```

- In react's case, it's ---

```js
Math.ceil(18920/30) = 631
```

- which gives us the final URL -

```
https://api.github.com/repos/facebook/react/commits?page=631
```

- Get to the first commit --- always the last index of the response, which is an array of objects

```js
const data = response.json();
const firstCommit = data.length - 1;
console.log(data[firstCommit])
```

## Base Bass
{: .center}

If you want to add other details, for example who pushed the repository, you'll have to add some logic for older repositories. Even though you can get to the first commit easily, there are unavailable fields which you'll find in the vast majority of repositories.

- *sample response from* --- `torvalds/linux`

```
$ curl https://api.github.com/repos/torvalds/linux/commits?page=42685 | jq
```

```txt
{
"sha": "1da177e4c3f41524e886b7f1b8a0c1fc7321cac2",
"commit": {
  "committer": {
    "name": "Linus Torvalds",
    "email": "torvalds@ppc970.osdl.org",
    "date": "2005-04-16T22:20:36Z"
  },
  "message": "Linux-2.6.12...Let it rip!",
},
"author": null,
"committer": null,
}
```

- *sample response from* --- `facebook/react`

```
$ curl https://api.github.com/repos/facebook/react/commits?page=632 | jq
```

```txt
{
"sha": "75897c2dcd1dd3a6ca46284dd37e13d22b4b16b4",
"commit": {
  "committer": {
    "name": "Paul O’Shannessy",
    "email": "paul@oshannessy.com",
    "date": "2013-05-29T19:54:02Z"
  },
   "message": "Initial public release",
   "author": {
   	"login": "zpao",
   },
   "committer": {
   	"login": "zpao",
   }
 }
}
```

---

__//__
{: .center}

[FirstPush](https://firstpush.vercel.app) has been built using one of the logics. It's also fast. You can check the source code on [GitHub](https://github.com/codedotjs/firstpush).

---

__Whining__
{: .center}

I didn't bother coming back to this post, but I thought I should just finish what I started writing. Looks like I ended up taking a month-long gap. Nevertheless, it's all done now.

