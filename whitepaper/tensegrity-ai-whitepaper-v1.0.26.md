# Tensegrity AI – Whitepaper

**Mathematisch fundiertes föderiertes Lernen mit Vertraulichkeitsstufen und Datensouveränität**

| Feld | Wert |
|---|---|
| Version | 1.0.26 |
| Stand | 2026-10-01 |
| Vorgänger | V1.0.25 (PDF, 09.07.2026) |
| Autor (Konzept) | Thomas Hoffermann |
| Lizenz | Apache License 2.0 (siehe `LICENSE`) |
| Status | Öffentliche Referenz für die Open-Source-Implementierung |

> **Leitsatz:** „All you need is attention, but forget what I said.“ – angelehnt an Vaswani et al. (2017).

---

## Änderungen gegenüber V1.0.25

| # | Abschnitt | Änderung | Grund |
|---|---|---|---|
| C1 | 2.2 / 5.2 | Rauschen wird mit **σ** skaliert, nicht mit ε: `y = K(clip(Δθ)) + σ·N(0, I_k)` | In V1.0.25 stand `ε·N(0,I)`; damit gilt die DP-Garantie nicht. |
| C2 | 5.2 | Beispielwert korrigiert: ε = 1/√1024 = 0,03125, δ = 10⁻⁵, Δ₂ = 1 → **σ ≈ 155** (nicht 14,5) | Rechenfehler. |
| C3 | 2.2 | **Clipping** auf Norm C wird verpflichtend; Sensitivität Δ₂ = 2C (lokales Modell) | Ohne Clipping ist Δ₂ unbeschränkt, „Δ₂ = 1“ war eine unbegründete Annahme. |
| C4 | 2.2 | PCA auf einem einzelnen Vektor ersetzt durch **gemeinsamen Kompressionsoperator K** (LoRA-nativ oder Zufallsprojektion mit Hive-Seed) | PCA ist für einen einzelnen Vektor nicht definiert; Rücktransformation braucht eine gemeinsame Basis. |
| C5 | 5.2 | „Offen“ enthält jetzt die **Rücktransformation** K⁺ | In V1.0.25 fehlte PCA⁻¹, Dimensionen passten nicht. |
| C6 | 5.2 / 9.2.1 | ε = 1/√d nicht mehr als Standard; ε wird **pro Runde per Richtlinie** gesetzt und über alle Runden **akkumuliert** (Privacy Accountant) | Bei d im Millionenbereich wäre σ so groß, dass kein Nutzen bleibt; Komposition über Runden fehlte. |
| C7 | 1–5 | Bitflag B ∈ {0,1} erweitert zu **vier Stufen** + **Berechtigungsmaske** | „Offen“ und „Vertraulich“ teilten sich B = 0; „Nur lokal“ fehlte. |
| C8 | 5.1 | Satz 3 präzisiert: Isolation gilt für **Gewichte**; die Strukturmeldung ist ein **benannter Metadatenkanal** mit Restrisiko | „I = 0“ galt nur für Δθ, nicht für die gesamte Nachricht. |
| C9 | 2.4 / 4.2 | „Neue Knoten (Transformer-Blöcke)“ technisch präzisiert als **LoRA-Slots** in einer Slot-Registry | Transformer-Blöcke werden nicht zur Laufzeit hinzugefügt; LoRA-Slots mit B-Matrix = 0 erfüllen die Semantik exakt. |
| C10 | 5.3 | Satz 5 als **Vermutung mit Annahmen** gekennzeichnet | Ein Beweis lag nicht vor. |
| C11 | 4.3 | Startwerte T(a) vs. R(a) vereinheitlicht; R(a) kann auch **sinken** | Widerspruch 0,05 vs. 0,5; fehlender Abbau bei schlechten Ergebnissen. |
| C12 | 4.4 (neu) | Abwehr von Vergiftung: Normschranke, robuste Aggregation, Eval-Gate | Fehlte. |
| C13 | 9.2.6 | Hive als **ein Produkt mit Rollen** (Root/Klon), Vertrauensanker, Import/Export | Präzisierung der Enterprise-Lösung. |
| C14 | 9.2.9 (neu) | Gezielte Updates für Streng-geheim-Slots mit **lokalem Prüf-Gate** | Präzisierung von 9.2.5. |

---

## 1. Problemstellung

### 1.1 Technologische Basis und Abgrenzung

