# Bedienungsanleitung für das Esperanto-Kanji-Konverter und Ruby-Annotationswerkzeug

## Einführung

Willkommen zum Esperanto-Kanji-Konverter und Ruby-Annotationswerkzeug! Diese Anwendung ermöglicht es Ihnen, Esperanto-Texte in verschiedene Formate zu konvertieren, wobei die Wörter durch andere Zeichen (z.B. chinesische Schriftzeichen/Kanji oder deutsche Übersetzungen) ersetzt werden können. Außerdem können Sie Ruby-Annotationen hinzufügen, die die ursprünglichen Esperanto-Wörter über oder unter den ersetzten Zeichen anzeigen.

Diese Anwendung besteht aus zwei Hauptkomponenten:
1. **Hauptseite**: Hier können Sie Texte umwandeln und verschiedene Ausgabeformate wählen
2. **Seite zur Erstellung von JSON-Dateien**: Hier können Sie eigene Ersetzungsregeln erstellen

## Inhalt dieser Anleitung

1. Übersicht der Hauptseite
2. Schritt-für-Schritt-Anleitung zur Textkonvertierung
3. Ausgabeformate verstehen
4. Spezielle Textmarkierungen mit % und @
5. Erstellen eigener Ersetzungsregeln (JSON)
6. Tipps und Tricks
7. Fehlerbehebung

## 1. Übersicht der Hauptseite

Die Hauptseite des Werkzeugs bietet Ihnen folgende Funktionen:

- **Laden von Ersetzungsregeln**: Sie können entweder Standard-Ersetzungsregeln verwenden oder Ihre eigenen hochladen
- **Eingabe von Esperanto-Text**: Entweder durch manuelle Eingabe oder durch Hochladen einer Textdatei
- **Auswahl des Ausgabeformats**: Verschiedene HTML-Formate mit Ruby-Annotationen oder einfache Textersetzung
- **Einstellung der Darstellung spezieller Esperanto-Zeichen**: Wahl zwischen verschiedenen Darstellungsformen (ĉ, cx, c^)
- **Parallelverarbeitung**: Option zur Beschleunigung großer Textkonvertierungen

## 2. Schritt-für-Schritt-Anleitung zur Textkonvertierung

### 2.1. Laden der Ersetzungsregeln (JSON-Datei)

1. Wählen Sie zu Beginn, ob Sie die Standard-JSON-Datei verwenden möchten oder eine eigene hochladen wollen.
   - **Standard-JSON verwenden**: Die Anwendung verwendet die integrierte Datei mit Esperanto-Wortstämmen und deren Übersetzungen.
   - **Datei hochladen**: Laden Sie Ihre eigene JSON-Datei hoch, die Sie entweder selbst erstellt haben oder von der zweiten Seite der Anwendung bezogen haben.

2. Falls Sie eine Beispiel-JSON herunterladen möchten, können Sie auf "Beispiel-JSON-Datei herunterladen" klicken.

### 2.2. Erweiterte Einstellungen (optional)

1. Klicken Sie auf "Einstellungen für die Parallelverarbeitung öffnen", um die Leistungsoptionen anzupassen.
2. Aktivieren Sie die Parallelverarbeitung, wenn Sie große Textmengen verarbeiten möchten.
3. Wählen Sie die Anzahl der gleichzeitigen Prozesse (2-4). Hinweis: Mehr Prozesse bedeuten nicht unbedingt bessere Leistung.

### 2.3. Ausgabeformat wählen

Wählen Sie das gewünschte Ausgabeformat aus dem Dropdown-Menü. Die Optionen werden später im Detail erklärt.

### 2.4. Eingabe des Esperanto-Textes

1. Wählen Sie, ob Sie den Text manuell eingeben oder eine Datei hochladen möchten.
2. Bei manueller Eingabe: Geben Sie den Esperanto-Text in das Textfeld ein.
3. Bei Datei-Upload: Laden Sie eine Textdatei (UTF-8-Format) hoch.

### 2.5. Darstellungsform der speziellen Esperanto-Zeichen wählen

