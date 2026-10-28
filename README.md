# Leech Calculator (Mirror)

A one-to-one mirror of SrslyGuys' Leech Calculator for [MapleLegends](https://legends.ml). The original service, [leechcalculator.com](https://leechcalculator.com/), has stopped working, so this copy keeps it available.

## What it does

The calculator tracks EXP gained while leeching (paying another player to level you) and works out whether a session was profitable.

- Look up a character by IGN and record its level and EXP at the start of a session.
- Look it up again at the end to get the EXP gained.
- Compare the EXP gained against the meso cost to get an hourly or per-EXP rate.
- Keep a history of sessions and totals for profit and loss. Everything is stored in the browser's `localStorage`, and the history can be exported to CSV.
- View the EXP table (`exptable.csv`), which the level calculations use.

It is a static site (`index.html`, `calcscript.js`, `style.css`) built on jQuery and jQuery UI, with no build step. Open `index.html` or serve the folder with any static host. It is currently deployed on Vercel.

## Proxy

Character data comes from the MapleLegends site, which the browser can't call directly because of CORS. The original service got around this with a CORS proxy hosted on Google Cloud Functions:

```
https://us-central1-maplelegendscorsproxy.cloudfunctions.net/app/playerdata/srslyguys
```

That proxy is broken, which is why the original site stopped working. I mirrored it as a new proxy, [ML-proxy](https://github.com/kxvxnc/ml-proxy), which this app calls instead:

```js
// calcscript.js
const infoUrl = "https://ml-proxy.vercel.app/api/character";
```

Requests are made as `GET ${infoUrl}?name=<ign>`. The proxy fetches the character's data and returns it as JSON with CORS headers.

## Domain change

MapleLegends moved from `maplelegends.com` to **`legends.ml`**. Every reference to the old domain has been updated:

| Where | URL |
| --- | --- |
| `index.html` – logo link | `https://legends.ml` |
| `index.html` – ranking link | `https://legends.ml/ranking/all?search=srslyguys` |
| `index.html` – EXP table source | `https://legends.ml/lib/exptable` |
| `calcscript.js` – avatar images | `https://legends.ml/api/getavatar?name=` |
| `calcscript.js` – job icons | `https://legends.ml/static/images/rank/<job>.png` |

No `maplelegends.com` references remain in the code.

## Known issues

- `index.html` includes `jquery.color.min.js`, but that file is not in the repository, so the script tag 404s.
