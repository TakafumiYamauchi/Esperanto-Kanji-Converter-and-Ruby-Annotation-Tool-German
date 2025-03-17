# Benutzerhandbuch: Esperanto-Kanji-Konverter und Ruby-Anmerkungstool

## Einführung

Willkommen zum Esperanto-Kanji-Konverter und Ruby-Anmerkungstool! Diese Anwendung ermöglicht Ihnen, Esperanto-Texte auf verschiedene Weisen zu bearbeiten:

- Ersetzen von Esperanto-Wörtern durch entsprechende Kanji (chinesische Zeichen) oder deutsche Übersetzungen
- Hinzufügen von Ruby-Anmerkungen (kleine Erklärungstexte über den Wörtern, ähnlich wie Furigana im Japanischen)
- Verschiedene Ausgabeformate wie HTML oder Klammernotation

Die Anwendung besteht aus zwei Hauptkomponenten:
1. **Hauptseite**: Zur Umwandlung von Esperanto-Texten
2. **Seite zur JSON-Generierung**: Zur Erstellung eigener Ersetzungsregeln

## 1. Hauptseite: Umwandlung von Esperanto-Texten

### 1.1 Erste Schritte

Wenn Sie die Hauptseite öffnen, sehen Sie den Titel "Ersetzung von Esperanto-Text durch Kanji oder HTML-Anmerkungen" und können mit der Konvertierung beginnen.

### 1.2 JSON-Datei für die Ersetzungsregeln auswählen

Als Erstes müssen Sie eine JSON-Datei mit Ersetzungsregeln wählen:

- **Standard-JSON verwenden**: Verwendet die vorinstallierte Standarddatei mit Ersetzungsregeln
- **Datei hochladen**: Laden Sie Ihre eigene JSON-Datei mit Ersetzungsregeln hoch

Sie können auch eine Beispiel-JSON-Datei herunterladen, indem Sie den Abschnitt "Beispiel-JSON-Datei herunterladen" erweitern.

### 1.3 Erweiterte Einstellungen

Im Abschnitt "Erweiterte Einstellungen" können Sie die Parallelverarbeitung aktivieren:

- **Parallelverarbeitung verwenden**: Aktiviert die Verwendung mehrerer Prozessoren zur schnelleren Verarbeitung
- **Anzahl gleichzeitiger Prozesse**: Legt fest, wie viele Prozesse parallel ausgeführt werden (2-4)

### 1.4 Ausgabeformat wählen

Wählen Sie das gewünschte Ausgabeformat aus den folgenden Optionen:

- **HTML-Format mit Ruby-Anmerkungen und Größenanpassung**: Fügt Ruby-Anmerkungen hinzu und passt deren Größe automatisch an
- **HTML-Format mit Ruby-Anmerkungen, Größenanpassung und Kanji-Ersetzung**: Wie oben, aber mit Kanji anstelle des Originaltextes
- **HTML-Format**: Einfaches HTML-Format mit Ruby-Anmerkungen
- **HTML-Format mit Kanji-Ersetzung**: Einfaches HTML-Format mit Kanji anstelle des Originaltextes
- **Format mit Klammern**: Zeigt Übersetzungen in Klammern hinter den Originalwörtern
- **Format mit Klammern und Kanji-Ersetzung**: Zeigt das Original in Klammern hinter den Kanji/Übersetzungen
- **Nur den ersetzten Text beibehalten**: Zeigt nur die Ersetzungen ohne den Originaltext

### 1.5 Eingabetext bereitstellen

Sie haben zwei Möglichkeiten, den zu verarbeitenden Esperanto-Text bereitzustellen:

- **Manuelle Eingabe**: Geben Sie den Text direkt in das Textfeld ein
- **Datei hochladen**: Laden Sie eine Textdatei (UTF-8-kodiert) hoch

### 1.6 Spezielle Markierungen im Text

Sie können Teile des Textes speziell markieren:

- **%Text%**: Text zwischen %-Zeichen wird **nicht ersetzt** und im Ergebnis unverändert beibehalten
- **@Text@**: Text zwischen @-Zeichen wird **nur innerhalb dieses Fragments** ersetzt (lokale Ersetzung)