Tensegrity AI nutzt die etablierte Transformer-Architektur [Vaswani et al., 2017] und offene, effiziente
**Small Language Models (SLMs)** unter freien Lizenzen (Apache-2.0/MIT) als lokale Inferenz- und
Trainingseinheiten auf Edge-Geräten. Die Basisgewichte werden **nicht** verändert; gelernt wird ausschließlich in
**LoRA-Adaptern** [Hu et al., 2021].

Die Innovation liegt nach dem lokalen Trainingsschritt, im Protokoll für

- **Falten** – datenschutzkonforme Transformation der Modell-Deltas Δθ je Vertraulichkeitsstufe,
- **Weben** – reputationsgewichtete Zusammenführung im Hive,
- **Unterteilen** – Aufteilung in Fachgebiets-Experten,
- **Teilen** – signierte Verteilung über Hive, P2P-Mesh und Datenträger.

### 1.2 Herausforderung

Standard-Federated-Learning (FedAvg, [McMahan et al., 2017]) überträgt Modell-Updates, aus denen Rückschlüsse auf
Trainingsdaten möglich sind (Model Inversion, Membership Inference [Fredrikson et al., 2015]). DSGVO und EU AI Act
verlangen nachweisbare Datenminimierung.

### 1.3 Lösungsansatz

1. Lokales Training von LoRA-Adaptern auf Edge-Geräten, bevorzugt in Leerlaufzeiten.
2. **Falten vor der Übertragung** abhängig von der Vertraulichkeitsstufe (Abschnitt 2.1).
3. Übertragung an den **Hive** – Sammel-, Web-, Unterteilungs- und Verteilsystem. Der Hive **trainiert nicht** auf
   Rohdaten; er kombiniert ausschließlich gefaltete Deltas.
4. Vertrauenslevel T(a) ∈ [0,1] pro App-ID steuert das Gewicht beim Weben.
5. Verteilung neuer, signierter Releases an Geräte über Hive, P2P und Datenträger.

---

## 2. Formale Definitionen

### 2.1 Vertraulichkeitsstufen und Berechtigungsmaske (ersetzt Definition 1)

**Definition 1 (Vertraulichkeitsstufe).** Jede Eingabe, jeder Gesprächsverlauf und jeder lokale Slot trägt eine Stufe
s ∈ {OFFEN, VERTRAULICH, STRENG_GEHEIM, NUR_LOKAL} mit der Ordnung
OFFEN < VERTRAULICH < STRENG_GEHEIM < NUR_LOKAL.

**Definition 1a (Berechtigungsmaske).** Jede Stufe wird auf eine Bitmaske M(s) ∈ {0,1}⁶ abgebildet:

| Bit | Bedeutung |
|---|---|
| 0 | Gewichte (Deltas) dürfen das Gerät verlassen |
| 1 | Differential Privacy wird erzwungen |
| 2 | Strukturmeldung (Slot-Kategorie) darf gesendet werden |
| 3 | Gezielte Updates für eigene Slots werden angenommen |
| 4 | Cloud-/SaaS-Inferenz darf genutzt werden |
| 5 | Teilen im Vertrauensraum (reserviert, Backlog BL-001) |

| Stufe | Bit 0 | Bit 1 | Bit 2 | Bit 3 | Bit 4 | Bit 5 |
|---|---|---|---|---|---|---|
| Offen | 1 | 0 | 1 | 1 | 1 | 0 |
| Vertraulich | 1 | 1 | 1 | 1 | 1 | 0 |
| Streng geheim | 0 | – | 1 | 1 | 0 | 0 |
| Nur lokal | 0 | – | 0 | 0 | 0 | 0 |

**Regel (Taint).** Werden Inhalte verschiedener Stufen kombiniert (Gesprächsverlauf, Kontext, Slot), gilt die
**strengste** Stufe: s(x ∪ y) = max(s(x), s(y)). Eine Herabstufung ist nur durch ausdrückliche Nutzeraktion möglich und
wird protokolliert.

**Regel (WebAssembly).** Im Browser-Client sind „Streng geheim“ und „Nur lokal“ gesperrt, weil Inferenz dort in der
Cloud stattfindet (Bit 4).

### 2.2 Falten-Funktion (ersetzt Definition 2 und 3)

**Definition 2 (Clipping).** Für eine Normschranke C > 0:
`clip(Δθ, C) = Δθ · min(1, C / ‖Δθ‖₂)`