Wählen Sie, wie die speziellen Buchstaben im Ergebnis dargestellt werden sollen:
- **Akzent auf dem Buchstaben**: Verwendet die Originalschreibweise mit Zirkumflex (ĉ, ĝ, ĥ, ĵ, ŝ, ŭ)
- **x-Format**: Verwendet die x-Notation (cx, gx, hx, jx, sx, ux)
- **^-Format**: Verwendet die Zirkumflex-Notation (c^, g^, h^, j^, s^, u^)

### 2.6. Konvertierung starten

1. Klicken Sie auf "Senden", um die Konvertierung zu starten.
2. Wenn Sie den Vorgang abbrechen möchten, klicken Sie auf "Abbrechen".

### 2.7. Ergebnis anzeigen und herunterladen

1. Nach der Konvertierung wird das Ergebnis angezeigt. Bei HTML-Formaten sehen Sie sowohl eine Vorschau als auch den generierten HTML-Code.
2. Klicken Sie auf "Ergebnis herunterladen", um die konvertierte Datei zu speichern.

## 3. Ausgabeformate verstehen

Die Anwendung bietet verschiedene Ausgabeformate an:

### 3.1. HTML-Formate mit Ruby-Annotationen

- **HTML-Format mit Ruby-Anmerkungen und Größenanpassung**:  
  Der Esperanto-Text wird als Haupttext beibehalten, mit den Übersetzungen/Kanji als Ruby-Anmerkungen darüber. Die Größe der Ruby-Anmerkungen wird automatisch angepasst.

- **HTML-Format mit Ruby-Anmerkungen, Größenanpassung und Kanji-Ersetzung**:  
  Die Übersetzungen/Kanji werden als Haupttext verwendet, mit dem Esperanto-Text als Ruby-Anmerkungen. Die Größe der Ruby-Anmerkungen wird automatisch angepasst.

- **HTML-Format**:  
  Einfaches HTML-Format mit Ruby-Anmerkungen ohne Größenanpassung.

- **HTML-Format mit Kanji-Ersetzung**:  
  Einfaches HTML-Format, bei dem die Kanji den Haupttext bilden und der Esperanto-Text als Ruby-Anmerkung erscheint.

### 3.2. Klammer-Formate

- **Format mit Klammern**:  
  Der Esperanto-Text bleibt der Haupttext, während die Übersetzungen/Kanji in Klammern dahinter stehen, z.B. "lingvo(Sprache)".

- **Format mit Klammern und Kanji-Ersetzung**:  
  Die Übersetzungen/Kanji bilden den Haupttext, während der Esperanto-Text in Klammern steht, z.B. "Sprache(lingvo)".

### 3.3. Einfache Ersetzung

- **Nur den ersetzten Text beibehalten**:  
  Nur die Übersetzungen/Kanji werden angezeigt, der Esperanto-Text wird vollständig ersetzt.

## 4. Spezielle Textmarkierungen mit % und @

Die Anwendung bietet zwei spezielle Markierungen, um die Konvertierung bestimmter Textabschnitte zu steuern:

### 4.1. %-Markierung: Teile unverändert beibehalten

Wenn Sie einen Teil des Textes von der Konvertierung ausschließen möchten, können Sie ihn in %-Zeichen einschließen:
```
La %Universala Esperanto-Asocio% estas grava organizaĵo.
```
In diesem Beispiel würde "Universala Esperanto-Asocio" nicht konvertiert werden.

**Wichtig**: Der Text zwischen %-Zeichen darf nicht länger als 50 Zeichen sein.

### 4.2. @-Markierung: Lokale Konvertierung

Wenn Sie einen Teil des Textes separat konvertieren möchten (ohne Berücksichtigung des Kontexts), können Sie ihn in @-Zeichen einschließen:
```
Mi lernas @Esperanto@ ĉiutage.
```
In diesem Beispiel würde "Esperanto" nach den grundlegenden Ersetzungsregeln konvertiert, ohne den umliegenden Text zu berücksichtigen.

**Wichtig**: Der Text zwischen @-Zeichen darf nicht länger als 18 Zeichen sein.

## 5. Erstellen eigener Ersetzungsregeln (JSON)

Auf der zweiten Seite der Anwendung können Sie eigene Ersetzungsregeln erstellen:

### 5.1. Zugriff auf die Erstellungsseite

