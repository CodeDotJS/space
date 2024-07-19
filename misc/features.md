---
layout: default
title: Features
permalink: /features
---

<span style="border-bottom: 3px solid black;">Existence ft Features</span>
{: .center}

This place exists solely to showcase and remember the features of this blog.
{: .center}

---
# Adding Artwork

Instead of using header images for the blog posts, use random shapes. These shapes change either when the canvas is clicked or the window is resized. Additionally, the grid size can be easily adjusted to make the shapes bigger or smaller. By default, the grid size is set to 5.

{% include artworks/resize.html size=5 %}

The height is customizable, too ---

{% include artworks/resize.html size=10 height=100 %}

```
{% raw %}
1. {% include artworks/resize.html %}
2. {% include artworks/resize.html size=10 %}
3. {% include artworks/resize.html size=10 height=100 %}
4. {% include artworks/resize.html size=10 height=100 animate=true %}
{% endraw %}
````

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

# Adding Image

{% include images.html file="griffin.png brush.svg sleeping-cat.png sis.gif" alt="A cute cat" caption="Therefore!" center=true width="35%" %}

```
{% raw %}
{% include images.html
   file="griffin.png brush.svg sleeping-cat.png sis.gif"
   alt="A cute cat"
   caption="Therefore!"
   center=true
   width="50%" %}
{% endraw %}
```

Root folder is set to `{% raw %}{{site.baseurl}}/assets/images/{% endraw %}`.

<br>

---

# Adding Books

The cover image is generated randomly. It also changes on a click.

{% include book.html
   status="active"
   name="Crime and Punishment"
   author="Fyodor Dostoevysky"
   started="January 9, 2024"
   finished="June 2, 2024"
   rating="4" progress="100"
   url="https://www.goodreads.com/book/show/7144.Crime_and_Punishment"
   tags="Fiction, Classic Literature, Novel"
   description="Fyodor Dostoevsky's Crime and Punishment is a masterful psychological exploration of guilt and redemption. The novel's protagonist, Raskolnikov, is compelling in his complex moral struggle and descent into madness after committing murder. Dostoevsky's vivid portrayal of 19th-century St. Petersburg adds depth to the narrative. The philosophical dialogues are profound, though occasionally dense. Overall, it's a thought-provoking and intense read that leaves a lasting impact." %}


- Make the block visible by setting `status="active"`. You can leave it empty to hide the block.

```
{% raw %}

{% include book.html
   status="active"
   name="Crime and Punishment"
   author="Fyodor Dostoevysky"
   started="January 9, 2024"
   finished="June 2, 2024"
   rating="4"
   progress="100"
   url="https://www.goodreads.com/book/show/7144.Crime_and_Punishment"
   tags="Fiction, Classic Literature, Novel"
   description="Fyodor Dostoevsky's Crime and Punishment...lasting impact." %}

{% endraw %}
```

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
  description: "A collection of meows"
  blog: "2024-06-28-cool-meow"
  preview: "https://meowproject.org"
```

- `image` can be the URL or the file saved in `assets/projects/`

<br>

---