**Definition 3 (Kompressionsoperator).** K: ℝ^d → ℝ^k ist eine lineare Abbildung mit ‖K x‖₂ ≤ ‖x‖₂ und bekannter
Rekonstruktion K⁺: ℝ^k → ℝ^d, die **Hive und Gerät gemeinsam** kennen:

- **K_LoRA (Standard):** Das Delta liegt bereits als Faktorpaar (A, B) eines LoRA-Adapters vom Rang r vor; k = r·(d_in + d_out) ≪ d. K ist die Identität auf den Faktoren, K⁺ das Produkt B·A.
- **K_RP (optional):** Zufallsprojektion P ∈ ℝ^{k×d} mit orthonormalen Zeilen (P Pᵀ = I_k), erzeugt aus einem vom Hive pro Runde veröffentlichten Seed. K = P, K⁺ = Pᵀ.

Damit ersetzt K die in V1.0.25 genannte PCA, die für einen einzelnen Vektor nicht definiert ist.

**Definition 4 (Falten).** F(Δθ, s) liefert die Nutzlast, die das Gerät verlässt:

```text
F(Δθ, s) =
  ⊥                                   wenn s = NUR_LOKAL        (keine Nachricht)
  (0, S)                              wenn s = STRENG_GEHEIM    (Null-Delta + Strukturmeldung S)
  K(clip(Δθ, C)) + σ·N(0, I_k)        wenn s = VERTRAULICH      ((ε, δ)-DP)
  K(clip(Δθ, C))                      wenn s = OFFEN            (nur Kompression)
```

Der Hive rekonstruiert mit K⁺. Bei „Offen“ ist das Clipping nicht für die Privatsphäre nötig, sondern begrenzt den
Einfluss einzelner Geräte (Abschnitt 4.4).

### 2.3 Differential Privacy für „Vertraulich“

Im lokalen Modell (Rauschen auf dem Gerät) haben zwei beliebige geclippte Deltas höchstens Abstand 2C, also
**Δ₂ = 2C**. Da ‖K x‖ ≤ ‖x‖, bleibt Δ₂ im komprimierten Raum gültig.

**Satz 1 ((ε, δ)-DP einer Einreichung).** Mit
`σ ≥ Δ₂ · √(2 ln(1,25/δ)) / ε` für ε ∈ (0, 1)
erfüllt die Abbildung Δθ ↦ K(clip(Δθ, C)) + σ·N(0, I_k) die (ε, δ)-Differential-Privacy [Dwork & Roth, 2014].
Für ε ≥ 1 ist σ mit dem analytischen Gauß-Mechanismus zu kalibrieren [Balle & Wang, 2018].

*Beweis:* Gauß-Mechanismus mit L2-Sensitivität Δ₂; Nachverarbeitung (K⁺ im Hive) erhält DP.

**Satz 1a (Komposition).** Reicht ein Gerät T Deltas ein, addieren sich die Budgets. Der Kern führt einen
**Privacy Accountant** (Rényi-DP bzw. zCDP [Mironov, 2017]) pro Gerät und Fachgebiet. Ist das konfigurierte
Gesamtbudget ε_total erreicht, wird bis zum Ende des Zeitfensters nichts mehr eingereicht.

**Rechenbeispiel (Korrektur C2).** ε = 1/√1024 = 0,03125, δ = 10⁻⁵, Δ₂ = 1:
√(2 ln 125 000) ≈ 4,845 → σ ≈ 4,845 / 0,03125 ≈ **155**.

**Parameterwahl (Korrektur C6).** Die Gauß-Garantie hängt nicht von d ab. Eine Kopplung ε = 1/√d ist daher nicht
begründet und macht das Verfahren bei LoRA-Größen (d ≈ 10⁶) nutzlos. Stattdessen:

| Parameter | Standard (Richtlinie, vom Betreiber änderbar) |
|---|---|
| ε pro Einreichung | 1,0 (analytisch kalibriert) |
| δ | 10⁻⁵ bzw. < 1/Anzahl Geräte |
| ε_total pro Gerät und Fachgebiet und 30 Tage | 8,0 |
| C | aus dem Median der beobachteten Normen der Vorrunde |
| k | über Rang r des Adapters (typisch r = 8…16) |

