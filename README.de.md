<p align="center">
  <img src="banner.png" alt="Claude Token Saver Banner" width="100%" style="max-width: 1000px; height: auto;">
</p>

# <p align="center">Claude Token Saver</p>

<p align="center">
  <b>Claude Token Saver</b> ist eine Sammlung praktischer Methoden zur Reduzierung des Token-Verbrauchs bei der Verwendung von Claude und Claude Code. In echten Arbeitsabläufen von Entwicklern getestet und bestätigt — keine Garantie, aber es funktioniert.
</p>

<h1 align="center">

[🇺🇸](https://github.com/duman-dev/claude-token-saver/blob/main/README.md) [🇷🇺](https://github.com/duman-dev/claude-token-saver/blob/main/README.ru.md) [🇩🇪](https://github.com/duman-dev/claude-token-saver/blob/main/README.de.md) [🇹🇷](https://github.com/duman-dev/claude-token-saver/blob/main/README.tr.md)

</h1>

> **Hinweis:** Diese Methoden basieren auf praktischer Erfahrung und Beobachtung. Wir haben gesehen, dass sie konsistent funktionieren, aber die Ergebnisse können je nach Nutzungsmuster, Sitzungsdauer und Modellversion variieren.

---

### Methoden

* [**1 — Prägnante Anfragen**](#1--prägnante-anfragen): Sag Claude, er soll Füllwort weglassen. Weniger Output, gleiches Ergebnis.
* [**2 — Bearbeiten statt Nachfolge**](#2--bearbeiten-statt-nachfolge): Ersetze gescheiterte Versuche, statt sie zu stapeln.
* [**3 — Kontext komprimieren**](#3--kontext-komprimieren): Setze lange Sitzungen zurück, ohne Fortschritt zu verlieren.
* [**4 — Batchen statt Splitten**](#4--batchen-statt-splitten): Kombiniere verwandte Aufgaben in einer Nachricht statt vielen.
* [**5 — Token-Optimierung mit Skills wie Caveman**](#5--token-optimierung-mit-skills-wie-caveman): Komprimiere Ein- und Ausgaben mit einem dedizierten Token-Optimierungs-Skill/Proxy.

---

## 1 — Prägnante Anfragen

Claudes Standardstil ist gesprächig. Jede Einleitung, Zusammenfassung und "Ich helfe dir gerne" kostet Output-Token — und wird in zukünftigen Turns als Teil der Historie erneut gelesen.

**Lösung:** Füge dies in deine `CLAUDE.md` (Claude Code) oder **Einstellungen → Profil → Benutzereinstellungen** (claude.ai) ein:

```
Reply in the language user employs. Use most concise form possible.
Skip pleasantries, preambles, recaps.
No "I'd be happy to", "Great question", "Let me explain".
Drop articles, filler words wherever meaning stays clear.
Prefer short declarative sentences. If tool call needed,
run first and show only result. Do not narrate steps.
Prioritize code/data format over prose.
No explanations unless essential. Output only.
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
Summarize our entire conversation with language user employs
so I can paste it into a
new chat and continue without losing context. Include:
(1) the original goal or problem
(2) key decisions made and why
(3) any code, config, or data we settled on, verbatim, in code blocks
(4) open questions and next steps
Use short sections with headings. Skip small talk and
exploratory tangents. Optimize the summary for a future
Claude reading it cold.
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

## 5 — Token-Optimierung mit Skills wie Caveman

Die obigen Methoden reduzieren Token-Verschwendung durch *wie du* Claude nutzt. Ein dedizierter Skill kann zusätzlich komprimieren, *was gesendet wird* — auf beiden Seiten der Konversation.

[**Caveman**](https://github.com/juliusbrussee/caveman) ist ein Open-Source-Skill/Plugin, das Agenten-Output komprimiert (knappere, "caveman-artige" Antworten — berichtete ~87 % Reduktion bei manchen Aufgaben) und über seinen optionalen lokalen Proxy auch den Input komprimiert, bevor er das Modell erreicht (JSON, Logs, Code-Diffs und lange Texte werden auf das Wesentliche gekürzt, Originale bleiben bei Bedarf lokal wiederherstellbar).

**Lösung:** Installiere den Skill und aktiviere ihn pro Sitzung.

```bash
curl -fsSL https://raw.githubusercontent.com/JuliusBrussee/caveman/v1.10.0/install.sh | bash
```

Dann in Claude Code:

```
/caveman [lite|full|ultra|off]
```

Die Einsparungen variieren je nach Workload — betrachte die Benchmarks des Projekts als Ausgangspunkt, nicht als Garantie, und vergleiche sie mit deiner eigenen Baseline, bevor du dich bei latenz- oder kostenkritischer Arbeit darauf verlässt.

---

### Beitragen

Hast du eine Methode gefunden, die wirklich funktioniert und überprüfbar ist? Öffne ein **Issue** oder reiche einen **Pull Request** ein.

---

### Lizenz

MIT
