# Offene Punkte – Status (Stand 07.07.2026)

| # | Punkt | Status | Blockiert durch |
|---|-------|--------|-----------------|
| 1 | v116-Build: drei Konzepte aus dem Bericht (Punkt 1) bauen | Wartet | Sichtung/Freigabe der Konzepte durch GF; außerdem liegt die v115 (App-Quellcode) nicht in diesem Repository |
| 2 | Firebase Security Rules neu veröffentlichen (GS-Berechtigungen Probenversand + Belegzentrale) | Vorbereitet | Veröffentlichen geht nur mit Firebase-Konto in der Console – Anleitung: `docs/firebase-rules-veroeffentlichen.md`, Vorlage: `firebase/firestore.rules.template` |
| 3 | Sechs Praxis-Befunde (Material-Sperren, Brutto/Tara/Netto, BeiMa2, Soft-Delete „Entsorgen", Big-Bag-Gewichtskorrektur mit Log, Muster-/Proben-Versandmodul) | Offen | App-Quellcode (v115) muss in dieses Repository |
| 4 | €/kg-vs-€/t-Inkonsistenz im Preisfeld | Offen | App-Quellcode (v115) muss in dieses Repository |
| 5 | Faktura Stufe 2 – Wizard | Offen | App-Quellcode (v115) muss in dieses Repository |

## Wichtigster nächster Schritt

Dieses Repository enthält bisher **nur die README – keinen App-Code**.
Damit Punkte 1, 3, 4 und 5 hier umgesetzt werden können:

1. Die aktuelle App-Datei (v115, z. B. `index.html` bzw. das App-Verzeichnis)
   in dieses Repository committen.
2. Die aktuellen Firestore Security Rules aus der Firebase Console als
   `firebase/firestore.rules` ablegen.
3. Den Bericht mit den drei Konzepten (Punkt 1) als Datei ins Repo legen oder
   in der Session mitgeben.
