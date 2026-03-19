# Fragetechniken

Wie man die richtigen Fragen stellt um von der Idee zum Scope zu kommen.

## Grundregeln

1. **Maximal 2-3 Fragen pro Nachricht** — nicht verhören
2. **Geschlossene Fragen via `ask_user_input`** — offene via Freitext
3. **Nicht alles auf einmal fragen** — schrittweise vertiefen
4. **Erst verstehen, dann strukturieren** — nicht zu früh in Templates denken

## Phase 1: Von der Idee zum Problem

### Wenn die Idee vage ist

User sagt etwas wie "ich will was mit AI machen" oder "ich hab eine Idee für eine App".

**Öffnende Fragen:**
- "Erzähl mal — was schwebt dir vor?"
- "Was hat dich auf die Idee gebracht?"
- "Gibt es ein konkretes Problem das du lösen willst?"

**Vertiefende Fragen (nachdem die erste Antwort kam):**
- "Wer würde das nutzen ausser dir?"
- "Wie löst du das Problem aktuell?"
- "Was nervt dich am meisten an der aktuellen Situation?"

### Wenn die Idee schon konkret ist

User sagt etwas wie "ich will ein Dashboard für mein Homelab bauen".

**Verifikations-Fragen:**
- "Was genau willst du im Dashboard sehen?"
- "Warum reichen bestehende Tools wie Portainer oder Homepage nicht?"
- "Woran erkennst du dass es fertig ist?"

### Wenn der User mit einer Lösung kommt

User sagt "ich will React + Supabase nutzen" statt ein Problem zu beschreiben.

**Umlenk-Fragen (sanft, nicht belehrend):**
- "Klingt gut — was baust du damit?"
- "Was soll am Ende rauskommen?"
- "Welches Problem löst das für dich?"

## Phase 2: Vom Problem zum Scope

### MVP definieren

Das Schwierigste: den User davon überzeugen, klein anzufangen.

**Hilfreiche Fragen:**
- "Wenn du nur EINE Sache bauen könntest — welche wäre es?"
- "Was ist die minimale Version die dir schon Nutzen bringt?"
- "Stell dir vor du hast nur ein Wochenende — was muss drin sein?"

**Wenn der Scope zu gross wird:**
- "Das sind viele Features — lass uns priorisieren. Was brauchst du am dringendsten?"
- "Welche Features kannst du erst mal von Hand machen, statt sie zu automatisieren?"

Via `ask_user_input` priorisieren lassen:
```
Frage: "Welche Features sind für V1 essentiell?"
Typ: multi_select
Optionen: [Die genannten Features]
```

### Constraints aufdecken

Nicht direkt fragen "was sind deine Constraints?" — das ist zu abstrakt.
Stattdessen konkrete Fragen stellen:

- "Wo soll das laufen?" (Plattform)
- "Braucht es ein Login?" (Auth)
- "Muss es von ausserhalb erreichbar sein?" (Zugriff)
- "Gibt es eine Deadline?" (Zeitrahmen)
- "Arbeitest du allein daran?" (Team)

### Abhängigkeiten aufdecken

- "Nutzt du schon bestehende Services die das neue Projekt braucht?"
- "Gibt es externe APIs oder Datenquellen?"
- "Muss es mit bestehenden Tools zusammenarbeiten?"

## Gesprächsmuster erkennen

### Der Perfektionist
**Erkennung:** Will alles von Anfang an perfekt, riesiger Feature-Scope.
**Umgang:** Sanft zum MVP führen. "Lass uns erst eine Version bauen die
funktioniert, dann können wir verfeinern."

### Der Unentschlossene
**Erkennung:** Wechselt ständig die Richtung, kann sich nicht festlegen.
**Umgang:** Konkrete Optionen anbieten via `ask_user_input`. Entscheidungen
festhalten und bestätigen lassen.

### Der Eilige
**Erkennung:** Will sofort coden, Planung fühlt sich wie Zeitverschwendung an.
**Umgang:** Kurz halten. "5 Minuten Planung, dann geht's los." Nicht auf
allen Details bestehen — das Minimum reicht.

### Der Techniker
**Erkennung:** Redet sofort über Stack, Libraries, Architektur.
**Umgang:** Kurz mitspielen, dann zum Problem zurücklenken. "Guter Stack —
lass uns kurz festhalten was das Problem ist, dann passt der Stack auch."

## Fragenfluss-Diagramm

```
User hat Idee
│
├── Vage? → Öffnende Fragen → Vertiefende Fragen
│
├── Konkret? → Verifikations-Fragen
│
└── Lösung statt Problem? → Umlenk-Fragen
│
↓
Problem-Statement → Bestätigung
│
↓
Projekttyp → Features → MVP vs. V2+ → Constraints → Abhängigkeiten
│
↓
Requirements-Dokument → Bestätigung
```
