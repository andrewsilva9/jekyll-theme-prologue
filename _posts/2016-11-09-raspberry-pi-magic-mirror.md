---
layout: post
title: Raspberry Pi Magic Mirror
date: '2016-11-09T09:28:00.001-05:00'
author: Andrew Silva
---

Magic Mirrors have been all the rage lately, and I couldn't resist the urge to make one myself after seeing so many different iterations come out. I had an old Raspberry Pi laying around and a couple of unused small TVs that I could repurpose, so I figured I was already halfway to success.

As it turns out, the mirror itself is the most important part and also the most expensive. I went for acrylic instead of glass because I didn't really know what the difference would be, and it was less than half the cost. It actually works very well, but if I were to make these things professionally then glass would probably look and function better. At over twice the cost, however, I think acrylic works fine as long as you're careful with it.

Overall, the shopping list for the project is not too bad. Mine looked like:
 * 1 Raspberry Pi
 * 1 Old TV or monitor (high contrast is better, black should be totally dark and light should shine brightly)
 * 1 USB WiFi plugin for the Pi (or onboard WiFi)
 * The mirror itself (one way acrylic for me)
 * Something to case it all up in (for me, 2x4s)

Once you have it all, the only big thing to setup before you put all the pieces together is the Pi itself. If you have a newer Pi, there are actually some magic mirror packages already made for you to install and be on your way! If you are less fortunate and have an older Pi, you're stuck doing it yourself. I threw together a WordPress site and have the Pi constantly acting as a server for that site. Feel free to pull it down and modify it as necessary [from here][mirror-repo].

After you set your Pi up with a site (or at least get WiFi and SSH access so you can tinker from the other side of the mirror), putting everything together is relatively straightforward. I cut up my 2x4s and built a simple case, attached the mirror to the front of the case and then secured the TV inside the case pressed up against the mirror so that the light shines out through the mirror. I also secured the Pi behind the TV, and left a couple of holes in the case for wires to stick out the bottom (since I didn't run a power supply or anything else up into the case itself, the TV and Pi power cords run out the bottom). 

<img src="https://andrewsilva9.github.io/jekyll-theme-prologue/assets/images/mirror.jpg" class="image centered-med"/>

[mirror-repo]: https://github.com/andrewsilva9/pi1-magic-mirror