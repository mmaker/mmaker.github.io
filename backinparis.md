---
layout: black
title: When is Michele back in Paris?
permalink: /backinparis.html
---

<center>
<tt>
Michele will be back in:

<br>
<br>
<p id="time" style="height:70px"></p>
</tt>
</center>
<script>
var countDownDate = new Date("July 7, 2022 16:20:00 +0200").getTime();

function foo() {

  // Get today's date and time
  var now = new Date().getTime();

  // Find the distance between now and the count down date
  var distance = countDownDate - now;

  // Time calculations for days, hours, minutes and seconds
  var days = Math.floor(distance / (1000 * 60 * 60 * 24));
  var hours = Math.floor((distance % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
  var minutes = Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60));
  var seconds = Math.floor((distance % (1000 * 60)) / 1000);

  var timer = document.getElementById("time");
  if (timer !== null) {
    timer.innerHTML = days + " jours, " + hours + "h et " + minutes + "minutes."; // + seconds + "s ";
  }

  // If the count down is finished, write some text
  if (distance < 0) {
    clearInterval(x);
    document.getElementById("time").innerHTML = "EXPIRED";
  }
}

foo()
setInterval(foo, 60000);
</script>