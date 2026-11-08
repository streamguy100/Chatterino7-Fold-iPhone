# Chatterino7 — Galaxy Fold + iPhone (Device Style)

A Chatterino7 rebuild (base commit `910b01d`) that makes the whole app look
like a phone, with a Settings tab to switch between two phones. The Smart TV
build is discarded — there is no remote, no channel static, no TV boot screen
and no holiday theme in this build.

## Launch flow — code screen, then home

- **Code set + Code Screen ON:** launch → code screen first → correct
  code (or a new code via "Forgot code?") → **home screen, always**.
- **No code ever set, or Code Screen OFF:** launch → the code screen is
  skipped entirely → straight to the **home screen**. No lock, no prompt.
  The app never locks on first install and never forces a code before
  chats can be used.
- The code screen only ever appears as the first gate at launch. It does
  not open again after launch in any other way.

## Home screen (OS-style hub)

After launch (and after the code screen, if any) the app **always** lands
on a home screen that looks like that phone's OS home screen — iPhone
home screen for the iPhone pick, Samsung One UI home screen for the Fold
pick.

- **Every chat tab is an app:** each open channel is an app icon on the
  home screen (channel name + an icon with the channel's initial, like a
  phone app). The normal Chatterino tab bar is hidden — the home screen
  is the hub.
- **Tapping an app icon** opens just that chat, full view, styled like
  that phone's messaging app (Fold = Samsung Messages, iPhone = iOS
  Messages). The **⌂ Home button in the status bar** at the top returns
  to the home screen.
- **＋ New chat tile** on the home screen opens a fresh tab, then that
  chat.

If a full home-screen grid needs a restart to appear after switching
phones in Settings, the Settings page says so — the frame size applies
on restart, colours/theme/bubbles switch live.

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

## Per-chat anonymous mode (Anon button)

**Every chat has its own Anon button**, in that chat's header bar (next to
the pinned-message / chatters / menu buttons, under the phone status bar).

- Tap it once: **anonymous mode ON for that chat only**. Tap it again: OFF
  for that chat only.
- It **never affects any other chat**: other chats keep their normal
  connected account and keep working as usual. There is no global switch
  here at all.
- **What ON does for that chat:** the chat becomes view-only — the typing
  box is disabled (placeholder "Anonymous mode — view only"), so nothing
  can be typed or sent as your account from that chat. And because no
  account counts as connected for that chat, **nobody goes on the right**:
  every message in it stays on the left, like the logged-out rule.
- **What OFF does:** the typing box comes back, the placeholder goes back
  to "Send message as …", and your connected account's messages go on the
  right again in that chat.
- **You can see the state at a glance:** OFF shows plain **"Anon"**;
  ON shows **"Anon ON"** with an accent-coloured border around the button.
  The tooltip also says which state it is in.
- **State is per split, in memory only.** Closing that chat drops it — a
  reopened chat starts with anonymous OFF. Switching the same split to a
  different channel also turns it off (a reconnect to the same channel
  keeps it).

### Why it exists: banned chats

If you are **banned in a chat**, that chat sits blank when viewed as your
connected account — Twitch does not deliver that channel's messages to
your banned account. Anonymous mode fixes that: it is **not just hiding
the typing box**. Tapping Anon on a banned chat **reloads that chat as an
anonymous viewer**:

- The chat is parked off the normal (authenticated) read connection and
  joined on a dedicated anonymous connection (the `justinfan` anonymous
  login, no account at all). The same channel keeps receiving messages —
  only which connection delivers them changes — so the messages flow in
  and the chat is no longer blank, view-only.
- Tapping Anon off joins the chat back on the normal connection, on your
  connected account.
- Only that chat reloads and changes; every other chat is untouched, and
  the anonymous connection survives account switches and reconnects of
  the normal pool.
- **Kick needs no reload:** Kick's chat feed is public and never uses
  your login to receive messages, so a Kick ban never blanked the chat
  in the first place — on Kick the button just makes the chat view-only
  (and a ban there only ever added a timeout notice).
- A system message marks the switch in the chat itself: "Viewing this
  chat anonymously" on, "Back on your connected account" off.

## Messaging look — normal Chatterino, laid out like a messaging app

Tapping an app icon on the home screen opens that chat in **normal
Chatterino format** — same input, same features — but laid out like a
real messaging app, in **each** channel:

- **Received on the LEFT, sent on the RIGHT**, for every channel.
- Who goes on the **right** is decided **per channel, by checking the
  account connected to the app for that channel's platform** — never by
  a fixed username, and no name is hard-coded:
  - **Twitch channel →** the Twitch account currently logged in to the
    app (`accounts->twitch.getCurrent()`). Messages from *that* account
    go right; everyone else goes left.
  - **Kick channel →** the Kick account currently logged in to the app
    (`accounts->kick.current()`). Messages from *that* account go
    right; everyone else goes left.
  - A Twitch login name does **not** count as own in a Kick channel,
    and a Kick login name does **not** count as own in a Twitch
    channel — the check is gated on the message's platform.
  - **Logged out / anonymous for that platform → nobody goes right.**
    Every message stays on the left until an account is connected.
- The platform outline colours stay: purple outline on Twitch chats,
  green outline on Kick chats, on both sides.
- This holds in the chat screen for both phones (Fold and iPhone).

## Switching — the whole app changes

Settings → **Device Style** → pick **Samsung Galaxy Fold** or **iPhone**.