Lokale DP kostet viel Nutzen. Deshalb wird „Vertraulich“ ab Phase 7 optional um **Secure Aggregation**
[Bonawitz et al., 2017] ergänzt, sodass weniger Rauschen pro Gerät nötig ist. Das ist ein Ausbauschritt, kein MVP.

### 2.4 Vertrauenslevel

**Definition 5 (Vertrauenslevel).** T: A → [0, 1] weist jeder App-ID a ein Vertrauenslevel zu (Abschnitt 4.3).
T(a) = 0 bedeutet: Deltas werden ignoriert.

### 2.5 Expertenbereiche, Schichten und Slots

**Definition 6 (Schichten).** Das Modell eines Geräts besteht aus

- **W_basis** – eingefrorene Basisgewichte des Open-Source-SLM,
- **W_logik** – gemeinsam gelernte LoRA-Adapter (Offen/Vertraulich), vom Hive verteilt,
- **W_PI** – lokale LoRA-Adapter für Streng geheim und Nur lokal; sie verlassen das Gerät nie.

Gradienten werden pro Trainingsbeispiel nach Stufe **maskiert**: Ein Streng-geheim-Beispiel aktualisiert nur W_PI.

**Definition 7 (Slot).** Ein Slot ist ein LoRA-Adapter mit Kennung, Fachgebiet, Kategorie, Rang und Lebenszyklus-
Metadaten. Der Hive führt die **Slot-Registry** und gibt Kategorien und Slots an die Geräte aus (Hive → Gerät). Ein
neuer Slot mit B-Matrix = 0 hat exakt keinen Einfluss auf die Ausgabe (B·A = 0) – das ist die technische Umsetzung der
„Knoten mit genullten Gewichtungen“ aus V1.0.25 (Korrektur C9).

**Definition 8 (Experte).** Ein Experte ist eine Menge von Slots eines Fachgebiets (z. B. „Steuerrecht DE“). Ein
**Router** (kleines Klassifikationsmodell) wählt pro Anfrage die passenden Experten.

---

## 3. Algorithmen auf dem Gerät

### 3.1 Lokales Training (Algorithmus 1)

1. Eingabe: lokale Beispiele D = {(xᵢ, yᵢ, sᵢ)} mit Stufe sᵢ; Quellen: Nutzerfeedback (Daumen hoch/runter,
   Korrekturen mit Beleg), bestätigte neue Informationen und Leerlauf-Aufgaben.
2. Verlust L(θ) = (1/n) Σ ℓ(f_θ(xᵢ), yᵢ), nur über LoRA-Parameter.
3. Maskierte Aktualisierung: Beispiele mit sᵢ ≥ STRENG_GEHEIM aktualisieren nur W_PI, alle anderen W_logik.
4. Ausgabe: Δθ_logik (einreichbar) und Δθ_PI (lokal).

**Leerlauf-Bedingungen:** Gerät lädt, Akku ≥ Schwelle, Temperatur unter Grenze, nicht getaktete Verbindung (für die
Einreichung), Nutzerzustimmung. Fallback: Training auf einem Desktop im LAN desselben Nutzers.

**Verifikation neuer Informationen:** Neue Fakten aus Nutzereingaben werden vor dem Lernen gegen einen Experten
geprüft (lokal oder – nur bei Offen/Vertraulich – Cloud-Experte). Ungeprüfte Fakten bleiben im lokalen Kontext.

### 3.2 Falten (Algorithmus 2)

Eingabe Δθ, Stufe s, App-ID a. Ausgabe F(Δθ, s) gemäß Definition 4. Die strengste Stufe aller Beispiele, die zu Δθ
beigetragen haben, bestimmt s (Taint).

### 3.3 Übertragung (Algorithmus 3)

`Hive.submit(F(Δθ, s), s, a, Signatur, Accountant-Stand)`

- Keine Rohdaten.
- Streng geheim: nur Strukturmeldung S (Abschnitt 5.1).
- Nur lokal: keine Nachricht – **0 Byte** (Systemtest-Kriterium).
- Transport: online (gRPC), P2P, Datenträger (USB) oder Intranet-Hive.

---

## 4. Hive: Weben, Unterteilen, Teilen

### 4.1 Sammeln

D_H = {(yᵢ, sᵢ, aᵢ)} enthält nur gefaltete Nutzlasten. Rohdaten werden nie angenommen.

