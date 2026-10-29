# Tarot Studio

A Windows C++17 offline tarot deck browser and reflective reading application. The 78 Rider–Waite–Smith images are packed in `tarot.assets`.

## Using the app

- Select a spread and press **New reading**. Available spreads: one card; past/present/future; situation/action/next step; a five-card relationship spread; and a ten-card Celtic Cross-inspired spread. The latter uses a **non-overlapping grid**, not a traditional crossed-card physical arrangement. Position labels and prompts appear for the selected card.
- Click a card, use the accessible reading-card dropdown, or press Ctrl+Left / Ctrl+Right while focus is outside text fields and dropdowns. Use the reversal checkbox to choose whether the next draw may include inverted cards.
- Browse the deck by name or filter by arcana/suit. Use **Return to reading** to restore the most recent draw after browsing. Search uses Windows linguistic case- and accent-insensitive matching. The browser shows both orientations; a reading shows the selected orientation. Switch languages with PT / EN.
- Write in the journal panel. **Save notes** and **Open notes** use UTF-8 text files at a location you choose. **Export reading** saves the cards, positions, interpretations and current notes together as a text file. Notes are not saved automatically or encrypted. **Save session** stores the current cards, positions, orientations, selection and notes in a checksummed local `.trs` file; **Open session** restores them. Sessions store card indices and use the current installed interpretation text, not a snapshot of it. Changing the bundled deck order in a future version would require a migration. A close/open warning appears when notes have unsaved edits. Notes remain in the panel across new draws; clear them manually if desired. No information is sent online.

Readings are for reflection and entertainment, not predictions or professional advice. Every card includes English and Portuguese upright/reversed summaries, a card-specific reflection question and an original bilingual commentary. Both languages include concise imagery descriptions and orientation-specific keywords. These interpretations are not a complete scholarly or professional reference; independent bilingual expert review remains outstanding. See `EDITORIAL_SPEC.md` and `REVIEW_78_CARDS.csv`.

## Build in Code::Blocks

1. Install Code::Blocks and a **64-bit MinGW-w64** toolchain with C++17 support (GCC 9 or later recommended). Configure the GNU GCC compiler under Settings → Compiler → Toolchain executables.
2. Open `TarotStudio.cbp`, select **Release**, and choose Build → Build and run. The executable is generated in the project folder, alongside `tarot.assets`. Keep those two files together when distributing the program.
3. If your compiler uses dynamic GCC runtime libraries, deploy the required MinGW DLLs next to the executable, or build with the static runtime options appropriate for your toolchain.

Manual command from the project folder:

```
windres tarot.rc -O coff -o tarot-resource.o
g++ -std=c++17 -O2 -finput-charset=UTF-8 -municode -mwindows main.cpp tarot-resource.o -o TarotStudio.exe -lgdiplus -lole32 -lgdi32 -luser32 -lcomdlg32
```

The included 64-bit `TarotStudio.exe` was cross-compiled with MinGW-w64 and statically linked to the GCC runtime; it has not been run on Windows. You may rebuild it using the included project. Windows provides GDI+ and OLE. No OpenGL or additional GUI library is required. The interface uses Win32 controls and GDI+; the minimum window size prevents controls and cards from overlapping. The interpretations panel scrolls when needed.

## Data and attribution

- Card artwork: Pamela Colman Smith, Rider–Waite–Smith deck, 1909. The image source describes the scans as public domain: https://github.com/strokirk/tarot (via Wikimedia Commons). Public-domain status may vary by jurisdiction; verify before redistribution.
- English card data, including original imagery descriptions and many keywords and short meanings: adapted from the MIT-licensed Tarot Muse `deck.json` by strokirk, https://github.com/strokirk/tarot. The original license is included as `TAROT_MUSE_LICENSE`.
- Portuguese card names, imagery, keywords and card-specific upright/reversed summaries were prepared for this application; they are concise original interpretations, not direct translations of the English material. See `RESEARCH.md` for scope and proposed editorial improvements.

## Asset format

`tarot.assets` is a little-endian file: eight-byte magic `TAROT01\0`, a 32-bit count (78), then for each card a 32-bit JPEG byte length followed by that JPEG. The order matches `cards.h`: 22 Major Arcana, then Wands, Cups, Swords and Pentacles (Ace through King). The binary contains images; names and interpretations are compiled from `cards.h`, `expanded.h`, `questions_pt.h`, `imagery_pt.h`, `keywords_pt.h` and `commentary.h`. The loader validates count and image sizes. `generate_content.py` regenerates the compiled content headers from local UTF-8 sources; `setup.py` is a compatibility wrapper. Neither rebuilds the binary pack.

## Notes

The source has been cross-compiled successfully with MinGW-w64. Windows runtime, accessibility, DPI and visual testing are still needed. The application declares per-monitor DPI awareness and scales fonts and layout when Windows reports a DPI change. This has not yet been verified on Windows, and the minimum window size can exceed the work area on small high-DPI screens.

## Flicker fix

The main window uses an off-screen back buffer, suppresses background erase, and clips child controls from parent painting to reduce flashing during selection and resizing. This build was cross-compiled but the flicker fix has not been tested on Windows; please check it on your display and DPI settings.

## Application icon

The multi-size `tarot.ico` is embedded in the executable by `tarot.rc` and selected for both the large application icon and the small title-bar icon. `resource.h`, `tarot.rc`, and `tarot.ico` are required when rebuilding; no separate icon file is needed when running the compiled program. Windows Explorer may cache old icons after replacing an executable. The icon artwork in `icon_art.png` was AI-generated for this project.

## Reliability update and remaining work

Writes are staged in the destination directory and flushed before Windows replacement; an overwrite can still fail, and guarantees vary by filesystem. Check error messages and keep backups. An unexported/unsaved reading prompts before replacement or closing. The app asks whether to carry existing journal notes into a new reading. Notes included in a reading export are still marked unsaved until separately saved or saved in a session.

`asset_tool.py verify` checks the provided pack against `asset_manifest.json`. The same script can extract its 78 JPEGs and rebuild an identical pack; run it without arguments for usage. These hashes pin the current pack bytes, not the legal status or original upstream revision. Runtime image decoding is bounded in size but does not enforce manifest hashes.

Still required: Windows runtime and fault-injection testing, Windows verification of per-monitor DPI scaling and small-screen responsive layout, accessibility/screen-reader verification, independent bilingual tarot editorial review, full commentary fields and verified source/licensing provenance. This is not yet a professional-release certification.

## User guide

Open `Manual.html` in a web browser. It is a self-contained English/Portuguese dark-mode guide; its external resource links require an internet connection. The guide is also usable offline without following those links.
