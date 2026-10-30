# PS5 12.xx Linux Loader

Boot Linux on a PS5 that's on firmware 12.xx.

You run one file on the console. It sets the system up, the console goes to rest mode, and it wakes into Linux.

Your PS5 firmware isn't modified. The loader only reads the console — the only things it writes go on the USB drive's boot partition: a log (from the 12.xx-only build) and, on a Pro, the GPU firmware.

Updating Linux on a PS5 isn't the usual path. The images ship each new version as a bundle on their GitHub releases page, run on the console — Bazzite Deck's bundle brings its own `update.sh`, for example. Follow the release notes of whichever image you use rather than the normal Bazzite update route.

---

## Which file do I need?

| Your console | File | Notes |
|---|---|---|
| PS5 (Phat) or PS5 Slim | `PS5-linux-loader-aio-3xx-13xx` | The main loader. **Use this.** |
| PS5 or Slim, if that gives trouble | `ps5-linux-loader-12xx-ONLY-aio.elf` | Fallback for 12.xx only — and there shouldn't be a need |
| PS5 Pro | `Linux-loader-pro-aio.elf` | One file, works on every supported Pro firmware |
| PS5 Pro, single firmware | `ps5-linux-loader-1200/1240/1260/1270-pro.elf` | Same code as the Pro AIO, locked to one firmware |

Use the Pro build on a Pro and a normal build on a Phat or Slim. The Pro builds check the console and refuse anything that isn't a Pro, rather than doing something half right.

The main loader comes from the **ps5-linux** project and handles everything from 3.00 to 13.60 in one file — 12.xx is simply what this repo is about. The 12.xx-only build is ours: it's the fallback if the main one misbehaves on your console, and it writes a log you can send me.

---

## Firmware support

| Firmware | PS5 / Slim | PS5 Pro |
|---|---|---|
| 12.00 | ✅ confirmed | ✅ confirmed working — Bazzite |
| 12.02 | ✅ confirmed | — no dump yet |
| 12.20 | ✅ confirmed | — no dump yet |
| 12.40 | ✅ confirmed | ✅ confirmed working — Bazzite |
| 12.60 | ✅ confirmed | ✅ confirmed working — Bazzite |
| 12.70 | ✅ confirmed | ✅ confirmed working — Bazzite |

---

## What you need

- A PS5 on 12.xx, already jailbroken
- **An SSD in a USB enclosure**, with a Linux image on it — highly recommended over a USB stick. The image goes on the drive's boot partition: `bzImage`, `initrd.img` and `cmdline.txt`. A stick often works, but it boots slowly and is the usual reason the loader doesn't find the image in time
- A way to send the ELF to the console (your payload host)

If you need an image, these work with this loader:

- **Bazzite Deck** (VivaLaVent) — https://github.com/VivaLaVent/ps5-bazzite-deck — **highly recommended.** Steam Gaming Mode, and on a Pro it takes the GPU firmware this loader dumps with no extra steps
- **ps5-linux** — https://github.com/ps5-linux/ps5-linux-image/releases — plain Linux, good for testing and tinkering

---

## How to use it

1. Put your Linux image on the SSD, in its enclosure, and plug it into the console.
2. Send the right `.elf` from this repo to the console and run it.
3. Watch the screen. It prints what it's doing, then finishes with **"Finished preparation. Going to rest mode in 5 seconds."**
4. The console drops into rest mode. Power it back on and Linux boots.

That's it. Nothing to type, nothing to configure.

---

## If it doesn't boot

**"File bzImage not found"** — the drive wasn't ready when the loader looked. The 12.xx-only build waits and retries for a while; the main loader doesn't, it gives up at once. Either way: power off, move the drive to a different USB port, and run it again. An SSD in an enclosure makes this much less likely.

**It detects the drive, then nothing happens** — the 12.xx-only build writes `ps5lin_probe.log` to the boot partition, adding to it each run so the whole history is kept. The main loader doesn't write a log at all. If you're chasing a problem, run the 12.xx-only build so there's something to send me.

**It sleeps and never wakes into Linux** — that's usually the image or the drive, not the loader. Check `cmdline.txt` on the boot partition points at the right partition (a `root=PARTUUID=…` that matches your drive is the reliable way).

---

## PS5 Pro notes

- The Pro build writes the console's GPU firmware — eight `viola_*.bin` files — to `lib/firmware/amdgpu/` on the boot partition.
- Images that already support the Pro — [Bazzite Deck](https://github.com/VivaLaVent/ps5-bazzite-deck) for example — pick those files up automatically at boot.
- If your image doesn't, the GPU firmware has to be added to the initrd yourself, otherwise the GPU won't come up properly.
- 12.02 and 12.20 on a Pro aren't supported yet. Nobody has sent dumps for them.

---

## Good to know

- **Pro builds check the console** and refuse a Phat or Slim rather than run with the wrong tables. The Phat/Slim builds don't do that check — run one on a Pro and it won't refuse, but you'll be missing the Pro GPU firmware, so use the Pro build on a Pro.
- **The 12.xx-only build refuses anything outside 12.xx.** The main loader covers 3.00 to 13.60.
- **Your firmware isn't modified** by running this.
- This is a loader, not an exploit. You still need your console jailbroken first.

---

## Thanks

Every 12.xx offset in here came from a tester's console dump. None of it exists without them.

**Phat / Slim:** Ben3Code, Jawshr, Inkredible (12.70) · Shortykun (12.20) · Cupido (12.40) · MagmaEye (12.60) · 4killerbeast, Rokkun (12.00) · Darks, frandizzle_ (12.02)

**PS5 Pro:** solidpsycho (12.00) · Anon (12.40) · Marlon (12.60) · H1Vltg3 (12.70)

Built on the work of https://github.com/ps5-linux/ps5-linux-loader (and https://github.com/ps5-linux/ps5-linux-loader/pull/69), with thanks to rmux for the clean-up.

**Discord:** https://discord.gg/f8YgEXS5Z