### 4.2 Weben (Algorithmus 4 – FedAvg mit Vertrauenslevel)

1. Vertrauenslevel T(aᵢ) abrufen (Abschnitt 4.3).
2. Nach Stufe verarbeiten:
   - **Streng geheim:** Strukturmeldungen sammeln. Erreicht eine Kategorie die Mindestanzahl k_min verschiedener
     Melder, legt der Hive ggf. einen Slot mit Null-Gewichten an. Bestehende Gewichte bleiben unverändert.
   - **Offen/Vertraulich:** Δθᵢ = K⁺(yᵢ) rekonstruieren.
3. Gewichtete Aggregation über I = {i : sᵢ ∈ {OFFEN, VERTRAULICH} ∧ T(aᵢ) > 0}:
   `Δθ_avg = Σ_{i∈I} T(aᵢ)·Δθᵢ / Σ_{i∈I} T(aᵢ)`
4. Kandidat θ_neu = θ_aktuell + η_H · Δθ_avg (Server-Lernrate η_H, Standard 1).
5. **Eval-Gate** (Abschnitt 4.4) – erst danach Release.

Eigenschaft (Dreiecksungleichung): ‖Δθ_avg‖ ≤ max_{i∈I} ‖Δθᵢ‖ ≤ C (vor Rauschen).

### 4.3 Vertrauenslevel-Berechnung

`T(a) = f_betreiber(a) · (w₁·R(a) + w₂·Q(a) + w₃·Kons(a))`, w₁ + w₂ + w₃ = 1

- R(a) ∈ [0,1] Reputation, Q(a) ∈ [0,1] Datenqualität, Kons(a) ∈ [0,1] Konsistenz mit anderen Einreichungen.
- f_betreiber(a) ∈ [0,1]: Betreiberfaktor (0 = gesperrt, z. B. Blacklist; 1 = volles Vertrauen).
- **Startwerte (Korrektur C11):** f_betreiber = 0,5, R = Q = Kons = 0,1 → T_start = 0,05.
- **Reputationsanpassung pro Zeitraum (z. B. 1 Woche):**
  `R_neu = clamp(R_alt + f_betreiber·α·(g/n) − β·(b/n), 0, 1)`, wenn n ≥ n_min,
  mit g = gute, b = schlechte, n = alle bewerteten Einreichungen; α, β Betreiberparameter.
  „Gut“ heißt: Das Delta verbessert die Eval-Suite des Fachgebiets oder wird von Feedback bestätigt.

### 4.4 Abwehr von Vergiftung (neu, C12)

- Normschranke C (Clipping) begrenzt den Einfluss jedes Geräts.
- Optional robuste Aggregation (koordinatenweiser getrimmter Mittelwert oder Median) pro Fachgebiet.
- **Eval-Gate:** Jedes Kandidaten-Release muss eine versionierte Evaluationssuite (Fachwissen, Sicherheit,
  Regressionen, Kanarien gegen Memorierung) bestehen; sonst Verwurf und Absenkung von R(a) der Beitragenden.
- Blacklist: f_betreiber(a) = 0; Trust-Historie pro App-ID.

### 4.5 Unterteilen

Der Hive ordnet Deltas Fachgebieten zu (Kategorie der Einreichung, Router-Signal) und bildet daraus Experten-
Adapter. Seltene Fachgebiete werden durch Betreiber-Knoten mit kuratierten Quellen bedient (z. B. Wikipedia-Crawler
mit hohem f_betreiber).

### 4.6 Teilen (Release und Verteilung)

- Releases sind **inhaltsadressiert** (Hash) und **signiert** (Rollen- und Schlüsselmodell nach The Update Framework,
  TUF): Root, Targets, Snapshot, Timestamp. Geräte prüfen vor dem Laden Signatur, Version (kein Downgrade) und Hash.
- Verteilung über Hive (HTTP/gRPC), **libp2p**-Mesh (Gerät ↔ Gerät) und Datenträger.
- Pakete sind **Delta-Pakete**: Das Gerät sendet einen Update-Request mit seinem Versionsstand; Hive oder Peer
  ermitteln die Lücke und liefern genau diese.
- Kleine Dosen („Chunked Learning“): Ein Release betrifft nur die geänderten Slots.

---

## 5. Garantien

### 5.1 Streng geheim (präzisiert, C8)

