# Projektkontext für Claude

Sprache: immer auf Deutsch antworten.

## Projekt
**Rotorsturm** – eigener senkrecht scrollender Hubschrauber-Shooter im Stil der 80er-Arcades (Genre, KEIN Nachbau eines
bestimmten Spiels: eigene Sprites, Gegner, Gelände, Musik; keine Namen bekannter Spiele verwenden).

- Reines HTML/JS ohne Build: `index.html` (Menüs, Overlays), `game.js` (alles andere)
- Ziel: iPhone/iPad (Safari, Home-Bildschirm) und Desktop; Live über GitHub Pages https://theossel-sys.github.io/rotorsturm/
- Logische Auflösung 240 px breit, Höhe nach Bildschirmformat (340–560), pixelgenau hochskaliert
- Testzugang: `window.__rotor` (G, state, start, update(dt)) – Playwright-Schnelldurchlauf mit unverwundbarem Heli
- Rekord in `localStorage` (`rotorsturm-hi`), Ton an/aus (`rotorsturm-ton`)

## Inhalt
- 4 Zonen (je 290 Reihen à 8 px, Scroll 30 px/s): Küste → Zerstörer, Wald/Fluss → Festungspanzer, Industrie → Bunkerwerk,
  Flugfeld → Sturmbrecher; danach neue Runde schwieriger (G.loop)
- Gegner: tank, truck (Extras), boat, bunker (Boden), jet, copter (Luft); Extras P (Feuerkraft 1–4), B (Bombe), S (Schild)
- Steuerung: relatives Ziehen irgendwo, Auto-Feuer, Bombenknopf; PC Pfeile/WASD, Leertaste, P
- Audio: Web-Audio-Chiptune (a-Moll-Schleife, Boss-Modus schneller), SFX

## Grafik
- Code-Sprites (Pixel-Strings) als Fallback; Bilder aus Draw Things (lokale App des Users, erzeugt in seiner lokalen
  Session) in `assets/`, geladen in game.js (`loadArt`). Bosse: Bild + Silhouetten-Schatten + zielende Rohre.
- Neue Bilder: Prompts in `art/PROMPTS.md`, Größen in `art/manifest.json`, Rohbilder nach `art/incoming/`,
  `python3 tools/import_art.py` → `assets/` (Hintergrund-Bilder als JPEG).

## Verlauf
- 2026-11-17: entstanden im Repo Dragon-Trader (Branch), mit Draw-Things-Grafiken, dann hierher umgezogen.
- Feedback „Heli verschwindet manchmal, Hintergrund nicht schön“: Unverwundbarkeit blinkt jetzt halbtransparent statt
  unsichtbar; bei Größenänderung (Safari-Leiste) wird der Heli ins Bild geklemmt. Neuer Gelände-Renderer
  (renderChunk/drawTerrain, Blöcke à 32 Reihen, Cache): Rauschtexturen, weiche Küste mit Strand/Brandung/Tiefwasser,
  Flüsse mit Ufern, durchgehende Straßen + Brücken, Baumkronen mit Schatten, Satteldächer, Felsen, Industrie-Deko,
  Wasser-Glitzern. Kacheldaten (rows) + meta[] je Reihe bleiben die Spiellogik.
- Stil-Wunsch (Referenzbild eines Arcade-Klassikers, nur als Richtung – nicht nachbauen): breite Straßen, weniger
  Schnickschnack. Umgesetzt: Straßen 3 Kacheln breit (2 Spuren, Mittelstreifen, Randlinien), fast gerade (Drift 3 %),
  breite Querstraßen (Küste alle 48 Reihen, Wald 44 mit breiten Brücken, Industrie 26), ruhigere flächige Texturen,
  kaum Büsche, lockerer Wald mit großen Kronen, weniger Industrie-Deko, eine Brandungslinie.
- Bug: Heli ab dem 2. Leben unsichtbar (und unverwundbar, sammelte keine Extras): Respawn-Timer endete bei -0,0x statt 0, Prüfungen nutzen !p.dead. Fix: p.dead = 0 beim Wiedereinstieg; Regressionstest (Treffer → Respawn → sichtbar/treffbar) per Playwright.
- Feedback (Kraterläufer, gleiche Ursache) „Knöpfe verfehlt → Zoom hängt“: gleicher Zoom-Schutz wie dort: touchend/touchmove/gesture*/dblclick außerhalb .ov blockiert, Trefferfläche ±16 px um BOMBE/Pause, Pause per pointerdown, Zoom-Reset über visualViewport.scale. Ziehen-Steuerung und Respawn-Test weiter ok.
