SlowEdgeStripboard
===

An analog video edge detection circuit as a Eurorack module, laid out on David Haillant's [Eurorack Stripboard](https://www.davidhaillant.com/eurorack-stripboard-1-3/). It works, but building on protoboard or stripboard is not really a great idea unless you are desperate to have something working as soon as possible. Caveat emptor.

Also, please note: this circuit is designed to work with 0-1v RGB analog video signals, not video standards like NTSC composite video, PAL, or SECAM.

This is merely a prototype version of Slow Edge, which is the slow version of Edgelord. If you can't find these more formal, polished Eurorack PCB projects in the Octovolt GitHub repos, please know they will be published very soon.

How does it work?
---

Here is the block diagram:

```
         ┌────────────┐   ┌──────────┐   ┌───────┐   ┌────────────┐   ┌───────┐
         │            ├──►│ Inverter ├──►│  HPF  ├──►│ Comparator ├──►│       │
         │            │   └──────────┘   └───────┘   └────────────┘   │       │
 IN ────►│ Comparator │                                               │  Mix  ├───► OUT
         │            │                  ┌───────┐   ┌────────────┐   │       │
         │            ├─────────────────►│  HPF  ├──►│ Comparator ├──►│       │
         └────────────┘                  └───────┘   └────────────┘   └───────┘
```

To explain, the strategy is: 

1. Create a posterized image with a comparator. That is, the resulting image is either fully white or fully black, based on a threshold.
2. Split the posterized image signal into two separate signals, and then invert one of them. Now we have the inverse of the posterized image.
3. Run both signals through high pass filters. This creates fuzzy lines, fading to the right, where the edges of the posterized images were previously. That is, when the image goes suddenly from white to black or black to white, the high pass filter retains this transition, but eliminates everything else. The high pass filter will create fuzzy lines for the leading, left edge of the posterized image, or the trailing, right edge of the posterized image, depending on whether the image is the original posterized image or the inverse.
4. Run both signals through comparators again. This eliminates any gradation or fuzziness, and only stark lines remain.
5. Mix the two signals together, joining the leading and trailing edges into a complete edge outline, and then send the result to the output.

Why is it called Slow Edge?
---

This circuit, like the [two_comparator_effect]() that inspired it, uses the LM339 quad comparator. This is a great, cheap, compact, readily available comparator chip, but unfortunately it's a little bit too slow for accuracy in video circuits. With the LM339, due to its 1.3 microsecond response time, the resulting image is offset very slightly to the right. For better accuracy, we should be using a comparator with a response time below 83 nanoseconds. While Slow Edge is cool, cheap and matches the output of the [two_comparator_effect](https://github.com/cyberboy666/two_comparator_effect), its sister project, called Edgelord, uses a faster comparator and will matcn analog video signals more accurately.

Thanks
---

Huge thanks to [Tim Caldwell / cyberboy666](https://github.com/cyberboy666) for his influence on this project. If I had not studied his [two_comparator_effect](https://github.com/cyberboy666/two_comparator_effect), I never would have come up with this circuit. I should also mention my tremendous debt to [Sean Russell Hallowell / isorhythmics](https://www.instagram.com/isorhythmics/), who suggested I check out the schematics for the [Sandin Image Processor](https://s3.eu-central-1.wasabisys.com/scanlines-other/DIY%20Resources/Sandin%20IP/Sandin_IP.pdf) while we were briefly chatting at a [video art meetup](https://www.syzygysf.com/event-details/phase-shift-video-artist-meetup-presented-by-av-club) at [Syzygy](https://www.syzygysf.com/) in San Francisco. The Sandin Image Processor's Differentiator circuit is another huge influence on this project.
