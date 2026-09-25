# Bundled UI font

## Runtime contract

`GameUiFont.Current` loads `Assets/Resources/Fonts/NanumGothic-Regular.ttf`. At the ROOM1–2 commit checkpoint, ROOM1/2 views, inventory, timer, exit overlays and the field modal fallback share this font instead of creating fonts from OS names. Font size remains a property of each UI Text component.

Missing font resources fail explicitly rather than silently showing unreadable UI.

## Provenance and redistribution

- Font: Nanum Gothic Regular, unmodified upstream TTF.
- Copyright: 2010 NHN Corporation; reserved font names are listed in the accompanying notice.
- License: SIL Open Font License 1.1. The upstream license permits bundling with software subject to its conditions; the font is not sold separately or renamed/modified here.
- Pinned source revision: `google/fonts` commit `5e35378e6bda803962ee6fd257e444a7d459660d`.
- Font source: https://raw.githubusercontent.com/google/fonts/5e35378e6bda803962ee6fd257e444a7d459660d/ofl/nanumgothic/NanumGothic-Regular.ttf
- License source: https://raw.githubusercontent.com/google/fonts/5e35378e6bda803962ee6fd257e444a7d459660d/ofl/nanumgothic/OFL.txt
- TTF SHA-256: `76f45ef4a6bcff344c837c95a7dcc26e017e38b5846d5ae0cdcb5b86be2e2d31`.
- Full copyright/license notice: `Assets/StreamingAssets/ThirdParty/NanumGothic-OFL.txt`. Keep this file in distributions; it is copied into the player StreamingAssets directory.

Browser screenshots, not OS/editor font availability, are the acceptance surface. Check Korean hints, inventory, item choices, lock feedback and endings; glyph coverage/layout outside the sampled screens still requires full-run QA.
