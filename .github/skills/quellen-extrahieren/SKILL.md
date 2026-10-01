---
name: quellen-extrahieren
description: "Use when reading PDF/DOCX source material for Tensegrity AI (whitepaper versions, pitch decks, chat exports) into text for analysis. Extracts with PyMuPDF into a local, git-ignored folder and never into repos. Keywords: pdf lesen, docx, extrahieren, quellen, whitepaper pdf, pymupdf."
---
# Quellen extrahieren

## Wann
Neue oder geänderte Quelldokumente (PDF/DOCX) müssen gelesen werden.

## Schritte
1. Python-Umgebung mit `pymupdf` (PDF) bzw. `python-docx` (DOCX) bereitstellen.
2. Text seitenweise extrahieren, Seitenmarker `===== Seite n =====` einfügen; große Dateien in Teile von ca. 25 Seiten splitten.
3. Ausgabe in einen Ordner `_extracted/` **außerhalb** der Repos bzw. in einen per `.gitignore` ausgeschlossenen Pfad.
4. Bei sehr langen Texten Teile parallel durch Explore-Subagenten zusammenfassen lassen.
5. Vor Übernahme in Repos: Firmen-, Bank-, Steuer- und Personendaten entfernen.

```python
import fitz, pathlib
src = pathlib.Path(r"<pdf>")
out = pathlib.Path(r"<ziel>/_extracted") / (src.stem + ".txt")
with fitz.open(src) as doc:
    out.write_text("".join(f"\n===== Seite {i+1} =====\n{p.get_text()}" for i, p in enumerate(doc)), encoding="utf-8")
```

## Nie
Extrakte committen oder veröffentlichen.