Navigieren Sie zur "Seite zur Erstellung einer JSON-Datei, um Esperanto-Texte durch Zeichenketten (Kanji) zu ersetzen".

### 5.2. CSV-Datei vorbereiten

1. Wählen Sie "CSV-Datei hochladen" oder "Standarddatei verwenden".
2. Bei Hochladen: Die CSV-Datei sollte zwei Spalten enthalten - Esperanto-Wortstamm und Übersetzung/Kanji.
3. Sie können auch Beispiel-CSV-Dateien herunterladen, um das richtige Format zu sehen.

### 5.3. JSON-Datei(en) vorbereiten

1. Wählen Sie für beide Optionen "JSON-Datei hochladen" oder "Standarddatei verwenden":
   - Die erste JSON-Datei definiert die Regeln zur Zerlegung von Esperanto-Wortstämmen
   - Die zweite JSON-Datei enthält benutzerdefinierte Ersetzungszeichenketten

### 5.4. Ausgabeformat wählen

Wählen Sie das gewünschte Ausgabeformat aus dem Dropdown-Menü. Dies sollte dem Format entsprechen, das Sie später in der Hauptanwendung verwenden möchten.

### 5.5. JSON-Datei erstellen

1. Klicken Sie auf "JSON-Datei für die Ersetzung erstellen".
2. Nach der Verarbeitung können Sie die erstellte JSON-Datei herunterladen.
3. Diese Datei können Sie dann in der Hauptanwendung hochladen.

## 6. Tipps und Tricks

### 6.1. Optimierung für große Texte

- Aktivieren Sie die Parallelverarbeitung für große Textmengen.
- Bei sehr großen Texten sollten Sie diese in kleinere Teile aufteilen und separat verarbeiten.

### 6.2. Verwendung der Ruby-Annotationen

- Ruby-Annotationen funktionieren am besten in modernen Browsern.
- Wenn Sie die Ergebnisse in andere Programme importieren, verwenden Sie am besten die Klammer-Formate.

### 6.3. Anpassung der JSON-Regeln

- Wenn Sie häufig bestimmte Wörter in einer anderen Weise übersetzen möchten, erstellen Sie Ihre eigene JSON-Datei.
- Die Zerlegungsregeln für Esperanto-Wortstämme können angepasst werden, um Präfixe, Suffixe und Wortendungen genauer zu erkennen.

## 7. Fehlerbehebung

### 7.1. Unerwartete Konvertierungen

Wenn Wörter nicht wie erwartet konvertiert werden:
- Prüfen Sie, ob der Wortstamm in der CSV-Datei enthalten ist.
- Verwenden Sie die @-Markierung, um das Wort lokal zu konvertieren.
- Erstellen Sie eine benutzerdefinierte JSON-Regel für dieses spezifische Wort.

### 7.2. Fehler beim Hochladen von Dateien

- Stellen Sie sicher, dass Ihre Dateien im UTF-8-Format kodiert sind.
- Prüfen Sie, ob die CSV-Datei das richtige Format hat (zwei Spalten).
- Bei JSON-Dateien: Stellen Sie sicher, dass sie syntaktisch korrekt sind.

### 7.3. Leistungsprobleme

- Reduzieren Sie die Textmenge, wenn die Konvertierung zu lange dauert.
- Schalten Sie die Parallelverarbeitung ein oder aus, um die beste Leistung zu finden.
- Bei großen JSON-Dateien kann das erste Laden länger dauern.

## Zusammenfassung

Das Esperanto-Kanji-Konverter und Ruby-Annotationswerkzeug bietet Ihnen vielfältige Möglichkeiten, Esperanto-Texte zu konvertieren und mit Übersetzungen oder Kanji zu versehen. Von einfachen Klammer-Formaten bis hin zu komplexen HTML-Strukturen mit Ruby-Annotationen können Sie das Format wählen, das Ihren Bedürfnissen am besten entspricht.

Mit der Möglichkeit, eigene Ersetzungsregeln zu erstellen, können Sie das Werkzeug an Ihre spezifischen Anforderungen anpassen. Die speziellen Markierungen mit % und @ geben Ihnen zusätzliche Kontrolle über den Konvertierungsprozess.

Viel Erfolg und Freude bei der Verwendung dieses Werkzeugs für Ihre Esperanto-Projekte!
