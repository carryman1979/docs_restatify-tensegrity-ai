# Tensegrity AI – Zukunfts-Backlog

Ideen, die bewusst **nicht** im MVP umgesetzt werden, aber architektonisch vorbereitet werden sollen.
Einträge erhalten Requirement-IDs (`REQ-STK-…`), sobald sie für eine Umsetzung eingeplant werden.

| ID | Titel | Status | Priorität | Vorbereitung im MVP |
|---|---|---|---|---|
| BL-001 | Teilen im Vertrauensraum | Idee | Fern | Identische Slot-Struktur, Geräte-Schlüssel, Paketformat |
| BL-002 | Team-Chat „Mitglieder einladen“ (verteiltes Denken) | Idee | Fern | Engine-Abstraktion, Geräte-Schlüssel, P2P-Kanal |
| BL-003 | Blindes Themen-Matching (Private Set Intersection) | Idee | Fern | – |
| BL-004 | Anonyme Berechtigungsnachweise für Struktur-Meldungen | Idee | Mittel | Getrennte Kennung für Struktur vs. Deltas |
| BL-005 | Ablaufdatum für Knoten + Archiv (Langzeitgedächtnis) | Idee | Mittel | Metadaten pro Knoten (Erstellt, Zugriff, Ablauf), Release-Format mit Lösch-Einträgen |

---

## BL-001 – Teilen im Vertrauensraum

**Ziel:** Wissen aus „Streng geheim“ oder „Nur lokal“ gezielt und gewollt mit ausgewählten Geräten teilen
(z. B. F&E-Partner, zweite Kanzlei, eigenes Zweitgerät).

**Kernregeln**
- Nur durch explizite Einladung eines Berechtigten (QR-Code, Einladungslink, Organisations-Zertifikat) – keine automatische Entdeckung.
- Ende-zu-Ende-verschlüsselt mit dem Schlüssel des Empfängers; Transport direkt per P2P, über Relay als undurchsichtiges Paket oder per USB.
- Der Hive erfährt weder Inhalt, Thema noch Teilnehmer.
- Adapter sind dank identischer Hive-Slots zusammenführbar (W_PI von A und B).
- Berechtigungs-Maske Bit 5 („Teilen im Vertrauensraum“).

**Risiken / offene Fragen**
- Adapter-Gewichte sind praktisch so sensibel wie die Quelldokumente (Memorization/Extraktion) → Warnhinweis, Bestätigung, Protokoll.
- Geteiltes Wissen ist nicht zurückholbar; Widerruf stoppt nur künftige Updates.
- Zusammenführungsverfahren (gewichteter Mittelwert vs. Weitertraining) und Konfliktauflösung.
- Rollen im Raum (Owner, Mitglied, Leser) und Übergabe der Ownership.

---

## BL-002 – Team-Chat „Mitglieder einladen“ (verteiltes Denken)

**Ziel:** Mehrere Teilnehmer arbeiten in einem gemeinsamen Chat. Die lokalen SLMs aller Teilnehmer verarbeiten
die Eingaben aller und stimmen sich untereinander über die Antwort ab. So wird die „Thinking-Last“ bei
Teamarbeit auf mehrere Geräte verteilt und das Fachwissen der einzelnen Geräte (lokale Experten, W_PI) kombiniert.

**Ablauf (Skizze)**
1. Ersteller öffnet einen Team-Chat und lädt Mitglieder explizit ein (wie BL-001).
2. Eine Nachricht wird verschlüsselt an alle Mitglieds-Geräte verteilt.
3. Jedes Gerät erzeugt lokal einen Antwortbeitrag bzw. Teilschritt (Aufgabenteilung nach Fachgebiet/Last).
4. Abstimmungsrunde: Beiträge werden ausgetauscht, bewertet und zu einer gemeinsamen Antwort konsolidiert
   (z. B. Moderator-Gerät, Mehrheits-/Konfidenz-Voting, Debattenrunde mit Abbruchkriterium).
5. Ergebnis wird allen angezeigt, inkl. Herkunft der Teilbeiträge.

**Kernregeln**
- Vertraulichkeit: Für den Team-Chat gilt die **strengste Stufe** aller eingebrachten Inhalte.
- Lokales Geheimwissen eines Mitglieds fließt nur als **Antwortbeitrag** (Text) ein, nicht als Gewichte –
  außer das Mitglied gibt es ausdrücklich frei.
