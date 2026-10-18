# Spectra Studio — Official Releases

This repository hosts the **signed official builds** of Spectra Studio, a GPU photo and video editing and colour-grading suite by **Miguel**.
The source code is private; only release files are published here.

## Use it in your browser
**https://spectrastudio.online/ no install needed, and it works offline after the first visit.

## Downloads
Open the [latest release](https://github.com/dm67er34/spectra-studio-releases/releases/latest):

| Platform | File |
|---|---|
| Windows (installer) | `SpectraStudio-Setup-<version>-x64.exe` |
| Windows (portable) | `SpectraStudio-<version>-portable.exe` |
| Linux | `SpectraStudio-<version>-linux-x86_64.AppImage`, `.deb`, `.tar.gz` |
| Web | `spectra-studio-web-<version>.zip` (serve the folder over HTTPS) |

Installed apps update themselves from this repository automatically.

## How updates are verified
Every release contains **`updates.json`**, which lists the SHA-256 and size of every file and is signed with the publisher's **Ed25519 release key**.
Before installing anything, the app:
1. checks the `updates.json` signature against the public key built into the app, and refuses forged releases and downgrades;
2. downloads the update and checks its SHA-256 and size against the signed list;
3. scans it with the system antivirus;
4. installs it only if every check passes. Anything else is deleted.

The web package also contains `integrity.json` (a signed SHA-256 of every app file), which the app re-checks at start-up.

**Release public key (Ed25519, base64):**
```
Jcay02U4AS+MiJM759zrMAoMoRYYlUsrA49pXdVGYkw=
```

### Verify a release yourself (Node.js 18+)
```js
const crypto = require('crypto'), fs = require('fs');
const m = JSON.parse(fs.readFileSync('updates.json'));
const { sig, ...rest } = m;
const body = { app: rest.app, version: rest.version, released: rest.released, minimumVersion: rest.minimumVersion, notes: rest.notes, assets: rest.assets };
const key = crypto.createPublicKey({ key: { kty: 'OKP', crv: 'Ed25519', x: Buffer.from('Jcay02U4AS+MiJM759zrMAoMoRYYlUsrA49pXdVGYkw=', 'base64').toString('base64url') }, format: 'jwk' });
console.log('signature valid:', crypto.verify(null, Buffer.from(JSON.stringify(body)), key, Buffer.from(sig, 'base64')));
for (const [name, a] of Object.entries(m.assets)) if (fs.existsSync(name))
  console.log(name, crypto.createHash('sha256').update(fs.readFileSync(name)).digest('hex') === a.sha256 ? 'OK' : 'MISMATCH');
```

© Miguel. All rights reserved.
