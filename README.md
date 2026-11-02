# PS5 12.xx Linux Loader

Boot Linux on a PS5 that's on firmware 12.xx.

You run one file on the console. It sets the system up, the console goes to rest mode, and it wakes into Linux.

Your PS5's firmware isn't modified and nothing is flashed — a reboot puts everything back the way it was. While it runs, the loader does patch the running system in memory: that's how it prepares the handoff, and it's gone on the next boot.

The files it writes are diagnostic: a log, written to the USB drive and to `/data` on the console so it survives a crash.

Updating Linux on a PS5 isn't the usual path. The images ship each new version as a bundle on their GitHub releases page, run on the console — Bazzite Deck's bundle brings its own `update.sh`, for example. Follow the release notes of whichever image you use rather than the normal Bazzite update route.

---

## Which file do I need?

| Your console | File |
|---|---|
| PS5 (Phat) or PS5 Slim | `ps5-linux-loader-12xx-ONLY-aio.elf` |

That one file is the loader. It's built here from our own 12.xx offsets, with extra sanity checks, and it writes a log to the USB drive's boot partition so a failure can be diagnosed instead of guessed at.

If your console is on a firmware outside 12.xx, this build refuses it by design — upstream's **ps5-linux** project (https://github.com/ps5-linux/ps5-linux-loader) builds a loader that covers more.

This repo is for a PS5 (Phat) or PS5 Slim. There's no build here for a PS5 Pro.

---

## Firmware support

| Firmware | PS5 / Slim |
|---|---|
| 12.00 | ✅ confirmed |
| 12.02 | ✅ confirmed |
| 12.20 | ✅ confirmed |
| 12.40 | ✅ confirmed |
| 12.60 | ✅ confirmed |
| 12.70 | ✅ confirmed |

**Pro console support and source** https://github.com/Wigglez-sudo/12.xx-linux-loader-pro-beta/

---

## What you need

- A PS5 on 12.xx, already jailbroken
- **An SSD in a USB enclosure**, with a Linux image on it — highly recommended over a USB stick. The image goes on the drive's boot partition: `bzImage`, `initrd.img` and `cmdline.txt`. A stick often works, but it boots slowly and is the usual reason the loader doesn't find the image in time
- A way to send the ELF to the console (your payload host)

If you need an image, these work with this loader:

- **Bazzite Deck** (VivaLaVent) — https://github.com/VivaLaVent/ps5-bazzite-deck **Latest** https://github.com/VivaLaVent/ps5-bazzite-deck/releases/tag/v2.1.0 — **highly recommended.** Steam Gaming Mode
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

**"File bzImage not found"** — the drive wasn't ready when the loader looked. This build waits and retries for a while, but if the drive never turns up: power off, move the drive to a different USB port, and run it again. An SSD in an enclosure makes this much less likely.

**It detects the drive, then nothing happens** — this build writes `ps5lin_probe.log` to the boot partition, adding to it each run so the whole history is kept. That log is what a failure gets diagnosed from, so send it if you're stuck.

**It sleeps and never wakes into Linux** — that's usually the image or the drive, not the loader. Check `cmdline.txt` on the boot partition points at the right partition (a `root=PARTUUID=…` that matches your drive is the reliable way).

---

## Good to know

- **This build refuses anything outside 12.xx.**
- **Your firmware isn't modified** by running this.
- This is a loader, not an exploit. You still need your console jailbroken first.

---

## Thanks

Every 12.xx offset in here came from a tester's console dump. None of it exists without them.

**Phat / Slim:** Ben3Code, Jawshr, Inkredible (12.70) · Shortykun (12.20) · Cupido (12.40) · MagmaEye (12.60) · 4killerbeast, Rokkun (12.00) · Darks, frandizzle_ (12.02)

Built on the work of https://github.com/ps5-linux/ps5-linux-loader (and https://github.com/ps5-linux/ps5-linux-loader/pull/69), with thanks to rmux for the clean-up.

**Discord:** https://discord.gg/ps5linux

**Rmux Guide** https://guide.rmux.eu/