### 1.7 Darstellungsform der Esperanto-Sonderzeichen

Wählen Sie, wie die speziellen Esperanto-Zeichen (ĉ, ĝ, ĥ, ĵ, ŝ, ŭ) im Ergebnis dargestellt werden sollen:

- **Akzent auf dem Buchstaben**: ĉ, ĝ, ĥ, ĵ, ŝ, ŭ
- **x-Format**: cx, gx, hx, jx, sx, ux
- **^-Format**: c^, g^, h^, j^, s^, u^

### 1.8 Verarbeitung und Ergebnis

Nach dem Klick auf "Senden" wird der Text verarbeitet. Das Ergebnis wird je nach gewähltem Ausgabeformat angezeigt:

- Bei HTML-Formaten: Zwei Registerkarten für "HTML-Vorschau" und "Ergebnis (HTML-Code)"
- Bei anderen Formaten: Eine Registerkarte "Ergebnis-Text"

Sie können das Ergebnis über den Button "Ergebnis herunterladen" als Datei speichern.

## 2. Seite zur JSON-Generierung

Diese Seite ermöglicht es Ihnen, eigene JSON-Dateien mit Ersetzungsregeln zu erstellen.

### 2.1 Einführung

Auf dieser Seite können Sie eine JSON-Datei erstellen, die für die Ersetzung von Esperanto-Texten auf der Hauptseite verwendet wird. Sie müssen dafür:

1. Eine CSV-Datei mit Esperanto-Wortstämmen und deutschen Übersetzungen hochladen oder die Standarddatei verwenden
2. Eine JSON-Datei mit Regeln zur Stammzerlegung hochladen oder die Standarddatei verwenden
3. Auf "JSON-Datei für Ersetzung erstellen" klicken, um die Ersetzungs-JSON zu generieren und herunterzuladen

### 2.2 Beispieldateien

Im Abschnitt "Liste der Beispieldateien" können Sie verschiedene Vorlagen herunterladen:

- **Beispiel-CSV**: Enthält Esperanto-Wortstämme mit deutschen Übersetzungen und Ruby-Anmerkungen
- **Beispiel-JSON**: Enthält Regeln zur Zerlegung von Esperanto-Wörtern
- Weitere CSV- und Excel-Dateien mit Übersetzungen in verschiedenen Sprachen

### 2.3 Ausgabeformat wählen

Wählen Sie das Format, in dem die Ersetzungen in der generierten JSON-Datei gespeichert werden sollen. Die Optionen entsprechen denen auf der Hauptseite.

### 2.4 CSV-Datei vorbereiten (Schritt 1)

Wählen Sie, ob Sie eine eigene CSV-Datei hochladen oder die Standarddatei verwenden möchten. Die CSV-Datei sollte Esperanto-Wortstämme und deren Übersetzungen enthalten.

### 2.5 JSON-Dateien vorbereiten (Schritt 2)

Hier müssen Sie zwei JSON-Dateien vorbereiten:

1. **JSON-Datei für die Zerlegung von Esperanto-Wortstämmen**: Definiert, wie Esperanto-Wörter in ihre Bestandteile zerlegt werden
2. **JSON-Datei für benutzerdefinierte Ersetzungszeichenketten**: Enthält spezielle Ersetzungen für bestimmte Wörter

Für beide können Sie entweder eine eigene Datei hochladen oder die Standarddatei verwenden.

### 2.6 Erweiterte Einstellungen (Schritt 3)

Wie auf der Hauptseite können Sie hier die Parallelverarbeitung aktivieren und die Anzahl der Prozesse festlegen.

### 2.7 JSON-Datei generieren

Klicken Sie auf "JSON-Datei für die Ersetzung erstellen", um die Ersetzungsregeln zu generieren. Nach der Verarbeitung können Sie die erzeugte JSON-Datei herunterladen.

## 3. Funktionsweise der Ersetzungsregeln

### 3.1 Grundprinzip

Die App zerlegt Esperanto-Wörter in ihre Bestandteile (Wortstämme, Präfixe, Suffixe) und ersetzt diese durch entsprechende Kanji oder Übersetzungen. Zum Beispiel:

