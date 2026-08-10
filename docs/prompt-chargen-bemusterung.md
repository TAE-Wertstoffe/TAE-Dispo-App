# Prompt für Claude Code – Chargen & Bemusterung mit QR-Code (Eigenproduktion + Handelsware)

> Kontext: Neues Chargen-Modul für Mahlgut. Ziel: lückenlose Rückverfolgbarkeit
> (von wem kommt das Material) und Bemusterungs-/Freigabestatus je Abnehmer.
> Gilt für ZWEI Chargenarten: **Eigenproduktion** (selbst gemahlenes Mahlgut) und
> **Handelsware** (angekauftes Mahlgut, das unverändert weitergehandelt wird).
> Kernregel Bemusterung: Ist eine Material-Qualität bei einem Abnehmer einmal
> bemustert/freigegeben, können Folgelieferungen derselben Qualität OHNE erneute
> Bemusterung versendet werden – Eigenproduktion muss genauso erst bemustert
> werden wie Handelsware. Erst Ist-Aufnahme + Konzept (Nummernkreis, Datenmodell,
> QR-Erzeugung offline), dann bauen. Leitplanken wie immer: buildPrintHtml/Nummern
> unangetastet für RE/GS, additiv, Regeln/SECURITY.md, Versionszeile.

## 1 – Chargen anlegen
- Neuer Nummernkreis, Vorschlag **CH-JJJJ-NNN** (ans bestehende DOC_LABELS/
  Nummern-Schema anpassen und VOR dem Bau zeigen).
- Felder: Material/Typ (aus Materialstamm), Netto-kg, Produktionsdatum,
  Maschine (KuMa1/KuMa2/BeiMa…), Gebinde-/Big-Bag-Bezug.
- **Chargenart:** „Eigenproduktion" | „Handelsware".
  - Eigenproduktion: Herkunft = Anlieferung(en)/Lieferant(en), aus denen gemahlen
    wurde – **mehrere möglich** (Mischcharge).
  - Handelsware: Herkunft = Ankaufs-Vorgang/Anlieferung + Lieferant.
- Bereichs-Chips 🧩/🔩 wie überall (praktisch meist 🧩, Mechanik aber einheitlich).

## 2 – QR-Code + Chargen-Etikett
- QR-Code **offline in der App** erzeugen (eingebettete JS-Bibliothek, kein
  externer Dienst/CDN). Inhalt: Chargen-Nr. + Direktlink in die Chargen-Akte.
- Neuer Druck „**Chargen-Etikett**" über den buildPrintHtml-Weg/Briefpapier
  (für Big Bag und Probenpäckchen). RE/GS-Druck bleibt byte-identisch.
- Scan mit dem Handy öffnet die Chargen-Akte (Herkunft, Versände, Freigaben).

## 3 – Probenversand mit Chargen-Bezug
- Im Muster-/Proben-Versandmodul wird beim Versand die Charge ausgewählt.
- Jeder Versand wird an der Charge protokolliert: Abnehmer, Datum, Menge,
  Versandart.
- Proben-Begleitschein zeigt Chargen-Nr. + QR-Code, damit der Abnehmer seine
  Freigabe eindeutig darauf beziehen kann.

## 4 – Freigaben (Bemusterung): Material-Qualität + Abnehmer, mit Referenz-Charge
- Die Freigabe hängt an **Material-Qualität + Abnehmer**, mit Verweis auf die
  **Referenz-Charge** (die bemusterte Probe) – NICHT nur an der Einzelcharge.
- Status je Abnehmer: **angefragt / freigegeben / abgelehnt**, mit Datum,
  optionaler Referenz (E-Mail/Prüfbericht) und Notiz.
- **Mehrere Abnehmer** können dieselbe Qualität freigeben → kontinuierliche
  Abnahme; pro Material sofort sichtbar, welche Abnehmer „grün" sind.
- Neue Charge derselben Material-Qualität zeigt vorhandene Freigaben automatisch
  an: „freigegeben durch [Abnehmer] über Referenz-Charge CH-…".
- **Sicherheitsnetz:** an jeder Charge pro Abnehmer „erneut bemustern" setzbar
  (z. B. neue Herkunft, anderer Lieferant, Qualitätszweifel) → für diese Charge
  gilt der Abnehmer wieder als „angefragt".
- **Handelsware:** gleiche Mechanik – nach der ersten Freigabe laufen
  Folgelieferungen ohne erneute Bemusterung. Bei **Lieferantenwechsel** der
  Handelsware deutlichen Hinweis anzeigen (Vorschlag: automatisch „erneut
  bemustern" empfehlen, aber GS entscheidet).
- Definition „gleiche Material-Qualität": ans bestehende Materialstamm-Schema
  anlehnen (Material/Typ, ggf. + Quelle). **Ist-Aufnahme machen und Vorschlag
  VOR dem Bau zeigen – keine stille Annahme.**

## 5 – Chargen-Akte & Konsistenz
- Chargen-Akte: Stammdaten, Herkunft, Proben-Versände, Freigaben, Verknüpfungen
  (Abverkauf/Lieferschein soweit vorhanden).
- Partnerakte (Abnehmer): Bemusterungsstatus je Material sichtbar.
- Chargen-Übersicht mit Filtern: „freigegeben bei …", „Bemusterung läuft",
  „nicht bemustert", Chargenart.
- Kein hartes Löschen – Soft-Delete-Logik wie im übrigen Bestand.

## Akzeptanz
1. Charge (Eigenproduktion) aus zwei Anlieferungen anlegen → Etikett mit QR
   druckt korrekt; Scan öffnet die Chargen-Akte; Herkunft zeigt beide
   Lieferanten/Anlieferungen.
2. Probe aus der Charge an zwei Abnehmer versenden → beide Versände stehen an
   der Charge; Freigabe von Abnehmer A setzen → Material gilt bei A als
   bemustert.
3. Neue Charge derselben Qualität anlegen → Freigabe von A wird automatisch mit
   Referenz-Charge angezeigt; Abnehmer B bleibt offen/„angefragt".
4. Handelsware-Charge: nach erster Freigabe Versand ohne erneute Bemusterung
   möglich; Lieferantenwechsel erzeugt den Hinweis/„erneut bemustern".
5. RE/GS-Belege byte-identisch wie zuvor; neue Collections in den Security Rules
   + SECURITY.md dokumentiert; REST-Negativtest.

## Abschluss
Bericht VOR dem Bau: Nummernkreis-Vorschlag, Datenmodell (Collections/Felder),
QR-Lösung (offline, eingebettet), Definition „gleiche Material-Qualität".
Danach Umsetzung + Nachtest-Anleitung: je ein kompletter Durchlauf
Eigenproduktion und Handelsware inkl. zweitem Abnehmer und „erneut bemustern".