**Satz 3 (Isolation der Gewichte).** Für s = STRENG_GEHEIM ist die Gewichts-Nutzlast konstant 0. Daher gilt
I(Δθ_raw ; Gewichts-Nutzlast) = 0 – auch mit unbegrenzter Rechenleistung ist aus ihr nichts rekonstruierbar.

**Metadatenkanal.** Die Strukturmeldung S verlässt das Gerät und ist **nicht** informationsfrei. Sie enthält:

| Feld | Ausprägung |
|---|---|
| Kategorie | grobe Kategorie aus der vom Hive vorgegebenen Liste (keine Freitexte) |
| Zeitpunkt | gebündelt und verzögert (z. B. tägliche Sammelmeldung) |
| App-ID | ja – für Blacklist und Trust-Historie |

Restrisiko: Aus Kategorien und Zeitpunkten kann ein Themen-/Aktivitätsprofil der App-ID entstehen. Maßnahmen:
grobe Kategorien, Bündelung, Mindestanzahl k_min, Hinweis in der UI und in den Nutzungsbedingungen. Wer das nicht
akzeptiert, nutzt „Nur lokal“ oder einen eigenen Hive-Klon (Abschnitt 9.2.6). Eine Entkopplung der Meldung von der
App-ID über anonyme Berechtigungsnachweise ist als Härtung vorgesehen (Backlog BL-004).

**Doppelte Absicherung.** Auch wenn ein manipuliertes Gerät Gewichte mit s = STRENG_GEHEIM senden würde, verwirft
der Hive sie (Bitflag-Prüfung).

### 5.2 Vertraulich und Offen

- **Vertraulich:** (ε, δ)-DP pro Einreichung (Satz 1) und kumuliert per Accountant (Satz 1a).
- **Offen:** keine Privatsphäre-Garantie. Der Nutzer stimmt der Verarbeitung durch den Hive-Betreiber zu. Offen lernt
  **sofort** (Trend-Themen), unterliegt aber Clipping, Vertrauenslevel und Eval-Gate.

**Satz 4 (Rekonstruktion bei Offen).** Für K_LoRA ist K⁺(K(x)) = x exakt. Für K_RP ist K⁺K = PᵀP eine orthogonale
Projektion; der Verlust ist ‖(I − PᵀP)x‖.

### 5.3 Konvergenz (Vermutung, C10)

**Vermutung 5.** Unter L-glatter Verlustfunktion, beschränkten Deltas ‖Δθᵢ‖ ≤ C und fester Teilnehmerverteilung
gilt für den Abstand zum Optimum θ* der T-gewichteten Datenverteilung eine Schranke der Form

`E‖θ_t − θ*‖² ≤ O(1/t) + O(σ²·k / (Σ_{i∈I} T(aᵢ))²) + Heterogenitätsterm`.

Der Rauschterm wächst mit k (deshalb kleine Ränge r) und sinkt mit der Summe der Vertrauenslevel. Ein Beweis ist
offen; Orientierung geben DP-FedAvg-Analysen [McMahan et al., 2018; Kairouz et al., 2021]. Bis dahin wird Konvergenz
empirisch im Eval-Gate nachgewiesen.

---

## 6. Vorteile

| Eigenschaft | Begründung | Relevanz |
|---|---|---|
| Datensouveränität | Nur lokal: 0 Byte; Streng geheim: Null-Delta (Satz 3) | DSGVO-Datenminimierung |
| (ε, δ)-DP | Satz 1 + Accountant | Schutz vor Inversion/Membership Inference |
| Keine Rohdaten | Nur gefaltete Nutzlasten | Grundprinzip |
| Doppelte Absicherung | Falten lokal + Stufenprüfung im Hive | Defense in Depth |
| Vertrauensbasiertes Weben | T(a) inkl. Betreiberfaktor | Kontrollierte Einbindung externer Quellen |
| Struktur-Erhaltung | Slots mit Null-Gewichten | Geheimes Wissen profitiert von gemeinsamen Updates |
| Offline-Fähigkeit | Lokale Inferenz und lokales Training; Datenträger-Updates | Air-Gap, Intranet, IoT |
| Energieeffizienz | Training in Leerlaufzeiten, kleine Adapter | Kosten, Akku |

---

## 7. Formale Zusammenfassung

