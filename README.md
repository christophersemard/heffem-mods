# Heffem mods: update files

This repository holds the **published files** of the Heffem mods for Football Manager 27 (Heffem Academy Manager and Heffem Nations Draw), so the mods can update themselves. There is **no source code** here.

- Mods page and downloads: [FMScout](https://www.fmscout.com/)
- Contact: Discord **krysler**, Twitter [@Krysler____](https://x.com/Krysler____)

<!-- downloads-section -->
## Downloads / Téléchargements

Latest packages of each mod. These links never change: share them as they are. / Derniers paquets de chaque mod. Ces liens ne changent jamais : partage-les tels quels.

<!-- downloads:academy -->
**Heffem Academy Manager** (1.0.25): [Windows (installer)](https://github.com/christophersemard/heffem-mods/releases/download/academy-latest/Heffem-Academy-Manager-Windows.zip) · [Windows (manual install)](https://github.com/christophersemard/heffem-mods/releases/download/academy-latest/Heffem-Academy-Manager-Windows-manual.zip) · [Mac (Apple Silicon)](https://github.com/christophersemard/heffem-mods/releases/download/academy-latest/Heffem-Academy-Manager-Mac.zip)
<!-- /downloads:academy -->

<!-- downloads:tirage -->
**Heffem Nations Draw** (0.6.38): [Windows (installer)](https://github.com/christophersemard/heffem-mods/releases/download/tirage-latest/Heffem-Nations-Draw-Windows.zip) · [Windows (manual install)](https://github.com/christophersemard/heffem-mods/releases/download/tirage-latest/Heffem-Nations-Draw-Windows-manual.zip) · [Mac (Apple Silicon)](https://github.com/christophersemard/heffem-mods/releases/download/tirage-latest/Heffem-Nations-Draw-Mac.zip)
<!-- /downloads:tirage -->
<!-- /downloads-section -->

## What's in here

| File | What it is |
|---|---|
| `latest.json` | The latest version of each mod: version number, download link, size and SHA-256 fingerprint of its DLL. |
| `latest.json.sig` | The digital signature of `latest.json`. |
| [Releases](https://github.com/christophersemard/heffem-mods/releases) | One release per mod version (`academy-0.9.11`, `tirage-0.6.7`...), with the mod's DLL attached. |

## How automatic updates work

1. When a mod starts, 2 minutes later, then every 30 minutes while the game runs, it downloads `latest.json` and its signature in the background. Nothing is sent: no account, no save data, no personal information. The request only reads public files from GitHub.
2. The mod checks the signature with the public key built into it (below). An unsigned or modified file is refused.
3. If a newer version exists, the mod downloads its DLL from the release, checks its size and SHA-256 fingerprint against the signed `latest.json`, and puts it aside (`BepInEx/HeffemUpdates`). The DLL in use is never touched while the game runs.
4. On the next game start, a small loader module (`BepInEx/patchers/HeffemUpdater.dll`, installed with the mods) swaps in the new DLL before the mods load, and keeps the previous one in `BepInEx/HeffemUpdates/previous`.

**Automatic updates are on by default.** To turn them off, set `MisesAJourAutomatiques = false` in `BepInEx/config/fr.heffem.academy.cfg` (or `fr.heffem.tirage.cfg`); the mod then never connects to anything. Manual downloads (below and on the mods page) stay available.

**To go back to the previous version**: copy `BepInEx/HeffemUpdates/previous/<mod>.dll` back into `BepInEx/plugins`, then turn updates off (`MisesAJourAutomatiques = false`).

Changes to the mod loader itself (BepInEx) are not delivered this way: they need a new installer from the mods page.

## Signature details

- Algorithm: ECDSA P-256 with SHA-256, over the exact bytes of `latest.json`.
- `latest.json.sig`: base64 of the raw signature (IEEE P1363, r‖s, 64 bytes).
- Public key (SubjectPublicKeyInfo, base64), the same one built into the mods:

```
MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAETg69N6QBHkgbUAD5r+LuhC87/mlcWSQghmaRMtmFjFcuEQbSMO4cctzsGedGOziTLG9+dHOupquroUHHTKA8mg==
```

Check it yourself (Python 3 with the `cryptography` package):

```python
import base64, hashlib
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.primitives.asymmetric import ec, utils

KEY = "MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAETg69N6QBHkgbUAD5r+LuhC87/mlcWSQghmaRMtmFjFcuEQbSMO4cctzsGedGOziTLG9+dHOupquroUHHTKA8mg=="
data = open("latest.json", "rb").read()
sig = base64.b64decode(open("latest.json.sig").read().strip())
key = serialization.load_der_public_key(base64.b64decode(KEY))
der = utils.encode_dss_signature(int.from_bytes(sig[:32], "big"), int.from_bytes(sig[32:], "big"))
key.verify(der, data, ec.ECDSA(hashes.SHA256()))  # raises InvalidSignature if it doesn't match
print("signature OK")
# Then compare a downloaded DLL with its fingerprint in latest.json:
# hashlib.sha256(open("HeffemAcademy.dll", "rb").read()).hexdigest()
```

## Notes

- These mods are provided as is. They have nothing to do with Sports Interactive or SEGA.
- The mods use the BepInEx mod loader (LGPL-2.1), distributed with the installers on the mods page, not here.

---

## En français

Ce dépôt contient seulement les **fichiers publiés** des mods Heffem pour Football Manager 27 (Académie et Tirage des Nations), pour que les mods se mettent à jour seuls. **Aucun code source.**

- Au démarrage, le mod lit `latest.json` et sa signature, sans rien envoyer (ni compte, ni partie, ni donnée personnelle).
- La signature est vérifiée avec la clé publique intégrée au mod : un fichier non signé ou modifié est refusé.
- La nouvelle DLL est téléchargée, contrôlée (taille, empreinte SHA-256), mise de côté, puis installée au lancement suivant du jeu. L'ancienne est gardée dans `BepInEx/HeffemUpdates/previous`.
- **Mises à jour automatiques activées par défaut.** Pour les couper : `MisesAJourAutomatiques = false` dans `BepInEx/config/fr.heffem.academy.cfg` (ou `fr.heffem.tirage.cfg`) ; le mod ne se connecte alors jamais. Les téléchargements manuels restent disponibles.
