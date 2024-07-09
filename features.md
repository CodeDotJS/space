---
layout: default
title: Features
---

<span style="border-bottom: 3px solid black;">Existence ft Features</span>
{: .center}

This place exists solely to showcase the features of this blog. Since I’m too lazy to document everything, I’ll put here what exists on this site.

---

# Adding Highlights

| <span class="color-red highlight-red">Red</span> | <span class="highlight-black">Black</span> | <span class="color-blue highlight-blue">Blue </span> | <span class="color-orange highlight-orange">Orange </span> | <span class="color-green highlight-green">Green</span>

```html
<span class="color-red highlight-red">Red Highlight</span>
<span class="highlight-black">Black Highlight</span>
<span class="color-blue highlight-blue">Blue Highlight</span>
<span class="color-orange highlight-orange">Orange Highlight</span>
<span class="color-green highlight-green">Green Highlight</span>
```

<br>

---

# Adding Date/Time

{% include date.html only="date" %}

```
{% raw %}
{% include date.html %}
{% include date.html only="time" %}
{% include date.html only="date" %}
{% include date.html custom="2024-07-09 15:30" %}
{% include date.html custom="2024-07-09 15:30" only="time" %}
{% include date.html custom="2024-07-09" only="date" %}
{% endraw %}
```

<br>

---

# Adding Projects

- Create a `projects.yml` file in `_data` folder.
- Save the following content and check the Project page.

```yaml
- image: "meow.png or https://meow.com/meow.png"
  title: "Meow Meow"
  description: "A collection meows"
  blog: "2024-06-28-cool-meow"
  preview: "https://meowproject.org"
```

- `image` can be the URL or the file saved in `assets/projects/`

<br>

---

# Adding CSV

- First, you need to save the CSV file in `assets/_data/dummy.csv`

```
{% raw %}
{% include csv.html file="dummy.csv" row=5 %}
{% endraw %}
```

{% include csv.html file="dummy.csv" row=5 %}

<br>

---

# Adding Collage

There are various ways to setup slider/collage in the blog post or anywhere -

```
{% raw %}
1. {% include collage.html dir="June" %}
2. {% include collage.html dir="March" file="heat.png, water.svg" %}
3. {% include collage.html dir="April" start=2 end=6 %}
{% endraw %}
````

{% include collage.html dir="assets" start=2 end=5 %}

```
{% raw %}

{% include collage.html dir="assets" start=2 end=5 %}

{% endraw %}
```
{: .center }

- The first option lets you show all the image of the folder "June" of `assets/images/June`
- Through second option, you can choose which images you have to show.
- If you wish to include a range of images, you can specific it. Index starts at zero.

<br>

---