1. Falten: Definition 4.
2. DP: σ ≥ 2C·√(2 ln(1,25/δ))/ε (ε < 1), sonst analytisch; Komposition per Accountant.
3. Vertrauen: T(a) = f_betreiber(a)·(w₁R + w₂Q + w₃Kons).
4. Weben: θ_neu = θ + η_H · Σ T·Δθ / Σ T über Offen/Vertraulich mit T > 0, nach Eval-Gate.
5. Isolation: I(Δθ_raw; Gewichts-Nutzlast) = 0 für Streng geheim; Metadatenkanal gemäß 5.1.

### 7.1 Stufenübersicht

| Stufe | Verlässt das Gerät | Hive-Verarbeitung | Garantie |
|---|---|---|---|
| Offen | K(clip(Δθ)) | Weben mit T(a) | keine (Zustimmung) |
| Vertraulich | K(clip(Δθ)) + σ·N | Weben mit T(a) | (ε, δ)-DP |
| Streng geheim | Strukturmeldung S | Slot mit Null-Gewichten ab k_min | Gewichts-Isolation + benannter Metadatenkanal |
| Nur lokal | nichts | – | vollständige Isolation |

---

## 8. Vergleich mit Standard-Federated-Learning

| Kriterium | Standard-FL | Tensegrity AI |
|---|---|---|
| Übertragung | Gradienten/Updates | gefaltete LoRA-Deltas je Stufe |
| Privatsphäre | optional | vier Stufen, DP für Vertraulich, Isolation für Streng geheim/Nur lokal |
| Vertrauen | meist keines | T(a) mit Betreiberfaktor, Blacklist, Eval-Gate |
| Offline | serverabhängig | lokal + Datenträger + P2P |
| Struktur | – | Slot-Registry, Null-Slots für Streng geheim |
| Souveräne Betreiber | – | Hive-Klone mit eigenen Vertrauensankern |

---

## 9. Fazit und offene Fragen

Der Ansatz kombiniert nachweisbare Garantien (Isolation, (ε, δ)-DP), praktische Flexibilität (offline, Datenträger,
Hive-Klone) und Skalierbarkeit (Smartphone bis Kubernetes). Gegenüber V1.0.25 sind die Garantien enger, aber
**ehrlich** formuliert.

### 9.2.1 DP-Parameter je Domäne

Parameter werden per **Richtlinie** gesetzt, versioniert und auditiert. Ein Fach-LLM darf Werte nur **vorschlagen**;
datenabhängige automatische Anpassung würde selbst Privatsphäre verbrauchen und ist nur mit Accounting zulässig.

### 9.2.2 Hive-Last

Der Hive trainiert nicht auf Rohdaten; Last entsteht durch Weben, Eval-Gate und Paketbau. Skalierung horizontal
(Kubernetes) oder durch Drosselung der Annahme (Einreichungen werden gepuffert, nicht verworfen, solange Speicher
reicht).

### 9.2.3 Reputation

Siehe 4.3 (steigt und sinkt).

### 9.2.4 Wahl von k

Über den Adapter-Rang r; Standard r = 8…16, Betreiber kann pro Fachgebiet anpassen.

### 9.2.5 Struktur-Erhaltung bei Streng geheim

Null-Slots sind strukturelle Container. Geräte ohne eigenen W_PI-Anteil ignorieren sie (kein Einfluss). Geräte mit
lokalen Gewichten behalten diese beim Update; gemeinsame Änderungen in W_logik wirken trotzdem auf ihre Ausgaben.

### 9.2.6 Offline-Updates und souveräne Hives (präzisiert, C13)

- **Ein Hive-Produkt, Rollen per Konfiguration:** Internet-Hive (Root/Upstream) oder Klon (Organisation, Intranet,
  Land). Kaskaden sind möglich.
- **Vertrauensanker konfigurierbar:** Ein Klon prüft Upstream-Signaturen und signiert lokal neu; Geräte vertrauen
  dem konfigurierten Anker.
- **Import/Export-Modul:** online oder per Datenträger, gesteuert durch Richtlinie. Ein Intranet-Hive ist strikt
  getrennt; Export ist optional und schließt Streng geheim immer aus.
- **Lokale Priorität:** Lokale Daten werden mit hohem Betreiberfaktor eingewoben.
- **Offline-Gerät:** Update-Request-Paket → Lückenermittlung → Delta-Paket (gleiches Verfahren wie P2P).

### 9.2.7 Leerlaufzeit und seltene Domänen

