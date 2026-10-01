# Glossar

| Begriff | Bedeutung |
|---|---|
| **Hive** | Server-Komponente, die gefaltete Deltas annimmt, webt, unterteilt, prüft und signierte Releases verteilt. Trainiert nicht auf Rohdaten. |
| **Hive-Klon** | Weitere Hive-Instanz in der Rolle „Klon“ (Organisation, Intranet, Land) mit eigenem Vertrauensanker. |
| **SLM** | Small Language Model; hier ein Open-Source-Basismodell ≤ ca. 2 B Parameter auf dem Gerät. |
| **Core** | „Tensegrity AI SLM – Core“, der Rust-Kern auf dem Gerät. |
| **Delta (Δθ)** | Änderung der LoRA-Parameter durch lokales Training. |
| **Falten** | Transformation eines Deltas je Vertraulichkeitsstufe vor dem Verlassen des Geräts. |
| **Weben** | Vertrauensgewichtete Zusammenführung (FedAvg × T(a)) im Hive. |
| **Unterteilen** | Zuordnung zu Fachgebieten und Bildung von Experten. |
| **Teilen** | Verteilung signierter Releases über Hive, P2P und Datenträger. |
| **Vertraulichkeitsstufe** | Offen, Vertraulich, Streng geheim, Nur lokal. |
| **Berechtigungsmaske** | 6-Bit-Maske, die jeder Stufe erlaubte Aktionen zuordnet. |
| **Taint** | Regel: Bei kombinierten Inhalten gilt die strengste Stufe. |
| **W_basis / W_logik / W_PI** | Eingefrorene Basis / gemeinsam gelernte Adapter / lokale Adapter für geheimes Wissen. |
| **Slot** | LoRA-Adapter mit Kennung, Fachgebiet, Kategorie und Lebenszyklus-Daten. |
| **Null-Slot** | Slot mit B-Matrix = 0; strukturell vorhanden, ohne Einfluss auf die Ausgabe. |
| **Strukturmeldung** | Meldung einer groben Kategorie aus Streng geheim (gebündelt, mit App-ID). |
| **Prüf-Gate (lokal)** | Vorschlag/automatische Prüfung mit Rollback für gezielte Updates von Streng-geheim-Slots. |
| **Eval-Gate** | Pflichtprüfung eines Kandidaten-Releases im Hive. |
| **T(a)** | Vertrauenslevel einer App-ID in [0, 1]. |
| **f_betreiber** | Betreiberfaktor in [0, 1]; 0 = gesperrt. |
| **Privacy Accountant** | Buchführung über verbrauchtes DP-Budget pro Gerät und Fachgebiet. |
| **TUF** | The Update Framework: Rollen- und Signaturmodell für sichere Updates. |
| **Leerlauftraining** | Training nur bei Laden, ausreichend Akku, Temperatur und Zustimmung. |
