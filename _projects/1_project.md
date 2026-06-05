---
layout: page
title: "555friend: My First PCB"
description: An educational 555 timer breakout board that cycles through all four operating modes without rewiring anything — designed in KiCad as my very first PCB.
importance: 1
category: fun
img: assets/img/555friend/pcb-render.png
github: https://github.com/harrisoncbrammell/555friend
giscus_comments: true
---

I skipped analog circuits in undergrad. I was a CS student, I had electives to fill, and "Digital Logic" sounded more immediately useful than "learning how capacitors charge." Years later I keep bumping into the consequences of that decision, so this past summer I decided to start filling in the gaps.

The 555 timer seemed like the right place to begin. It's the most-produced integrated circuit in history. Hobbyists have been doing wild things with it since 1972. And it turns out the whole chip is just a couple of comparators, a flip-flop, and a transistor — genuinely elegant once you actually look at it. I breadboarded a few configurations, had fun, and then PCBway announced their summer design contest. The prize for *just entering* was a Raspberry Pi Pico 2. That was enough motivation to try making my first PCB.

---

## The Circuit Idea

The 555 timer has four classic operating modes: **astable** (free-running oscillator), **monostable** (single timed pulse), **bistable** (SR latch, no timing at all), and an **external drive** mode where you break out the internal pins to experiment with from the outside. The usual way to explore all four is to wire each one up on a breadboard, which means tearing things down and rebuilding every time you want to switch modes.

My idea was to make a board that eliminates the rewiring. Press a button, advance through the modes, and the board reconfigures itself automatically.

The key component that makes this work is a **CD4052B**, a dual 4-channel analog multiplexer. Think of it as a programmable switch matrix — you tell it which inputs to connect to which outputs, and it handles the rest. I used it to route the 555's timing, trigger, and threshold pins differently depending on which mode is active. A **4D24 binary counter** increments on each button press and feeds the multiplexer's select lines, and a **4555 binary decoder** takes the same counter output and drives four mode-indicator LEDs so you always know where you are.

The whole thing is a state machine, basically. Once I drew it out that way it seemed obvious, but getting there took a while of staring at the 4052 datasheet.

I had the logic in my head, but I wasn't sure which specific ICs to use — the 4052 family has a lot of variants. I used AI to help narrow down the parts and verify that my voltage levels and switching specs were compatible. Honestly that part went faster than I expected.

---

## KiCad and the Schematic

I'd never touched KiCad before this project. I downloaded it, watched about twenty minutes of tutorial videos, and then just started placing symbols. The schematic ended up being less painful than I feared — once you understand that you're just drawing a logical diagram and KiCad will handle the netlist, it clicks pretty fast.

The schematic is essentially the state machine described above: counter drives decoder and multiplexer select lines, multiplexer routes 555 pins, jumper headers let you swap resistor and capacitor values to change the timing. I also added a 4-pin header that breaks out the 555's trigger, threshold, control voltage, and discharge pins directly for the external drive mode.

One thing I wanted from the start was for the board itself to be a reference tool. I planned to silkscreen the timing formulas and waveform diagrams for each mode directly onto the PCB — so you could sit down with it, cycle through modes, and have the relevant math right in front of you without opening a browser.

---

## Getting Ambitious with the Shape

Here's where things got more complicated than they needed to be.

I decided the board should be shaped like a stopwatch. It made thematic sense — a timer IC on a timer-shaped PCB — and PCBway's contest seemed like an opportunity to do something a little more interesting than a boring rectangle.

My first approach was to generate the outline myself. I found a stopwatch silhouette image online, traced it in an SVG editor, tried to smooth the curves, and exported it for KiCad. That... did not go great. The outline had all these tiny detail features — the crown stem on top, the pusher buttons on the side — held together by very thin bridges that were basically asking to be snapped off during depaneling.

I went through a bunch of iterations trying to fix this. I worked with Claude to iterate on the shape: simplifying the geometry, adding reinforcing material at the narrow connection points, trying a version without the handle entirely. Here's a sampling of where that process went:

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/555friend/shape-iter-01-original.png" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/555friend/shape-iter-02-reinforced.png" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/555friend/shape-iter-03-no-handle.png" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Left: the original traced silhouette — lots of fragile detail. Middle: a reinforced version that tried to thicken the narrow bridges. Right: an attempt without the handle entirely.
</div>

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/555friend/shape-iter-04-smooth.png" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/555friend/shape-iter-05-final-attempt.png" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Two more attempts at a smoother, more manufacturable outline. The shapes kept either losing the stopwatch character or retaining structural weak points. Eventually I gave up on rolling my own and found a clean silhouette image to import instead.
</div>

I eventually just found a clean alarm clock silhouette on the internet, imported it, and used that as the board outline. Much better. Lesson learned: don't spend three days on board shape when your deadline is approaching.

---

## Component Placement (and Why Radial Was a Mistake)

With the outline settled, I moved on to placement. I had this idea that since the board was circular, I should lean into the geometry: place the ICs around the outer edge of the circle, oriented radially, with the 555 timer at the center. That way there'd be a big open area inside the circle for the silkscreen text and diagrams.

It sounded clean on paper. In practice, it created a mess.

**Routing to angled components is a pain.** KiCad's router wants to run traces at 45- and 90-degree angles. When your IC is rotated 30 degrees to follow a circular edge, getting clean traces into its pads is awkward at best and ugly at worst.

