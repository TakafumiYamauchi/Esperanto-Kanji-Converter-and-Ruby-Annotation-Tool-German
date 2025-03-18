# Benutzerhandbuch: Esperanto-Kanji-Konverter und Ruby-Annotationstool

## Inhaltsverzeichnis

- [Benutzerhandbuch: Esperanto-Kanji-Konverter und Ruby-Annotationstool](#benutzerhandbuch-esperanto-kanji-konverter-und-ruby-annotationstool)
  - [Inhaltsverzeichnis](#inhaltsverzeichnis)
  - [Einführung](#einführung)
  - [Hauptfunktionen der Anwendung](#hauptfunktionen-der-anwendung)
  - [Erste Schritte](#erste-schritte)
  - [Hauptseite: Konvertierung von Esperanto-Texten](#hauptseite-konvertierung-von-esperanto-texten)
    - [Laden der Ersetzungsdatei](#laden-der-ersetzungsdatei)
    - [Erweiterte Einstellungen](#erweiterte-einstellungen)
    - [Auswahl des Ausgabeformats](#auswahl-des-ausgabeformats)
    - [Eingabe des Esperanto-Textes](#eingabe-des-esperanto-textes)
    - [Spezielle Textmarkierungen](#spezielle-textmarkierungen)
    - [Darstellung der Esperanto-Sonderzeichen](#darstellung-der-esperanto-sonderzeichen)
    - [Verarbeitung und Ergebnis](#verarbeitung-und-ergebnis)
  - [Seite zur JSON-Generierung](#seite-zur-json-generierung)
    - [Vorbereitung der CSV-Datei](#vorbereitung-der-csv-datei)
    - [Vorbereitung der JSON-Dateien](#vorbereitung-der-json-dateien)
    - [Generierung der Ersetzungs-JSON](#generierung-der-ersetzungs-json)
  - [Technische Details](#technische-details)
    - [Unterschiedliche Ausgabeformate](#unterschiedliche-ausgabeformate)
    - [Funktionsweise der Ruby-Annotationen](#funktionsweise-der-ruby-annotationen)
  - [Tipps und Tricks](#tipps-und-tricks)
  - [Häufig gestellte Fragen](#häufig-gestellte-fragen)

## Einführung

Willkommen zum Benutzerhandbuch des Esperanto-Kanji-Konverters und Ruby-Annotationstools! Diese Anwendung ermöglicht es Ihnen, Esperanto-Texte in verschiedene Formate zu konvertieren, wobei die Wortstämme durch entsprechende Kanji (chinesische Schriftzeichen) oder deutsche Übersetzungen ersetzt werden können. Die Anwendung bietet dabei verschiedene Darstellungsoptionen, von denen die Ruby-Annotation besonders hervorzuheben ist – hierbei werden die ursprünglichen Esperanto-Wörter als kleine Anmerkungen über den Kanji oder Übersetzungen angezeigt.

Diese Webanwendung wurde mit Streamlit entwickelt und ist in mehreren Sprachen verfügbar, darunter diese deutsche Version, die speziell für deutschsprachige Nutzer angepasst wurde.

## Hauptfunktionen der Anwendung

Die Anwendung besteht aus zwei Hauptkomponenten:

1. **Hauptseite: Konvertierung von Esperanto-Texten**
   - Umwandlung von Esperanto-Texten in verschiedene Formate
   - Ersetzung von Esperanto-Wortstämmen durch Kanji oder deutsche Übersetzungen
   - Verschiedene Ausgabeformate: HTML mit Ruby-Anmerkungen, Klammer-Format, einfache Ersetzung
   - Spezielle Textmarkierungen zum Ausschließen oder lokalen Ersetzen von Text
   - Anpassung der Darstellung der Esperanto-Sonderzeichen

2. **Seite zur JSON-Generierung**
   - Erstellung eigener Ersetzungsdateien für die Konvertierung
   - Import von CSV-Dateien mit Esperanto-Wortstämmen und deren Übersetzungen
   - Anpassung der Regeln zur Zerlegung von Esperanto-Wörtern
   - Generierung und Download der fertigen JSON-Ersetzungsdatei

## Erste Schritte

Um mit der Anwendung zu beginnen, öffnen Sie einfach den Link zur deutschen Version:
https://esperanto-kanji-converter-and-ruby-annotation-tool-german.streamlit.app/

Die Anwendung lädt im Browser und zeigt Ihnen die Hauptseite zur Konvertierung von Esperanto-Texten. Über das Seitenmenü können Sie zur zweiten Seite (JSON-Generierung) wechseln, falls Sie eigene Ersetzungsregeln erstellen möchten.

## Hauptseite: Konvertierung von Esperanto-Texten

Die Hauptseite ist das zentrale Element der Anwendung, auf dem Sie Esperanto-Texte konvertieren können.

### Laden der Ersetzungsdatei

Zuerst müssen Sie die JSON-Datei mit den Ersetzungsregeln laden:

1. Wählen Sie eine der folgenden Optionen:
   - **Standard-JSON verwenden**: Verwendet die vorinstallierte Ersetzungsdatei (empfohlen für Anfänger)
   - **Datei hochladen**: Laden Sie Ihre eigene JSON-Ersetzungsdatei hoch (für fortgeschrittene Nutzer)

2. Falls Sie eine Beispiel-JSON-Datei herunterladen möchten, klicken Sie auf den Abschnitt "Beispiel-JSON-Datei (für Ersetzungen) herunterladen" und dann auf den Download-Button.

### Erweiterte Einstellungen

Unter "Erweiterte Einstellungen" können Sie die Parallelverarbeitung konfigurieren:

1. Aktivieren Sie "Parallelverarbeitung verwenden", wenn Sie die Verarbeitung beschleunigen möchten (besonders nützlich bei langen Texten).
2. Wählen Sie die "Anzahl gleichzeitiger Prozesse" (2-4), um die Leistung an Ihren Computer anzupassen.

### Auswahl des Ausgabeformats

Wählen Sie das gewünschte Ausgabeformat für Ihren konvertierten Text:

- **HTML-Format mit Ruby-Anmerkungen und Größenanpassung**: Zeigt Esperanto-Wörter als kleine Anmerkungen über den Übersetzungen an, mit automatischer Größenanpassung.
- **HTML-Format mit Ruby-Anmerkungen, Größenanpassung und Kanji-Ersetzung**: Wie oben, aber mit den Kanji/Übersetzungen als Haupttext und den Esperanto-Wörtern als Anmerkungen.
- **HTML-Format**: Einfachere HTML-Darstellung mit Ruby-Anmerkungen ohne Größenanpassung.
- **HTML-Format mit Kanji-Ersetzung**: Wie oben, aber mit vertauschten Positionen von Original und Übersetzung.
- **Format mit Klammern**: Stellt die Übersetzungen in Klammern hinter den Esperanto-Wörtern dar.
- **Format mit Klammern und Kanji-Ersetzung**: Stellt die Esperanto-Wörter in Klammern hinter den Übersetzungen dar.
- **Nur den ersetzten Text beibehalten**: Zeigt nur die Übersetzungen ohne die ursprünglichen Esperanto-Wörter an.

### Eingabe des Esperanto-Textes

Sie haben zwei Möglichkeiten, den Esperanto-Text einzugeben:

1. **Manuelle Eingabe**: Geben Sie Ihren Esperanto-Text direkt in das Textfeld ein.
2. **Datei hochladen**: Laden Sie eine Textdatei (UTF-8-kodiert) hoch, die den zu konvertierenden Text enthält.

Nach der Texteingabe wird dieser im Textfeld angezeigt, wo Sie ihn bei Bedarf noch bearbeiten können.

### Spezielle Textmarkierungen

Sie können bestimmte Teile des Textes mit speziellen Markierungen versehen:

- **%Text%**: Text, der zwischen %-Zeichen steht (bis zu 50 Zeichen), wird **nicht ersetzt** und im Ergebnis unverändert beibehalten.
- **@Text@**: Text, der zwischen @-Zeichen steht (bis zu 18 Zeichen), wird **lokal ersetzt** (nur innerhalb dieses Fragments).

Diese Markierungen sind nützlich, um bestimmte Wörter oder Phrasen von der Ersetzung auszuschließen oder speziell zu behandeln.

### Darstellung der Esperanto-Sonderzeichen

Wählen Sie, wie die speziellen Esperanto-Zeichen im Ergebnis dargestellt werden sollen:

- **Akzent auf dem Buchstaben (ĉ → c + ˆ)**: Verwendet die Standardform mit Zirkumflex.
- **x-Format (ĉ → cx)**: Verwendet das x-System, bei dem Sonderzeichen durch Hinzufügen eines x dargestellt werden.
- **^-Format (ĉ → c^)**: Verwendet das Caret-Symbol (^) nach dem Buchstaben.

### Verarbeitung und Ergebnis

Nach der Konfiguration klicken Sie auf "Senden", um den Text zu verarbeiten. Die Anwendung zeigt dann das Ergebnis an:

- Bei HTML-Formaten werden zwei Tabs angezeigt: Eine HTML-Vorschau und der generierte HTML-Code.
- Bei anderen Formaten wird der Ergebnistext direkt angezeigt.

Sie können das Ergebnis über den Button "Ergebnis herunterladen" als Datei speichern.

## Seite zur JSON-Generierung

Die zweite Seite der Anwendung ermöglicht es Ihnen, eigene Ersetzungs-JSON-Dateien zu erstellen. Diese Funktion ist für fortgeschrittene Nutzer gedacht, die spezifische Ersetzungsregeln definieren möchten.

### Vorbereitung der CSV-Datei

Zuerst müssen Sie eine CSV-Datei mit Esperanto-Wortstämmen und deren Übersetzungen vorbereiten:

1. Wählen Sie eine der folgenden Optionen:
   - **Standard-CSV verwenden**: Verwendet die vorinstallierte CSV-Datei mit Esperanto-Wortstämmen und deutschen Übersetzungen.
   - **Datei hochladen**: Laden Sie Ihre eigene CSV-Datei hoch, die die Zuordnung zwischen Esperanto-Wortstämmen und Übersetzungen enthält.

2. Unter "Liste der Beispieldateien" können Sie verschiedene Beispiel-CSVs herunterladen, um sie als Vorlage zu verwenden oder anzupassen.

### Vorbereitung der JSON-Dateien

Als Nächstes benötigen Sie zwei JSON-Dateien, die die Regeln zur Zerlegung und Ersetzung definieren:

1. **JSON-Datei zur Zerlegung von Esperanto-Wortstämmen**:
   - Wählen Sie "Standarddatei verwenden" oder "JSON-Datei hochladen".
   - Diese Datei definiert, wie Esperanto-Wörter in Stämme zerlegt werden (z.B. wie Verbendungen oder Suffixe behandelt werden).

2. **JSON-Datei für benutzerdefinierte Ersetzungszeichenketten**:
   - Wählen Sie wieder "Standarddatei verwenden" oder "JSON-Datei hochladen".
   - Diese Datei ermöglicht es, bestimmten Wörtern spezielle Formatierungen oder Übersetzungen zuzuweisen.

Auch hier können Sie Beispieldateien herunterladen, um sich mit dem Format vertraut zu machen.

### Generierung der Ersetzungs-JSON

Nach der Vorbereitung der Eingabedateien:

1. Wählen Sie das gewünschte **Ausgabeformat** (identisch mit den Optionen der Hauptseite).
2. Konfigurieren Sie optional die **Parallelverarbeitung** für schnellere Generierung.
3. Klicken Sie auf "JSON-Datei für die Ersetzung erstellen".

Die Anwendung verarbeitet die Eingabedateien und generiert eine umfassende JSON-Datei, die alle Ersetzungsregeln enthält. Diese können Sie dann herunterladen und in der Hauptseite verwenden.

## Technische Details

### Unterschiedliche Ausgabeformate

Die Anwendung unterstützt verschiedene Ausgabeformate, die jeweils unterschiedliche Darstellungsmöglichkeiten bieten:

1. **HTML-Formate mit Ruby-Anmerkungen**:
   - Verwenden HTML `<ruby>` und `<rt>` Tags zur Darstellung von Anmerkungen.
   - Die "Größenanpassung"-Varianten passen die Schriftgröße der Anmerkungen automatisch an, basierend auf dem Verhältnis zwischen Original und Übersetzung.
   - Beispiel: `<ruby>esperanto<rt class="M_M">Esperanto</rt></ruby>`

2. **Klammer-Formate**:
   - Einfache Textdarstellung mit Klammern für die Übersetzung.
   - Beispiel: `esperanto(Esperanto)` oder `Esperanto(esperanto)`

3. **Einfache Ersetzung**:
   - Zeigt nur die Übersetzungen ohne die ursprünglichen Esperanto-Wörter an.

### Funktionsweise der Ruby-Annotationen

Ruby-Annotationen sind ein Merkmal der Anwendung, das besonders für die Darstellung von Übersetzungen über dem Originaltext nützlich ist:

- Die Annotationen werden in HTML mit CSS-Styling dargestellt.
- Die Größe der Anmerkungen wird automatisch angepasst, um gute Lesbarkeit zu gewährleisten.
- Bei sehr langen Übersetzungen werden diese automatisch umgebrochen.
- Verschiedene CSS-Klassen (S_S, M_M, L_L etc.) werden verwendet, um die Größe der Anmerkungen zu steuern.

## Tipps und Tricks

- **Für Anfänger**: Beginnen Sie mit der Standardkonfiguration und einem kurzen Esperanto-Text, um sich mit der Funktionsweise vertraut zu machen.
- **Markierungen verwenden**: Nutzen Sie die %...% Markierung, um Eigennamen oder spezielle Begriffe von der Ersetzung auszuschließen.
- **Lokale Ersetzungen**: Verwenden Sie die @...@ Markierung, um bestimmte Textabschnitte mit spezifischen Regeln zu ersetzen.
- **Größere Texte**: Aktivieren Sie die Parallelverarbeitung für schnellere Ergebnisse bei umfangreichen Texten.
- **Eigene Ersetzungen**: Wenn Sie regelmäßig mit eigenen Übersetzungen arbeiten, lohnt es sich, eine eigene Ersetzungs-JSON zu erstellen.
- **HTML-Export**: Die heruntergeladenen HTML-Dateien können in Webseiten eingebettet oder in Textverarbeitungsprogrammen geöffnet werden.

## Häufig gestellte Fragen

**F: Kann ich die Anwendung offline nutzen?**  
A: Die Anwendung läuft online über Streamlit Cloud. Für eine offline Version müssten Sie den Quellcode herunterladen und lokal mit Python und Streamlit ausführen.

**F: Welche Esperanto-Wörter werden erkannt und ersetzt?**  
A: Die Standarddatei enthält etwa 4736 Esperanto-Wortstämme mit Übersetzungen. Die Anwendung erkennt auch verschiedene Wortformen (z.B. Verbkonjugationen, Pluralformen) basierend auf den Regeln der Esperanto-Grammatik.

**F: Wie kann ich eigene Übersetzungen hinzufügen?**  
A: Erstellen Sie eine eigene CSV-Datei mit den Spalten für Esperanto-Wortstämme und deren Übersetzungen, und generieren Sie damit eine neue Ersetzungs-JSON auf der zweiten Seite der Anwendung.

**F: Was bedeuten die verschiedenen Größenklassen bei den Ruby-Anmerkungen?**  
A: Die Klassen (XXS_S, XS_S, S_S, M_M, L_L, XL_L, XXL_L) steuern die Größe der Anmerkungen basierend auf dem Verhältnis zwischen der Länge des Originaltextes und der Übersetzung.

**F: Wie funktioniert die Zerlegung von Esperanto-Wörtern?**  
A: Die Anwendung verwendet Regeln, um Esperanto-Wörter in ihre Bestandteile zu zerlegen: Wortstämme, Präfixe, Suffixe und grammatikalische Endungen. Diese Regeln sind in der JSON zur Zerlegung von Esperanto-Wortstämmen definiert.

---

Wir hoffen, dass dieses Benutzerhandbuch Ihnen hilft, die volle Funktionalität des Esperanto-Kanji-Konverters und Ruby-Annotationstools zu nutzen. Bei weiteren Fragen können Sie die Projektseite auf GitHub besuchen oder sich an den Entwickler wenden.

Viel Freude beim Experimentieren mit Esperanto und verschiedenen Darstellungsformen!