Leerlauftraining nach den Bedingungen in 3.1; Hochleistungs-Knoten der Betreiber mit kuratierten Quellen für seltene
Domänen. Je mehr Geräte teilnehmen, desto schneller lernt das Netz.

### 9.2.8 Sicherheit im Modus „Offen“

Klare UI-Hinweise vor der Auswahl. Anwendungsfall: öffentliche Informationen (Veranstaltungen, Pressemitteilungen),
die das Netz schnell lernen soll. Missbrauch wird über Vertrauenslevel, Eval-Gate und Ablaufdaten (BL-005) begrenzt.

### 9.2.9 Gezielte Updates für Streng-geheim-Slots (neu, C14)

Der Hive kann Updates für eine Kategorie liefern, zu der ein Gerät Null-Slots gemeldet hat. Das Gerät wendet sie nicht
automatisch an, sondern über ein **lokales Prüf-Gate**: Vorschlag an den Nutzer bzw. automatische Prüfung gegen lokale
Testfragen, Übernahme nur bei Verbesserung, **Rollback** jederzeit. Für Offen/Vertraulich gilt dagegen: Vertrauenslevel
des Hive > lokales Gerät, Updates werden direkt übernommen.

---

## Literatur

- Vaswani, A. et al. (2017). *Attention Is All You Need.* arXiv:1706.03762.
- Hu, E. et al. (2021). *LoRA: Low-Rank Adaptation of Large Language Models.* arXiv:2106.09685.
- McMahan, H. B. et al. (2017). *Communication-Efficient Learning of Deep Networks from Decentralized Data.* arXiv:1602.05629.
- McMahan, H. B. et al. (2018). *Learning Differentially Private Recurrent Language Models.* arXiv:1710.06963.
- Dwork, C., Roth, A. (2014). *The Algorithmic Foundations of Differential Privacy.* FnT TCS 9(3–4).
- Balle, B., Wang, Y.-X. (2018). *Improving the Gaussian Mechanism for Differential Privacy.* arXiv:1805.06530.
- Mironov, I. (2017). *Rényi Differential Privacy.* arXiv:1702.07476.
- Bonawitz, K. et al. (2017). *Practical Secure Aggregation for Privacy-Preserving Machine Learning.* CCS 2017.
- Bonawitz, K. et al. (2019). *Towards Federated Learning at Scale: System Design.* SysML 2019.
- Kairouz, P. et al. (2021). *Advances and Open Problems in Federated Learning.* FnT ML 14(1–2).
- Fredrikson, M. et al. (2015). *Model Inversion Attacks that Exploit Confidence Information.* CCS 2015.
- Samuel, J. et al. (2010). *Survivable Key Compromise in Software Update Systems* (The Update Framework). CCS 2010.

> Hinweis: Einige Referenzangaben aus V1.0.25 waren fehlerhaft (z. B. arXiv-Nummern für McMahan 2017 und Balle 2018)
> und wurden korrigiert. Vor einer Veröffentlichung sind alle Angaben noch einmal gegen die Originalquellen zu prüfen.

---

## Anhang A: Pseudocode

```text
function fold(delta, level, cfg, accountant):
    if level == NUR_LOKAL:      return None
    if level == STRENG_GEHEIM:  return StructureReport(category(delta), batch=cfg.batch)
    x = clip(delta, cfg.C)
    y = compress(x, cfg.K)                         # LoRA-Faktoren oder P·x
    if level == VERTRAULICH:
        sigma = calibrate_gaussian(cfg.eps, cfg.delta, sensitivity=2*cfg.C)
        if not accountant.can_spend(cfg.eps, cfg.delta): return None
        y = y + sigma * normal(size=len(y))
        accountant.spend(cfg.eps, cfg.delta)
    return Payload(y, level)

function weave(submissions, trust, cfg):
    acc, wsum = 0, 0
    for s in submissions:
        if s.level == STRENG_GEHEIM: registry.report(s.category, s.app_id); continue
        if s.level not in {OFFEN, VERTRAULICH}: reject(s); continue
        t = trust(s.app_id)
        if t <= 0: continue
        acc += t * decompress(s.payload, cfg.K)
        wsum += t
    registry.materialize_slots(k_min=cfg.k_min)    # Null-Slots
    if wsum == 0: return None
    candidate = theta + cfg.eta_h * acc / wsum
    return candidate if eval_gate(candidate) else None
```