**Radial placement isolates one side of the IC.** When a chip is pointing outward, all the pins on the "outside" edge are facing the board edge — they've got nowhere to go without routing back through the whole board. You end up with traces that cross over each other or take huge detours.

The multiplexer was the worst offender. It had connections going to every other chip on the board, and with it sitting on the outer edge at an angle, those traces were going everywhere. I eventually moved it upright to the center near the 555 timer, which immediately made routing cleaner. Some of the simpler chips — like the Schmitt trigger inverters I used for debouncing — could stay on the edge since their connections were mostly local, but even those were annoying.

By the time I was mostly done routing, the placement looked pretty different from my original vision. A lot more components had migrated toward the center.

---

## The Vcc Problem (and an Inelegant Solution)

Once the bulk of the routing was done, I added a solid ground plane on the back copper layer. This is basically standard practice — it gives every component a low-impedance path to ground and reduces interference. Easy.

Vcc was a different story.

My original plan was to route a Vcc ring along the outer edge of the circular board, connecting to components as they needed power. This worked fine when the components were all on the outer edge. But after moving things inward, a bunch of components now sat inside the circle with no clean path to the outer ring without crossing the ground plane or adding a bunch of vias.

I didn't want to redo all the routing just to fix the power distribution. The deadline was approaching and I wasn't going to be paying for assembly anyway (more on that below), so I made a judgment call: I routed Vcc on the back copper layer, weaving it through the ground plane.

{% include figure.liquid loading="eager" path="assets/img/555friend/back-trace.png" class="img-fluid rounded z-depth-1" caption="The back copper layer showing the Vcc trace routed inside the ground plane." %}

Is this ideal? No. Is it going to cause problems at the frequencies this circuit runs at (a few hundred Hz at most)? Probably not. The Vcc trace is surrounded by ground on all sides, which accidentally gives it decent shielding. I'll fix it properly in a future revision, but for a first board it seemed acceptable.

---

## Component Selection: SMD vs. Through-Hole

One thing I want to flag as a lesson for future me: I went mostly surface-mount (SMD) on the passive components. SMD parts are smaller, cheaper to have assembled, and look more professional. Great reasons, in theory.

The problem is I wasn't planning to pay for assembly. I was going to build it myself. And soldering 0402 resistors and capacitors by hand, with standard hobbyist tools, is genuinely miserable. I can do it, but it's slow and unforgiving in a way that through-hole components just aren't.

If I were doing this again, I'd go through-hole for a hand-assembly board. You pay a little more per component and the board has to be slightly larger, but the PCB itself is still cheap and you can actually build it yourself without wanting to throw it across the room. The SMD choice made sense if PCBway was going to assemble it for me — which I considered — but ultimately didn't happen.

Future revision: through-hole.

---

## The Final Board

Here's how it turned out:

{% include figure.liquid loading="eager" path="assets/img/555friend/pcb-render.png" class="img-fluid rounded z-depth-1" caption="The 555friend PCB render. The silkscreen includes waveform diagrams and timing formulas for each mode, so the board doubles as a reference card." %}

The board has the 555 timer (a TLC555, chosen for its lower power consumption) at the center, mode indicator LEDs along one edge, jumper headers for swapping resistor and capacitor values, a pushbutton to advance modes, and a 4-pin header for the external drive breakout. The silkscreen diagrams cover astable, monostable, and bistable modes with their formulas.

Does it work? I don't know yet — I haven't had it manufactured. But the design rules check passes, the Gerbers look correct in the 3D viewer, and PCBway accepted the files. At some point I'll actually order a run and find out. If you want to order one yourself before I get around to it, the project is shared on PCBway:

<div class="text-center">
  <a href="https://www.pcbway.com/project/shareproject/The_555friend_A_learning_aid_for_the_legandary_555_timer_ceb3dad3.html"><img src="https://www.pcbway.com/project/img/images/frompcbway-1220.png" alt="PCB from PCBWay" /></a>
</div>

---

## What's Next

A few things I want to do with this project:

- **Order a board and actually test it.** This is the obvious one. I want to see the LEDs cycle through modes and verify that the multiplexer routing actually does what I think it does.
- **Build a 3D-printed case.** The board has mounting holes placed so you can house it in a case shaped like the PCB outline. I want to design a simple two-piece enclosure — bottom plate with a battery holder, top lid with cutouts for the LEDs and buttons. Nothing fancy, just enough to make it look finished.
- **Rev 2 with through-hole passives.** Fix the component selection mistake so I can actually assemble it by hand without magnification.
- **Better power distribution.** Clean up the back-layer Vcc routing properly with a power plane or at minimum a less cramped trace layout.

---

This was a genuinely fun project to work on, even when I was losing my mind over routing. I learned more KiCad in two weeks than I thought I'd pick up in six months, and I have a much better intuition for why analog PCB layout decisions matter. The 555 timer is a great chip to learn with — simple enough to reason about completely, but flexible enough that you can keep finding new things to do with it.

If you're a CS person thinking about making the jump into analog circuits or PCB design, I'd say just do it. The tools are free, the fabrication is cheap, and the worst case is you make a board that doesn't work and you learn why. Schematics, KiCad project files, and Gerbers are all on GitHub if you want to poke around or take the design somewhere:

**[github.com/harrisoncbrammell/555friend](https://github.com/harrisoncbrammell/555friend)**
