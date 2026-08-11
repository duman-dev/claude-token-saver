<p align="center">
  <img src="banner.png" alt="Claude Token Saver Banner" width="100%" style="max-width: 1000px; height: auto;">
</p>

# <p align="center">Claude Token Saver</p>

<p align="center">
  <b>Claude Token Saver</b> ist eine Sammlung praktischer Methoden zur Reduzierung des Token-Verbrauchs bei der Verwendung von Claude und Claude Code. In echten Arbeitsabläufen von Entwicklern getestet und bestätigt — keine Garantie, aber es funktioniert.
</p>

<p align="center">
  <a href="README.md">🇬🇧</a> &nbsp;&nbsp;
  <a href="README.tr.md">🇹🇷</a> &nbsp;&nbsp;
  <a href="README.de.md">🇩🇪</a> &nbsp;&nbsp;
  <a href="README.ru.md">🇷🇺</a>
</p>

---

> **Hinweis:** Diese Methoden basieren auf praktischer Erfahrung und Beobachtung. Wir haben gesehen, dass sie konsistent funktionieren, aber die Ergebnisse können je nach Nutzungsmuster, Sitzungsdauer und Modellversion variieren.

---

### Methoden

* [**1 — Prägnante Anfragen**](#1--prägnante-anfragen): Sag Claude, er soll Füllwort weglassen. Weniger Output, gleiches Ergebnis.
* [**2 — Bearbeiten statt Nachfolge**](#2--bearbeiten-statt-nachfolge): Ersetze gescheiterte Versuche, statt sie zu stapeln.
* [**3 — Kontext komprimieren**](#3--kontext-komprimieren): Setze lange Sitzungen zurück, ohne Fortschritt zu verlieren.
* [**4 — Batchen statt Splitten**](#4--batchen-statt-splitten): Kombiniere verwandte Aufgaben in einer Nachricht statt vielen.

---

## 1 — Prägnante Anfragen

Claudes Standardstil ist gesprächig. Jede Einleitung, Zusammenfassung und "Ich helfe dir gerne" kostet Output-Token — und wird in zukünftigen Turns als Teil der Historie erneut gelesen.

**Lösung:** Füge dies in deine `CLAUDE.md` (Claude Code) oder **Einstellungen → Profil → Benutzereinstellungen** (claude.ai) ein:

```
Antworte auf prägnanteste Weise möglich. Überspringe Höflichkeiten,
Einleitungen und Zusammenfassungen meiner Frage. Verwende keine
Phrasen wie "Ich helfe dir gerne", "Großartig" oder "Lass mich erklären".
Entferne Artikel und Füllwörter überall dort, wo der Sinn klar bleibt.
Bevorzuge kurze, prägnante Sätze. Wenn ein Tool-Aufruf nötig ist,
führe ihn aus und zeige nur das Ergebnis. Erläutere nicht deine Schritte.
```

**Vorher:**
> "Großartig! Ich helfe dir gerne damit. Um diesen Bug zu beheben, musst du die Null-Referenz in Zeile 12 überprüfen. Lass mich erklären, was hier passiert..."

**Nachher:**
> "Null-Referenz in Zeile 12."

Gleiche Antwort. Ein Bruchteil der Token.

---

## 2 — Bearbeiten statt Nachfolge

Wenn Claude den Punkt verfehlt, ist der Reflex, eine Korrektur zu senden: *"Nein, ich meine..."* Diese Korrektur wird der Historie hinzugefügt. Die falsche Antwort bleibt. Beide werden danach jede Turn erneut gelesen.

**Lösung:** Bearbeite deine Original-Nachricht und regeneriere, statt zu antworten.

| ❌ Nachfolge | ✅ Bearbeiten |
|---|---|
| Original-Nachricht bleibt | Original-Nachricht wird aktualisiert |
| Falsche Antwort bleibt in der Historie | Falsche Antwort wird verworfen |
| Beide werden jede Turn gelesen | Nur korrektes Gespräch im Kontext |

In Claude Code: Wenn eine Sitzung aus den Gleisen läuft, starte eine neue Sitzung, statt zu versuchen, die gleiche zu lenken.

---

## 3 — Kontext komprimieren

Jede Nachricht trägt die vollständige Gesprächshistorie mit sich. Nach 15–20 Nachrichten ist der größte Teil dieser Historie irrelevant — wird aber trotzdem jede Turn geladen.

**Lösung:** Bevor du einen neuen Chat startest, sende diese Anfrage für eine maschinenoptimierte Übergabe:

```
Fasse unser gesamtes Gespräch zusammen, damit ich es in einen
neuen Chat einfügen und ohne Kontextverlust fortfahren kann. Schließe ein:
(1) das ursprüngliche Ziel oder Problem
(2) getroffene Schlüsselentscheidungen und warum
(3) eingeigten Code, Konfiguration, Daten (Wort für Wort, in Code-Blöcken)
(4) offene Fragen und nächste Schritte
Verwende kurze Abschnitte mit Überschriften. Überspringe Smalltalk und
Erkundungstangenten. Optimiere die Zusammenfassung für einen Claude,
der sie kalt liest.
```

Füge das Ergebnis als erste Nachricht eines neuen Chats ein. Eine 40-Nachrichten-Sitzung wird zu einer 200-Wort-Zusammenfassung — keine History mehr mitgeschleppt.

---

## 4 — Batchen statt Splitten

Jede Nachricht lädt die vollständige Gesprächshistorie neu. Drei kleine Aufgaben separat gesendet = drei History-Ladevorgänge. Alle drei auf einmal = ein Ladevorgang.

**Lösung:** Kombiniere verwandte Anfragen in einer Nachricht.

**Vorher:**
```
Du: Behebe den Tippfehler in dieser Funktion
Du: Füge jetzt eine Dokumentation hinzu
Du: Und füge Typhinweise hinzu
```

**Nachher:**
```
Du: Behebe den Tippfehler, füge eine Dokumentation und Typhinweise hinzu
```

Seiteneffekt: Outputs sind konsistenter, weil Claude das volle Bild auf einmal sieht.

---

### Beitragen

Hast du eine Methode gefunden, die wirklich funktioniert und überprüfbar ist? Öffne ein **Issue** oder reiche einen **Pull Request** ein.

---

### Lizenz

MIT
