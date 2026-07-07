# Firebase Security Rules neu veröffentlichen

Anleitung zum Veröffentlichen der Firestore Security Rules – nötig, damit die
GS-Berechtigungen für **Probenversand** und **Belegzentrale** greifen.

> **Wichtig:** Beim Veröffentlichen werden die Regeln **komplett ersetzt**, nicht
> ergänzt. Es muss immer der vollständige Regelsatz eingefügt werden – also die
> bestehenden Regeln **plus** die neuen Abschnitte für Probenversand und
> Belegzentrale.

## Schritt für Schritt (Firebase Console)

1. https://console.firebase.google.com öffnen und mit dem Firebase-Konto anmelden.
2. Das Projekt der TAE Dispo App auswählen.
3. Links im Menü: **Build → Firestore Database** öffnen.
4. Oben den Tab **Regeln** (englisch: *Rules*) anklicken.
5. **Zuerst sichern:** Den kompletten aktuellen Regeltext kopieren und als
   `firestore.rules` in dieses Repository legen (siehe unten). So geht bei
   Fehlern nichts verloren und Claude Code kann in Zukunft direkt damit arbeiten.
6. Den neuen, vollständigen Regelsatz in den Editor einfügen
   (Vorlage: `firebase/firestore.rules.template` in diesem Repo – vorher mit den
   bestehenden Regeln zusammenführen und an die echten Collection-Namen anpassen).
7. Auf **Veröffentlichen** (*Publish*) klicken.
8. Die Änderung ist sofort aktiv – es dauert höchstens 1–2 Minuten.

## Testen nach dem Veröffentlichen

- In der Console unter **Regeln → Playground** (*Rules Playground*) einzelne
  Zugriffe simulieren: z. B. „get" auf `probenversand/test` mit der UID eines
  GS-Benutzers – muss **erlaubt** sein; mit der UID eines Fahrers – je nach
  gewünschter Berechtigung.
- Danach in der App selbst prüfen: Als GS-Benutzer anmelden und Probenversand
  sowie Belegzentrale öffnen. Die Fehlermeldung
  `Missing or insufficient permissions` darf nicht mehr erscheinen.

## Regeln im Repository pflegen (empfohlen)

Damit die Regeln nicht nur in der Console leben:

1. Aktuellen Regeltext aus der Console kopieren.
2. In diesem Repo als `firebase/firestore.rules` speichern und committen.
3. Bei jeder Regeländerung: erst hier ändern, dann in der Console veröffentlichen.

Alternativ per Firebase CLI (falls installiert):

```bash
firebase login
firebase deploy --only firestore:rules
```

Dafür muss im Repo eine `firebase.json` mit Verweis auf die Rules-Datei liegen.
