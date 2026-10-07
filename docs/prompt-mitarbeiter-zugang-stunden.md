# Prompt für Claude Code – Mitarbeiter-Zugang „Fahrer/Aushilfe": Stundenerfassung + Touren-Sicht

> Kontext: Christian Wolf arbeitet neu auf Stundenbasis (Minijob) und fährt den
> Absetzkipper. Er bekommt einen eigenen Login (E-Mail + Passwort) mit stark
> eingeschränkter Rolle: Er erfasst seine Arbeitsstunden selbst und sieht seine
> zugewiesenen Touren – sonst nichts. Die Lohnabrechnung läuft extern im
> Lohnprogramm; die App führt NUR Stunden (keine Stundensätze, keine Lohndaten).
> Nebeneffekt: Für Minijobber gilt die MiLoG-Aufzeichnungspflicht (Beginn, Ende,
> Dauer je Arbeitstag, Aufzeichnung binnen 7 Tagen, 2 Jahre aufbewahren) – das
> Modul deckt das gleich mit ab. Erst Ist-Aufnahme + Konzept (Rollen-/users-
> Schema, Touren-Datenmodell!), dann bauen. Leitplanken wie immer:
> buildPrintHtml/Nummern unangetastet für RE/GS, additiv, Regeln/SECURITY.md,
> Versionszeile.

## 1 – Rolle & Zugang
- Neue Rolle „**fahrer**" (Minimal-Rechte). Ans bestehende Rollen-/users-Schema
  anpassen – Ist-Aufnahme machen und VOR dem Bau zeigen.
- Benutzer wird vom Admin angelegt (Firebase Auth E-Mail/Passwort + users-
  Dokument mit Rolle).
- Nach dem Login: reduzierte Oberfläche mit genau zwei Bereichen:
  **„Meine Stunden"** und **„Meine Touren"**. Keine Preise, keine Partner-
  Finanzdaten, keine Belege/OPOS, keine Chargen, keine Stammdaten.
- Die Einschränkung gilt nicht nur in der Oberfläche, sondern hart über die
  Security Rules (siehe 5).

## 2 – Stundenerfassung (MiLoG-konform, handy-tauglich)
- Eintrag: Datum, Arbeitsbeginn, Arbeitsende, Pause (Minuten) → **Dauer wird
  automatisch berechnet**. Tätigkeit/Fahrzeug vorbelegt „Absetzkipper",
  Notiz optional.
- Bedienung bewusst simpel und mobil-tauglich (Eintrag vom Handy in <30 Sek.).
- Eigene Einträge kann der Fahrer bis zum Monatsabschluss selbst korrigieren;
  danach gesperrt. Admin/GS kann jederzeit korrigieren – **mit Korrektur-Log**
  (gleiche Mechanik wie Big-Bag-Gewichtskorrektur).
- **Keine Lohn-/Stundensatzdaten in der App** – die App liefert nur Stunden,
  abgerechnet wird im externen Lohnprogramm.

## 3 – Monatsansicht & Stundenzettel
- Admin/GS: Monatsansicht je Mitarbeiter – alle Tage mit Beginn/Ende/Pause/
  Dauer, Monatssumme in Stunden.
- Button „**Monat abschließen**": sperrt die Einträge des Monats für den
  Fahrer (Admin kann mit Log wieder öffnen).
- Druck „**Stundenzettel**" (Monat) über den buildPrintHtml-Weg/Briefpapier:
  Tagesliste, Monatssumme, Unterschriftszeilen (Mitarbeiter + Arbeitgeber).
  Das ist der MiLoG-Nachweis (2 Jahre aufbewahren) und die Vorlage fürs
  Lohnprogramm.

## 4 – Touren-Sicht für den Fahrer
- „Meine Touren": alle dem Fahrer zugewiesenen Touren (heute + kommende) mit
  Reihenfolge, Partner/Adresse, Material/Behälter, Bemerkung – damit er
  jederzeit weiß, was zu tun ist.
- Fahrer kann eine Tour als „**erledigt**" markieren + kurze Notiz; weitere
  Änderungen an Touren kann er nicht vornehmen.
- Zuweisung: am Tour-Vorgang ein Feld „Fahrer" (an die bestehende Dispo-
  Mechanik anlehnen – Ist-Aufnahme).
- **WICHTIG – Befund VOR dem Bau:** Enthalten die Tour-/Vorgangs-Dokumente
  Preise oder andere sensible Daten? Firestore-Regeln wirken je Dokument,
  nicht je Feld – ein Lesezugriff aufs Tour-Dokument gäbe also ALLE Felder
  frei. Falls Preise drinstehen: Lösungsvorschlag zeigen (z. B. separate,
  reduzierte Fahrer-Touren-Dokumente, die die Dispo automatisch pflegt).
  Bitte Befund, keine stille Annahme.

## 5 – Sicherheit & Konsistenz
- Security Rules: Rolle „fahrer" darf **nur eigene** Stunden-Einträge lesen und
  anlegen/ändern (userId == auth.uid, Änderung nur bis Monatsabschluss) und
  **nur zugewiesene** Touren lesen + das Erledigt-/Notiz-Feld setzen. Alle
  anderen Collections: verweigert.
- **REST-Negativtest:** mit Fahrer-Token gezielt fremde Stunden, Partner,
  Belege, OPOS abfragen → muss verweigert werden.
- SECURITY.md um Rolle + neue Collection(s) ergänzen. Nach dem Bau: Firebase
  Rules in der Console neu veröffentlichen (Anleitung liegt im Repo:
  docs/firebase-rules-veroeffentlichen.md).
- Mehrbenutzerfähig bauen: weitere Aushilfen später = einfach weitere Benutzer
  mit Rolle „fahrer".

## Akzeptanz
1. Admin legt Benutzer „Christian Wolf" (Rolle fahrer) an → sein Login zeigt
   nur „Meine Stunden" + „Meine Touren", sonst nichts.
2. Stundeneintrag vom Handy: Beginn/Ende/Pause → Dauer korrekt berechnet;
   eigener Eintrag bearbeitbar; fremde Einträge weder sichtbar noch per REST
   lesbar.
3. Monatsansicht zeigt Tagesliste + Summe; „Monat abschließen" sperrt die
   Fahrer-Bearbeitung; Stundenzettel druckt korrekt mit Unterschriftszeilen.
4. Tour mit Fahrer-Zuweisung erscheint unter „Meine Touren"; „erledigt"-
   Markierung ist in der Dispo sichtbar; nicht zugewiesene Touren erscheinen
   nicht und sind per REST nicht lesbar (bzw. Lösung laut Befund aus 4).
5. Fahrer sieht keinerlei Preise/Belege/OPOS; RE/GS-Belege byte-identisch wie
   zuvor; Versionszeile aktualisiert; REST-Negativtest dokumentiert.

## Abschluss
Bericht VOR dem Bau: Ist-Aufnahme Rollen-/users-Schema, Touren-Datenmodell-
Befund (Preise ja/nein + Lösungsvorschlag), UI-Skizze der Fahrer-Ansicht.
Danach Umsetzung + Nachtest-Anleitung: kompletter Durchlauf – Benutzer anlegen,
Stunden erfassen (inkl. Korrektur), Tour zuweisen und erledigen, Monat
abschließen, Stundenzettel drucken – und zum Schluss Erinnerung: Firebase
Security Rules neu veröffentlichen.
