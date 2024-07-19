---
layout: post
title: "Abstract Poop"
tags: [garbage, art, liquid, javascript]
---

Just documenting something from an ongoing project. It’s of no use to anyone else right now, but hopefully, I can make sense of it later. Let’s see when that happens. It’s still probably not going to be useful to anyone else, which is why I’m putting it here instead of letting it rot in some forgotten folder on my computer.

---

📄🖊 __Note__ --- The options can be mixed and matched based on the preference. Each instance will generate a unique piece of art, and clicking on it will regenerate a new variation with the same parameters.

---

- Default Behavior:

{% include artworks/shapes.html %}

```
{% raw %}
{% include artworks/shapes.html %}
{% endraw %}
```

---

- Palette Variations:

{% include artworks/shapes.html palette="random" %}

```
{% raw %}
{% include artworks/shapes.html palette="warmEarth" %}
{% include artworks/shapes.html palette="coolOcean" %}
{% include artworks/shapes.html palette="forestGreen" %}
{% include artworks/shapes.html palette="autumnHarvest" %}
{% include artworks/shapes.html palette="pastelDream" %}
{% include artworks/shapes.html palette="vintageMuted" %}
{% include artworks/shapes.html palette="vibrantJewel" %}
{% include artworks/shapes.html palette="deepSea" %}
{% include artworks/shapes.html palette="sunsetGlow" %}
{% include artworks/shapes.html palette="mistyMountain" %}
{% include artworks/shapes.html palette="random" %}
{% endraw %}
```

---

- Style Variations:

{% include artworks/shapes.html palette="vintageMuted" style="organic" %}

```
{% raw %}
{% include artworks/shapes.html style="abstract" %}
{% include artworks/shapes.html style="geometric" %}
{% include artworks/shapes.html style="organic" %}
{% include artworks/shapes.html style="pixelated" %}
{% endraw %}
```

---

- Density Variations:

{% include artworks/shapes.html density="low" style="geometric" palette="vibrantSea" %}

```
{% raw %}
{% include artworks/shapes.html density="low" %}
{% include artworks/shapes.html density="medium" %}
{% include artworks/shapes.html density="high" %}
{% endraw %}
```

---

- Combining Variations:

{% include artworks/shapes.html palette="random" style="pixelated" density="high" %}

```
{% raw %}
{% include artworks/shapes.html palette="warmEarth" style="abstract" density="high" %}
{% include artworks/shapes.html palette="coolOcean" style="geometric" density="medium" %}
{% include artworks/shapes.html palette="random" style="organic" density="low" %}
{% endraw %}
```

---

- Using only some parameters:

{% include artworks/shapes.html style="abstract" density="high" %}

```
{% raw %}
{% include artworks/shapes.html palette="forestGreen" %}
{% include artworks/shapes.html style="pixelated" density="high" %}
{% endraw %}
```

---

🐬 *Meow Meow* 🐬
{: .center}
