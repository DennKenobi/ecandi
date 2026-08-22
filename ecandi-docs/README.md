# ECANDI

**ECANDI takes the guests of a live show, joining from anywhere through a
browser link, and turns each of them into two things your production can use
independently: a named video stream on your network, and an audio feed you can
place on its own channel.**

That is the whole idea. Everything else in ECANDI exists to keep those two
things reliable while a show is running.

---

## What problem it solves

Getting several remote guests into a live production is easy to do badly. The
usual approaches make every guest depend on the same fragile thing: one browser
window holding everyone, one capture source, one audio device shared by all. It
works until a guest's connection wobbles, or someone needs their microphone
moved, and the fix disturbs everybody.

ECANDI is built around the opposite assumption: **something will go wrong with
one guest, mid-show, and fixing them must cost nothing to anyone else.**

So each guest gets:

- **Their own video stream**, published on your network under a stable name, for
  any NDI receiver to subscribe to.
- **Their own audio feed**, which you can place on a dedicated channel of a
  multi-channel device, so your mixer sees each guest as a separate input
  rather than one blended feed.

These two travel entirely separate paths and never share state. Rebuilding a
guest's audio does not touch their video. Rebuilding one guest does not touch
any other. That independence is the product.

---

## Who it is for

Someone running a recurring live show with remote participants who wants each
participant under individual control (separate levels, separate treatment,
separate framing) without babysitting a pile of browser windows.

You do not need to be a developer. You do need to know what you want your video
and audio to end up in: some NDI receiver, and some audio destination.

---

## How it works, briefly

Guests join through **vdo.ninja** links, the same links you would send them
anyway. ECANDI opens each guest twice behind the scenes: once for video only,
once for audio only. The video side is rendered off-screen and published as NDI.
The audio side is pointed at the playback device and channel you chose.

You drive all of it from one window: a list of guests, live thumbnails proving
each stream is really flowing, live meters proving each guest's audio really
arrived, and per-guest controls.

Two design decisions are worth knowing up front, because they explain how the
app behaves:

- **Nothing is edited in place.** Changing a guest's settings never mutates a
  running piece; ECANDI rebuilds exactly that piece from your saved
  configuration. This is why one change never has side effects on another
  guest.
- **The window is not the show.** Closing the ECANDI console leaves everything
  streaming. Reopen it and you are reconnected to the running session. The
  console can crash without taking your broadcast with it.

---

## What ECANDI is built on

ECANDI exists because of two incredible projects by **Steve Seguin**:

**[vdo.ninja](https://vdo.ninja)** is how the guests get here at all. It solves
the genuinely hard part: getting live camera and microphone from someone's
browser, anywhere in the world, into your machine over WebRTC, with no software
for them to install and no account to make. Every guest in ECANDI arrives
through a vdo.ninja link. ECANDI drives it only through its public, documented
URL parameters; it never injects or patches anything inside the page.

**[Electron Capture](https://github.com/steveseguin/electroncapture)** is the
application ECANDI is a fork of. It contributes the window and capture
foundation, and with it the custom Electron runtime Steve maintains, whose
patches (near-lossless encoding, hardware encode, cursor suppression) ECANDI
inherits and ships. The parts of ECANDI that quietly do not go wrong are mostly
parts Electron Capture already got right.

**What ECANDI adds** is narrow by comparison: it runs many guests at once
instead of one window at a time, splits each of them into an independent video
stream and audio channel, and puts a supervisor around the whole set so one
guest can be rebuilt without disturbing the others. That is an addition to
Steve's work, built on top of it, not a replacement for it.

ECANDI is not affiliated with or endorsed by Steve Seguin or vdo.ninja.

---

## What you need

Only one thing is strictly required: **a vdo.ninja link for each guest.**

Beyond that it depends on which half of ECANDI you want. To use the video side
you need something on your network that receives NDI. To separate guests onto
their own audio channels you need a multi-channel virtual audio device; without
one, guests' audio simply plays out your normal playback device, mixed together.
You can use either half on its own.

The **[Quick Start](QUICKSTART.md)** covers this properly, with the exact steps.

---

## Will it run on my machine?

There is no fixed specification, because the cost scales with how many guests
you run and at what resolution. What follows is measured, not estimated.

**One guest at 1080p30 runs on very little.** ECANDI has been proven on a
passively cooled four-core mini PC (Intel N100 class, 8 GB of memory) with no
NDI software of any kind installed on it: one guest, holding 29.9 fps, picked
up cleanly by a receiver on another machine, picture smooth. If you want a
single remote camera turned into an NDI source, a cheap small box is enough.

**Two guests is about the ceiling for that class of machine.** On the same mini
PC, a second guest took it to roughly 90 percent CPU with both feeds settling
around 25 to 28 fps. The picture still looked good at the receiver, but there
was no room left. Those numbers were measured with remote desktop software also
running, which is expensive, so a box doing nothing else has somewhat more
headroom than that suggests.

**A full show wants a desktop processor.** On a ten-core desktop CPU, six guests
at the normal mix cost roughly one and a half cores for all the video, about one
core for all six audio feeds, and about half a core for the console with
thumbnails on. That is comfortable. It is the machine ECANDI was developed and
run on.

Three things matter more than the processor model:

- **Your guests should be remote.** That is the normal case anyway, and it
  matters because a guest's browser does the encoding work on whatever machine
  it runs on. Running guests' browsers on your capture machine is the most
  expensive thing you can do to it.
- **1080p30 is the tested setting**, and it is what the sample images here were
  captured at. Dropping a guest to 720p costs roughly half as much. 60 fps works
  for a single guest but has not been validated for a full six-guest show.
- **The console is optional once the show is running.** Closing it leaves
  everything streaming.

**If you are short of headroom**, in the order that helps most: close the
console after bring-up, turn the video thumbnails off, drop guests to 720p, and
close other software that continuously encodes your screen, such as remote
desktop or streaming tools.

---

## Where to go next

| If you want to | Read |
|---|---|
| Get one guest working, start to finish | **[Quick Start](QUICKSTART.md)** |
| Understand every control, indicator, and failure state | **[Operator's Manual](MANUAL.md)** |
| Know what's bundled and under what license | `NOTICE.md`, beside the application |

---

## Licensing and names

ECANDI inherits Electron Capture's **GNU General Public License v3.0** and stays
under it, with upstream attribution preserved. The full text ships as
`LICENSE.md` beside the application, and `NOTICE.md` lists everything bundled.

NDI® is a registered trademark of Vizrt NDI AB. ECANDI is not a product of
Vizrt NDI AB and is not endorsed by them; NDI is named here only to say what
ECANDI is compatible with.
