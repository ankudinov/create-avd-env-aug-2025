---
marp: true
theme: default
class: invert
author: Petr Ankudinov
size: 16:9
paginate: true
math: mathjax
style: |
    img[alt~="custom"] {
      float: right;
    }
    .columns {
      display: grid;
      grid-template-columns: repeat(2, minmax(0, 1fr));
      gap: 1rem;
    }
    .columns3 {
      display: grid;
      grid-template-columns: repeat(3, minmax(0, 1fr));
      gap: 1rem;
    }
    .columns1fr2fr {
      display: grid;
      grid-template-columns: 1fr 2fr;
      gap: 1rem;
      height: 80%
    }
    .columns1fr3fr {
      display: grid;
      grid-template-columns: 1fr 3fr;
      gap: 1rem;
      height: 80%
    }
    footer {
      font-size: 14px;
    }
    section::after {
      font-size: 14px;
    }
    img {
      background-color: transparent;
    }
    img[alt~="center"] {
      display: block;
      margin: 0 auto;
    }
---

# Building AVD Environment

![bg right:50%](img/pexels-obviouslyarthur-1296265.jpg)

<style scoped>section {font-size: 26px;}</style>

<!-- Do not add page number on this slide -->
<!--
_paginate: false
-->

```bash
$ id -P "$USER" | awk -F: '{print "Author: " $1 ", " $8}'
Author: pa, Petr Ankudinov
#        └─ @arista.com

$ date +"%b %Y"
Aug 2025
```

<!-- Add footer starting from this slide -->
<!--
footer: '![h:20](https://www.arista.com/assets/images/logo/Arista_Logo.png)'
-->

---

# Fun with iFrames

<div class="columns1fr3fr">
<div>

- test

</div>
<div>

<iframe src="http://localhost:8080" title="Slides" frameborder="0" width="100%" height="100%" allowfullscreen></iframe>

</div>
</div>

---

# Q&A

![bg left](img/pexels-valeriia-miller-3020919.jpg)

- [Ansible AVD](https://avd.arista.com/)
- [This repository](https://github.com/ankudinov/create-avd-env-aug-2025)

```diff
- One more slide!
+ No more slides.
```

```bash
git commit -m "The END!"
```

<!-- Add footer starting from this slide -->
<!--
footer: '![h:20](https://www.arista.com/assets/images/logo/Arista_Logo.png)'
-->