- Lernen aus dem Team-Chat nur lokal pro Gerät; Delta-Übermittlung an den Hive richtet sich nach der Chat-Stufe.
- Funktioniert ohne Hive (reines P2P/LAN) und mit Hive als Relay für nicht lesbare Pakete.

**Risiken / offene Fragen**
- Latenz und Verfügbarkeit (Mitglied offline, Mobilgerät im Energiesparmodus) → Timeouts, Teilantworten.
- Konsens-Verfahren und Umgang mit widersprüchlichen Beiträgen (Halluzinationen eines Teilnehmers).
- Indirekter Abfluss: Ein Antwortbeitrag kann Geheimwissen im Klartext enthalten → lokale Prüfung vor dem Senden.
- Lastverteilung fair gestalten (Akku, Rechenleistung, Opt-out pro Gerät).
- Rollen- und Rechtemodell (wer lädt ein, wer darf Stufe ändern, wer moderiert).

---

## BL-003 – Blindes Themen-Matching (Private Set Intersection)

**Ziel:** Zwei Parteien erfahren nur, ob sie ein gemeinsames Fall-/Themen-Token haben – sonst nichts.
Grundlage, um mögliche Partner für BL-001 zu finden.

**Kernregeln:** Nur per ausdrücklicher Zustimmung („auffindbar für Thema X“), nie Standard.

---

## BL-004 – Anonyme Berechtigungsnachweise für Struktur-Meldungen

**Ziel:** Struktur-Meldungen der Stufe „Streng geheim“ sind nicht mit der Organisation verknüpfbar,
der Hive kann trotzdem Missbrauch begrenzen (Rate-Limits).

**Ansatz:** Blind Tokens / anonyme Credentials (z. B. Privacy-Pass-artig). Im MVP ersetzt durch ein vom Hive
ausgestelltes Kontingent pro Gerät und eine von der App-ID getrennte, rotierende Kennung.

---

## BL-005 – Ablaufdatum für Knoten + Archiv (Langzeitgedächtnis)

**Ziel:** Kurzlebiges, schnell gelerntes Wissen (z. B. aktuelle Ereignisse: „Welches Kleid trug die Braut bei der
Prominenten-Hochzeit?“) soll das Modell nicht dauerhaft belasten. Nach Ablauf wird der Knoten aus dem aktiven Netz
entfernt, das Wissen in ein Archiv (Langzeitgedächtnis) verschoben; ein Verweis am ehemaligen Knotenende zeigt
auf den Archiv-Eintrag.

**Skizze**
- Jeder Knoten/Experten-Slot erhält Lebenszyklus-Metadaten: Erstellt, letzter Zugriff, Zugriffshäufigkeit, Ablaufdatum bzw. Abklingfunktion.
- Der Hive entscheidet über Ablauf (Betreiber-Regeln pro Kategorie, z. B. „Tagesgeschehen: 30 Tage“).
- Archivierung: Gewichte bzw. extrahiertes Faktenwissen wandern in einen Abrufspeicher (z. B. Vektor-/Dokumentenarchiv, RAG).
- Verweis-Stub im Modell/Router: Trifft eine Anfrage das archivierte Thema, wird das Archiv abgefragt statt des Knotens.
- Reaktivierung: Steigt das Interesse wieder, kann der Hive das Wissen erneut als aktiven Knoten einweben.
- Verteilung: Releases enthalten Lösch-/Archivierungs-Einträge; Geräte archivieren lokal oder laden Archive bei Bedarf.

**Nutzen**
- Schlanke Modelle, geringerer Speicher-/Rechenbedarf auf Edge-Geräten.
- Unterstützt DSGVO-Löschpflichten (Art. 17) für personenbezogene, öffentlich geteilte Inhalte.

**Offene Fragen**
- Wie lassen sich LoRA-Gewichte sauber „ausbauen“, ohne benachbartes Wissen zu beschädigen (eigene Slots pro Ereignis/Trend?).
- Format und Ort des Archivs (Hive, P2P, lokal) und Zugriff offline.
- Gilt der Ablauf auch für lokale Stufen („Streng geheim“, „Nur lokal“) – nur auf Wunsch des Nutzers.
