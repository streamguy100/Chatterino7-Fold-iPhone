# Chatterino7 — Galaxy Fold + iPhone (Device Style)

A Chatterino7 rebuild (base commit `910b01d`) that makes the whole app look
like a phone, with a Settings tab to switch between two phones. The Smart TV
build is discarded — there is no remote, no channel static, no TV boot screen
and no holiday theme in this build.

## The two looks

**Samsung Galaxy Fold** (default on a fresh install)
- One UI dark look: Samsung blue `#0381fe` accent, dark grey-blue window.
- Tall narrow phone frame, rounded corners, punch-hole camera at the top
  centre, status bar (time, battery, signal) above the chats.
- Tabs at the bottom, header at the top, chat input pinned at the bottom.
- Samsung Messages-style bubbles: blue sent, grey received, with a purple
  outline on Twitch chats and a green outline on Kick chats, on both sides.

**iPhone**
- iOS Messages-style bubbles: received on the left, your own messages on
  the right, username above the message, no timestamps, emotes as platform
  images, sender initial in a circle next to received messages.
- iPhone (Light) and iPhone (Dark) themes are also built in — pick them in
  Settings → Appearance → Themes for the light/dark version of the iPhone
  look.

## Switching — the whole app changes

Settings → **Device Style** → pick **Samsung Galaxy Fold** or **iPhone**.

Picking a phone changes the whole app together: frame, status bar, header,
tab bar, input, colours, fonts, buttons and bubbles — not just the colours.
Colours, theme and bubbles switch live. The window frame shape (tall narrow
phone, punch-hole vs notch) is applied on restart — the Settings page says
so — close and reopen the app after switching and you get the full phone
look.

## What stays the same

Everything else is plain Chatterino7: Twitch + Kick, 7TV / BTTV / FFZ,
highlights, notifications, plugins, tabs and splits. No protocol, channel,
moderation or emote-provider code is changed.

## Files

- `fold-iphone.patch` — the whole change, as a diff against clean
  Chatterino7 `910b01d`. Apply with `git apply fold-iphone.patch`.
- `build-fold-iphone.yml` — GitHub Actions workflow: Qt 6.9.3 / MSVC2022 /
  Conan 2.11.0 / Ninja, uploads `Chatterino7FoldiPhone.zip` and
  `Chatterino7FoldiPhone.exe`.
- `themes/` — the three theme JSONs in the patch (GalaxyFold, iPhoneLight,
  iPhoneDark), kept here for reference.

## Honest limits

- Qt chat rows are full-width, so bubbles are row bubbles, not
  content-tight phone bubbles: long messages still wrap at full row width.
- Avatars are sender initials in a circle, not fetched profile photos —
  Qt chat rows carry no per-message photo. Real photos stay in the
  user-info popup.
- The status bar time is real (your PC clock); battery / signal are
  drawn icons, not live hardware readings — a desktop app has no phone
  battery or signal to read.
- Whisper rows carry two username elements, so the username-above-message
  break can look slightly off on whispers. Cosmetic only.
- The phone frame is a look, not a phone: it does not add touch gestures
  or a real fold/unfold.