- Das Wort "amiko" (Freund) wird zerlegt in den Stamm "amik" + Endung "o"
- Der Stamm "amik" wird dann durch seine Übersetzung/Kanji ersetzt
- Je nach Ausgabeformat wird das Ergebnis mit Ruby-Anmerkungen oder in Klammern dargestellt

### 3.2 Spezielle Syntax

Die App unterstützt zwei spezielle Markierungen im Text:

- **%Text%**: Schützt Text vor Ersetzungen
  - Beispiel: "Mi estas %homo% kaj vi estas hundo." → Nur "homo" bleibt unverändert
  
- **@Text@**: Ermöglicht lokale Ersetzungen
  - Beispiel: "Mi @amas@ vin." → Nur innerhalb von "amas" werden lokale Ersetzungsregeln angewendet

### 3.3 Esperanto-Sonderzeichen

Die App kann mit verschiedenen Darstellungen der Esperanto-Sonderzeichen umgehen:

- Akzent auf dem Buchstaben: ĉ, ĝ, ĥ, ĵ, ŝ, ŭ
- x-Format: cx, gx, hx, jx, sx, ux
- ^-Format: c^, g^, h^, j^, s^, u^

## 4. Tipps und Beispiele

### 4.1 Optimale Ergebnisse erzielen

- Verwenden Sie die HTML-Formate mit Größenanpassung für die besten Ruby-Anmerkungen
- Bei sehr langen Ruby-Anmerkungen fügt die App automatisch Zeilenumbrüche ein
- Bei der Erstellung eigener Ersetzungsregeln sollten Sie mit den Beispieldateien beginnen

### 4.2 CSV-Format für eigene Ersetzungsregeln

Ihre CSV-Datei sollte folgendes Format haben:
- Erste Spalte: Esperanto-Wortstamm (z.B. "am")
- Zweite Spalte: Übersetzung oder Kanji (z.B. "Liebe" oder "愛")

### 4.3 Beispieltext

Hier ist ein einfaches Beispiel, wie die App einen Esperanto-Text verarbeiten könnte:

Original:
```
La suno brilas. Mi amas vin.
```

Ergebnis (HTML-Format mit Ruby-Anmerkungen):
```html
<ruby>La<rt>Die</rt></ruby> <ruby>sun<rt>Sonne</rt></ruby><ruby>o<rt>-o</rt></ruby> <ruby>bril<rt>glänzen</rt></ruby><ruby>as<rt>-as</rt></ruby>. <ruby>Mi<rt>Ich</rt></ruby> <ruby>am<rt>lieben</rt></ruby><ruby>as<rt>-as</rt></ruby> <ruby>vin<rt>dich</rt></ruby>.
```

Dies würde im Browser angezeigt als:
> L͟a͟ s͟u͟n͟o͟ b͟r͟i͟l͟a͟s͟. M͟i͟ a͟m͟a͟s͟ v͟i͟n͟.  
> Die Sonne -o glänzen -as Ich lieben -as dich

## 5. Verfügbare Sprachversionen

Die App ist in verschiedenen Sprachen verfügbar. Links zu allen Versionen finden Sie am Ende der Hauptseite.

## 6. Fehlerbehebung

- **Problem**: Die Ausgabe enthält unerwartete Zeichen  
  **Lösung**: Stellen Sie sicher, dass alle Dateien UTF-8-kodiert sind

- **Problem**: Bestimmte Wörter werden nicht korrekt ersetzt  
  **Lösung**: Überprüfen Sie die JSON-Ersetzungsregeln oder erstellen Sie eigene für spezifische Wörter

- **Problem**: Die App reagiert langsam bei großen Texten  
  **Lösung**: Aktivieren Sie die Parallelverarbeitung in den erweiterten Einstellungen

---

Diese Anwendung ist ein leistungsstarkes Werkzeug für Esperanto-Lernende und -Enthusiasten. Mit ihrer Hilfe können Sie Texte nicht nur übersetzen, sondern auch visuell aufbereiten, was das Lernen und Verstehen der Sprache erheblich erleichtert.