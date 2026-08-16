# Spectral

A two-player word/phrase game. One player names something that sits at a hidden point
on a sliding scale between two words/phrases; the other guesses where it was. Closest
guess scores most.

Spectral is an imitation of Spectrum, a game I played online during lockdown at
playfromhome.us, which no longer seems to be running. Rebuilt from memory, so the
details are probably wrong. Both it and this likely owe a debt to Wavelength by
Alex Hague, Justin Vickers and Wolfgang Warsch (CMYK, 2019).

Game mechanics aren't copyrightable, so rebuilding a remembered game is fine. The
name is the softer spot: there is an unrelated 2024 deduction board game also
called Spectral. Different genre, so confusion is unlikely, but worth knowing if
this ever goes beyond a link shared between two people.

Claude built this prototype, responding to my descriptions and requests for edits to the visual design, colour scheme and gameplay. It has much potential for expansion, including to increase the available 'spectral pairs' to avoid repetition and overfamiliarity. The game I remember, which this is based on, was multiplayer, so that's another area for development.

## How a game runs

Three rounds each, and two links change hands:

1. **Player 1** writes three labels and sends a link.
2. **Player 2** opens it, guesses Player 1's three, writes three of their own,
   and sends a link back.
3. **Player 1** opens that, guesses Player 2's three, and sees the final score.

A third link exists only if Player 1 wants Player 2 to see the results table
rather than just being told the number.

Scoring is 1000 points within 4 percentage points of the target, then 800, 600,
400 and 200 as the guess drifts, and nothing beyond 40 away. You score for your
own guesses only.

## How the state travels

There is no server and no database. The entire game is JSON, compressed to
base64url and hung off the URL after a `#`.

Two things follow from that. Anything after `#` is never sent to the server, so
the host never sees a game. And the app is completely stateless — it works from
any device, in any browser, with nothing stored.

The trade-off: the hidden target positions ride in the link too, because scoring
needs the actual distance. Obfuscation, not secrecy. Someone determined
could decode a link and read the answers. Fine for a game between two people who
know and trust each other.

## Editing the spectrum pairs

There are 58 pairs and a game draws six. They live in the `PAIRS` array near
the top of the script in `public/index.html`. Each entry is `["TOP OF THE BAR", "BOTTOM OF THE BAR"]`.

Worth adding more to this eventually and high potential for tailoring the pairs — in-jokes and shared references can be a highlight of gameplay. Add as many as you like; the app picks at random and never repeats a pair within a single game.

## Local preview

No build step. Open `public/index.html` in a browser, or:

```
python3 -m http.server 8000 --directory public
```

then visit `http://localhost:8000`.

## Deploying

Cloudflare Workers, serving static assets. Connect the repo in the Cloudflare
dashboard, leave the build command empty, and push. `wrangler.jsonc` points at
`./public` and that's all the configuration needed.

## Accessibility notes

Every colour combination in the app has been checked: text clears 4.5:1 and
interface shapes clear 3:1 against their neighbours. The pale bar and slider
button rely on their dark outline for that, not their fill.

The one deliberate exception is the SPECTRAL wordmark, which sits below the
threshold and is covered by WCAG's logotype exemption. Don't copy that pattern
onto text that carries information.

The slider is a real ARIA slider: reachable by tab, movable with arrow keys,
Page Up and Page Down for bigger jumps, Home and End for the extremes.