Picking a phone changes the whole app together: frame, status bar, header,
tab bar, input, colours, buttons and bubbles — not just the colours.
Colours, theme and bubbles switch live. The window frame shape (tall narrow
phone, punch-hole or notch) is applied on restart — the Settings page says
so — close and reopen the app after switching and you get the full phone
look.

## Code Screen (passcode lock)

Settings → **Code Screen** (its own tab, separate from Device Style).

- **Toggle:** "Code Screen — ask for a code every time the app opens".
  Default **OFF** on a fresh install.
- **Your code:** set your own numeric code, **4–6 digits**. Changing it
  there just saves the new code. Clearing it unprotects the app.
- **Lock screen style** follows the Device Style pick: iOS "Enter
  Passcode" — dots, numeric keypad 1–9 / 0, Delete — on iPhone; Samsung
  One UI "Enter PIN" on the Fold. Wrong code shows "Try again" and
  shakes the dots; it never locks you out. Correct code fades into the
  home screen.
- **Forgot code?** On the lock screen itself there is a "Forgot code?"
  button. Tapping it lets you set a **new code right there, with no old
  code asked, no extra step, no extra verification**. After setting the
  new code the lock screen unlocks into the home screen.
- **No code ever set = no lock screen at all.** The app opens straight
  to the home screen. Instead there are hints to add a code, so no one
  else using this PC can type as you:
  - Settings → Code Screen tab shows: "No passcode set yet — anyone
    using this PC can type as you. Turn on Code Screen and set a code
    to lock the app."
  - A small banner above the chats on startup shows the same thing,
    with a "Set a passcode" button that opens Settings → Code Screen.
    It is dismissible (×) and never blocks chats or typing.
  - Once a code is set and Code Screen is on, the lock screen appears
    on launch and the hint goes away. Turn Code Screen off again, or
    clear the code, and the hint comes back.

## What stays the same

Everything else is plain Chatterino7: Twitch + Kick, 7TV / BTTV / FFZ,
highlights, notifications, plugins, tabs and splits. No protocol, channel,
moderation or emote-provider code is changed.

## Files

- `fold-iphone.patch` — the whole change, as a diff against clean
  Chatterino7 `910b01d` (3,462 lines, 41 files). Apply with
  `git apply fold-iphone.patch`.
- The per-chat anonymous button is in `SplitHeader` ("Anon" / "Anon ON"),
  the per-split state lives on `Split` (`isPhoneAnonymous` /
  `setPhoneAnonymous`, in memory only), and the layout/paint code threads
  it through `MessageLayoutContext.phoneAnonymous` from `ChannelView`,
  so an anonymous split shows everybody on the left. The anonymous
  reload lives in the Twitch provider: `TwitchIrcServer::
  setChannelAnonymousRead` moves one channel between the normal read
  pool and a dedicated anonymous read pool (`TwitchReadConnectionPool`
  with `forceAnonymousRead`, `joinChannel` / `partChannel` per pool),
  and `IrcMessageHandler` skips the "unexpectedly dropped" message for
  the park PART itself.
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
  user-info popup. Home-screen app icons are initials too, for the same
  reason — tabs carry no photo.
- The status bar time is real (your PC clock); battery / signal are
  drawn icons, not live hardware readings — a desktop app has no phone
  battery or signal to read.
- Whisper rows carry two username elements, so the username-above-message
  break can look slightly off on whispers. Cosmetic only.
- The phone frame is a look, not a phone: it does not add touch gestures
  or a real fold/unfold.
- **The code is stored locally in the app settings on this PC.** It is a
  local lock screen, not real account security — anyone who can read your
  settings file could read it. And by design, "Forgot code?" sets a new
  code with no old code asked, so it keeps you from ever being locked
  out — it does not keep someone at your keyboard out.
- The tab bar is hidden by the home-screen hub; the ⌂ Home button in the
  status bar is the way back to it. The usual Chatterino tab-switching
  shortcuts still work, but the tab strip itself is not shown.
- The lock screen and home screen are compile-checked by patch review
  only (no Qt toolchain on this machine); the first real check is the
  Windows GitHub Actions build, then a live run.
- **Per-chat anonymous is view-only for that one split, not a real
  account switch.** Chatterino's Anonymous account is global — switching
  it would change every chat at once, which is exactly what this button
  must not do — so instead the button disables that one split's typing
  box and treats its messages as anonymous (nobody on the right), and
  for Twitch it moves that one channel's *reading* to a dedicated
  anonymous connection. Your connected account stays logged in
  everywhere else. Moderator tools reached from that chat's message
  menu still act as the connected account, as in plain Chatterino; the
  block covers typing/sending in that chat.
- **Anonymous viewing cannot get past a chat that requires login to
  view.** A plain ban is fixed by it, but a subscriber-only or
  followers-only chat still shows nothing to an anonymous viewer —
  Twitch only sends those messages to logged-in subscribers/followers.
  (Subscriber-only / followers-only room modes are enforced by Twitch
  per viewer, not per ban.)
- While a chat is anonymous, Twitch thinks the viewer is `justinfan`,
  so that chat briefly forgets you are a mod/VIP there (the anonymous
  connection's USERSTATE carries no badges). Toggling Anon off re-joins
  on your account and Twitch sends your real status again.
- Two splits showing the *same* channel share that channel's read
  connection: if one of them is closed while anonymous, the other split
  keeps reading anonymously until it toggles Anon off (or the channel
  is closed everywhere, which clears the state). The button, input and
  bubbles always follow the split you are looking at.
