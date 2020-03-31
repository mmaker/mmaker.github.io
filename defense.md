---
layout: page
title: PhD Defense
permalink: /defense.html
---

<style>
/* The switch - the box around the slider */
.switch {
  position: relative;
  display: inline-block;
  width: 60px;
  height: 34px;
}

/* Hide default HTML checkbox */
.switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

/* The slider */
.slider {
  position: absolute;
  cursor: pointer;
  top: 0px;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ccc;
  -webkit-transition: .4s;
  transition: .4s;
}

.slider:before {
  position: absolute;
  content: "";
  height: 26px;
  width: 26px;
  left: 4px;
  bottom: 4px;
  background-color: white;
  -webkit-transition: .4s;
  transition: .4s;
}

input:checked + .slider {
  background-color: #2196F3;
}

input:focus + .slider {
  box-shadow: 0 0 1px #2196F3;
}

input:checked + .slider:before {
  -webkit-transform: translateX(26px);
  -ms-transform: translateX(26px);
  transform: translateX(26px);
}

/* Rounded sliders */
.slider.round {
  border-radius: 34px;
}

.slider.round:before {
  border-radius: 50%;
}
</style>

<script>
function toggleElement(x, default_display) {
  if (x.style.display === "none") {
    x.style.display = default_display;
  } else {
    x.style.display = "none";
  }
}

function toggleJury() {
  var below = document.getElementById("jury-slides");
  var vid = document.getElementById("stream");
  toggleElement(below, "flex");
  toggleElement(vid, "block");
}
</script>


Motivated by the recent development of a pandemia, the collapse of western economy,
and the increase of police and military surveillance, this thesis defense will **not**
be hosted at the École. Instead,
be broadcasted to all corners of the interweb. On this webpage. In livestream.

### Material
- [PhD manuscript](https://www.di.ens.fr/~orru/thesis.pdf), 110 pages. It's also a [Game of Life](https://en.wikipedia.org/wiki/Glider_(Conway%27s_Life)) flip book!
- [Slides](https://slides.com/micheleorru/deck/fullscreen?token=5Sr5iC4M), ~40 slides;
- [Implementations](https://www.di.ens.fr/~orru/projects.html).


### When

Tuesday the 7th April 2020, at 17:00 CET
You can download the [ics file](invite.ics) for your own calendar or [add it to your gCal](https://calendar.google.com/event?action=TEMPLATE&tmeid=MDFydjJ1M2gwbXQwbWE1YTd1YzFsMjVmazggdHVtYm9sYW5kaWEubmV0XzFibmhnbzBhZTlrY3UxZm0zZjlvM2ZoYWc0QGc&tmsrc=tumbolandia.net_1bnhgo0ae9kcu1fm3f9o3fhag4%40group.calendar.google.com).
 <a target="_blank" href="https://calendar.google.com/event?action=TEMPLATE&amp;tmeid=MDFydjJ1M2gwbXQwbWE1YTd1YzFsMjVmazggdHVtYm9sYW5kaWEubmV0XzFibmhnbzBhZTlrY3UxZm0zZjlvM2ZoYWc0QGc&amp;tmsrc=tumbolandia.net_1bnhgo0ae9kcu1fm3f9o3fhag4%40group.calendar.google.com"><img border="0" src="https://www.google.com/calendar/images/ext/gc_button1_en.gif"></a>

(*After 2 failed attemps, this date is the good one.*)



### The defense

Just below, you can find the livestream. Slides can also be followed **[live](https://slides.com/micheleorru/deck/live?token=5Sr5iC4M)**.
<!-- Rectangular switch -->

<div align="center" id="stream">
<iframe src="https://player.twitch.tv/?channel=michmichmu" frameborder="0" allowfullscreen="true" scrolling="no" height="378" width="620"></iframe>
</div>


<div class="split" align="center" id="jury-slides" style="display: none;">
<div class="minipage" align="center">
<iframe src="https://player.twitch.tv/?channel=michmichmu" frameborder="0" allowfullscreen="true" scrolling="no" height="420" width="500"></iframe>
</div>
<br>
<div  class="minipage">
<iframe src="https://slides.com/micheleorru/deck/embed?token=5Sr5iC4M&style=hidden" width="550" height="420" scrolling="no" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

</div>
</div>

<div align="center">
<h4>Slides on the side:
<label class="switch" style="vertical-align: 7px;">
  <input type="checkbox" onclick="toggleJury();">
  <span class="slider round"></span>
</label>
</h4>
</div>


<br />
<br />


### The thesis: "Non-interactive Arguments of Knowledge"
Besides using this defense as an occasion to bash at ENS' security, we will explore non-interactive arguments of knowledge, a cryptographic primitive that allows a prover to convince a verifier of the truth of a certain statement.
We will focus on cryptographic constructions that allow a user to prove knowledge of a so-called witness that satisfies a circuit, while simultaneously hiding it.

First, we will prove the existence of non-interactive witness-indistinguishable arguments of knowledge in the standard model. Our proof system is an argument of knowledge that is secure even if an adversary subverts the initial parameters.
Secondly, we will revisit a family of zero-knowledge arguments of knowledge (SNARKs), and show that it can be moved to post-quantum assumptions, as long as the verifier is known in advance.
Lastly, we consider a novel, anonymous cryptocurrency whose security can be guaranteed via arguments of knowledge: Mimblewimble. The cryptocurrency was proposed by an anonymous author in 2016.

The defense will conclude reminding academics that there's people dying because of bad cryptography, and that it is our duty as cryptographers to work also on standards and implementations.
