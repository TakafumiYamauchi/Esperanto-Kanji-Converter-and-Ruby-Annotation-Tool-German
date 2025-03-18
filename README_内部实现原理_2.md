# Technische Dokumentation: Esperanto-Kanji-Konverter und Ruby-Anmerkungstool

## Inhaltsverzeichnis

1. Architekturüberblick
2. Kernkomponenten und Datenfluss
3. Detaillierte Analyse der Codemodule
   - main.py
   - Seite zur JSON-Generierung
   - esp_text_replacement_module.py
   - esp_replacement_json_make_module.py
4. Zentrale Algorithmen und Datenstrukturen
5. Parallelverarbeitung
6. Anpassungs- und Erweiterungsmöglichkeiten

## 1. Architekturüberblick

Die Anwendung ist als Streamlit-Webanwendung konzipiert und besteht aus vier Hauptkomponenten:

1. **Hauptanwendung (main.py)**: Streamlit-UI für die Ersetzung von Esperanto-Texten
2. **JSON-Generierungsseite**: Hilfstool zum Erstellen eigener Ersetzungsregeln in JSON-Format
3. **Ersetzungsmodul (esp_text_replacement_module.py)**: Kernfunktionen zur Textverarbeitung und -ersetzung
4. **JSON-Erstellungsmodul (esp_replacement_json_make_module.py)**: Funktionen zur Erstellung der Ersetzungs-JSON-Dateien

Die Anwendung folgt einem modularen Design, wobei die Hauptfunktionalität in eigenständige Module ausgelagert ist, die von den Streamlit-Seiten importiert werden. Dies ermöglicht eine saubere Trennung zwischen der Benutzeroberfläche und den zugrunde liegenden Ersetzungsalgorithmen.

Das Gesamtsystem basiert auf einem mehrstufigen Ersetzungsprozess, der Esperanto-Text einliest, spezielle Muster identifiziert, Esperanto-Wörter in ihre Bestandteile (Stämme, Präfixe, Suffixe) zerlegt und diese durch entsprechende Übersetzungen oder Kanji ersetzt.

## 2. Kernkomponenten und Datenfluss

Der grundlegende Datenfluss der Anwendung lässt sich wie folgt beschreiben:

1. **Eingabe**: Esperanto-Text (manuell eingegeben oder aus Datei geladen)
2. **Konfiguration**: Auswahl des Ausgabeformats und anderer Parameter
3. **Vorverarbeitung**: Normalisierung des Textes (Umwandlung von Esperanto-Sonderzeichen)
4. **Tokenisierung und Schutz**: Erkennung von geschützten Bereichen (mit % oder @ markiert)
5. **Ersetzungsprozess**: Systematisches Ersetzen von Esperanto-Wörtern durch Übersetzungen/Kanji
6. **Formatierung**: Anwendung des ausgewählten Ausgabeformats (HTML, Ruby, Klammern)
7. **Ausgabe**: Anzeige und Download-Option für das Ergebnis

Die Kernkomponenten sind:

- **Ersetzungsregeln**: JSON-Datei mit drei Hauptlisten:
  - `replacements_final_list`: Hauptersetzungsliste für globale Ersetzungen
  - `replacements_list_for_2char`: Spezielle Liste für 2-Zeichen-Wortstämme
  - `replacements_list_for_localized_string`: Liste für lokale Ersetzungen (@-markiert)

- **Platzhalter-System**: Temporäre Platzhalter zum Schutz vor versehentlichen Ersetzungen
  - Für globale Ersetzungen: `$xxxxx$`
  - Für lokale Ersetzungen: `@xxxxx@`
  - Für unveränderliche Teile: `%xxxxx%`

- **Parallelverarbeitung**: Optional für große Texte, spaltet die Verarbeitung in mehrere Prozesse auf

## 3. Detaillierte Analyse der Codemodule

### 3.1 main.py

Die Hauptdatei ist für die Benutzerschnittstelle und den primären Arbeitsablauf verantwortlich. Hier sind die wichtigsten Abschnitte:

```python
# Funktionen, die aus den Modulen importiert werden
from esp_text_replacement_module import (
    x_to_circumflex, x_to_hat, hat_to_circumflex, circumflex_to_hat,
    replace_esperanto_chars, import_placeholders,
    orchestrate_comprehensive_esperanto_text_replacement,
    parallel_process, apply_ruby_html_header_and_footer
)

# Cache-Funktion zum effizienten Laden der JSON-Dateien
@st.cache_data
def load_replacements_lists(json_path: str) -> Tuple[List, List, List]:
    # Lädt die drei Ersetzungslisten aus einer JSON-Datei
    ...
```

#### Hauptablauf:

1. **Konfiguration der Streamlit-Seite**:
   - Seitentitel, Layout-Einstellungen
   - UI-Elemente für die Benutzereingabe

2. **Laden der Ersetzungsregeln**:
   - Entweder Standard-JSON oder benutzerdefinierte JSON
   - Extraktion der drei Listen: `replacements_final_list`, `replacements_list_for_localized_string`, `replacements_list_for_2char`

3. **Laden der Platzhalter**:
   - Zur Verwendung im Ersetzungsprozess

4. **Erweiterte Einstellungen**:
   - Optionen für Parallelverarbeitung

5. **Ausgabeformat-Auswahl**:
   - HTML mit Ruby-Anmerkungen, Klammerformat, etc.

6. **Texteingabe**:
   - Via manueller Eingabe oder Datei-Upload

7. **Textverarbeitung**:
   - Aufruf der Hauptersetzungsfunktion oder der parallelen Version
   - Konvertierung der Esperanto-Sonderzeichen
   - Anwendung von HTML-Headern und -Footern

8. **Ergebnisdarstellung**:
   - Anzeige des Ergebnisses in Tabs (HTML-Vorschau, HTML-Code, Text)
   - Download-Button für das Ergebnis

Die wichtigste Funktion ist der Aufruf von `orchestrate_comprehensive_esperanto_text_replacement` (oder `parallel_process` bei aktivierter Parallelverarbeitung), die den eigentlichen Ersetzungsprozess durchführt.

### 3.2 Seite zur JSON-Generierung

Diese Seite ermöglicht es Benutzern, eigene Ersetzungsregeln zu erstellen. Sie ist als separate Streamlit-Seite im "pages"-Verzeichnis implementiert.

```python
# Import der benötigten Module
from esp_text_replacement_module import (
    convert_to_circumflex, safe_replace, import_placeholders, apply_ruby_html_header_and_footer
)
from esp_replacement_json_make_module import (
    convert_to_circumflex, output_format, import_placeholders, capitalize_ruby_and_rt,
    process_chunk_for_pre_replacements, parallel_build_pre_replacements_dict,
    remove_redundant_ruby_if_identical
)
```

#### Wichtige Datenstrukturen:

- **Spezielle Listen für Verb-Suffixe und andere Wortbestandteile**:
  ```python
  verb_suffix_2l = {
      'as':'as', 'is':'is', 'os':'os', 'us':'us','at':'at','it':'it','ot':'ot',
      'ad':'ad','iĝ':'iĝ','ig':'ig','ant':'ant','int':'int','ont':'ont'
  }
  ```

- **Listen für die Suffixe `-an` und `-on`**:
  ```python
  AN=[['dietan', '/diet/an/', '/diet/an'], ['afrikan', '/afrik/an/', '/afrik/an'], ...]
  ON=[['duon', '/du/on/', '/du/on'], ['okon', '/ok/on/', '/ok/on'], ...]
  ```

- **Listen für 2-Zeichen-Wortstämme**:
  ```python
  suffix_2char_roots=['ad', 'ag', 'am', 'ar', 'as', ...]
  prefix_2char_roots=['al', 'am', 'av', 'bo', 'di', ...]
  standalone_2char_roots=['al', 'ci', 'da', 'de', 'di', ...]
  ```

#### Hauptablauf zur JSON-Generierung:

1. **Laden von CSV-Daten**:
   - Esperanto-Wortstämme und Übersetzungen/Kanji

2. **Laden oder Hochladen von JSON-Dateien**:
   - Regeln zur Stammzerlegung
   - Benutzerdefinierte Ersetzungen

3. **Erstellung eines temporären Wörterbuchs**:
   - Abbildung von Esperanto-Stämmen auf Ersetzungen
   - Verarbeitung mit `output_format` für verschiedene Ausgabeformate

4. **Komplexe Verarbeitung**:
   - Prioritätsbasierte Sortierung (längere Wörter zuerst)
   - Spezialbehandlung für Verben, Suffixe, Präfixe etc.
   - Rekursive Anwendung von Ersetzungsregeln

5. **Generierung der drei finalen Listen**:
   - `replacements_final_list`: Hauptersetzungsliste
   - `replacements_list_for_2char`: Ersetzungen für 2-Zeichen-Stämme
   - `replacements_list_for_localized_string`: Lokale Ersetzungen

6. **Ausgabe als JSON**:
   - Zusammenführung der drei Listen in eine JSON-Datei
   - Download-Button für das Ergebnis

Der komplexeste Teil dieses Moduls ist die Erstellung der Prioritäten für die Ersetzungen, um sicherzustellen, dass längere Wörter vor kürzeren ersetzt werden und dass spezielle Wortstrukturen (wie Verben mit verschiedenen Endungen) korrekt behandelt werden.

### 3.3 esp_text_replacement_module.py

Dieses Modul enthält die Kernfunktionen für die Textverarbeitung und Ersetzung.

```python
# Konversionswörterbücher für Esperanto-Sonderzeichen
x_to_circumflex = {'cx': 'ĉ', 'gx': 'ĝ', 'hx': 'ĥ', 'jx': 'ĵ', 'sx': 'ŝ', 'ux': 'ŭ', ...}
circumflex_to_x = {'ĉ': 'cx', 'ĝ': 'gx', 'ĥ': 'hx', 'ĵ': 'jx', 'ŝ': 'sx', 'ŭ': 'ux', ...}
# ... weitere Konversionswörterbücher
```

#### Wichtige Funktionen:

1. **replace_esperanto_chars**:
   ```python
   def replace_esperanto_chars(text, char_dict: Dict[str, str]) -> str:
       # Ersetzt Esperanto-Zeichen basierend auf einem Wörterbuch
       for original_char, converted_char in char_dict.items():
           text = text.replace(original_char, converted_char)
       return text
   ```

2. **safe_replace**:
   ```python
   def safe_replace(text: str, replacements: List[Tuple[str, str, str]]) -> str:
       # Führt eine sichere zweistufige Ersetzung durch (Original -> Platzhalter -> Ziel)
       valid_replacements = {}
       # Zuerst old -> placeholder
       for old, new, placeholder in replacements:
           if old in text:
               text = text.replace(old, placeholder)
               valid_replacements[placeholder] = new
       # Dann placeholder -> new
       for placeholder, new in valid_replacements.items():
           text = text.replace(placeholder, new)
       return text
   ```

3. **orchestrate_comprehensive_esperanto_text_replacement**:
   ```python
   def orchestrate_comprehensive_esperanto_text_replacement(
       text, 
       placeholders_for_skipping_replacements: List[str],
       replacements_list_for_localized_string: List[Tuple[str, str, str]],
       placeholders_for_localized_replacement: List[str],
       replacements_final_list: List[Tuple[str, str, str]],
       replacements_list_for_2char: List[Tuple[str, str, str]],
       format_type: str
   ) -> str:
       # Hauptfunktion für den gesamten Ersetzungsprozess
       # Führt mehrere Schritte der Textverarbeitung durch
       # ...
   ```

#### Ersetzungsprozess in Schritten:

1. **Normalisierung des Eingabetextes**:
   - Vereinheitlichung von Leerzeichen
   - Konvertierung der Esperanto-Sonderzeichen

2. **Verarbeitung von Schutzmustern**:
   - `%...%` für unveränderte Textteile
   - `@...@` für lokale Ersetzungen

3. **Globale Ersetzungen**:
   - Anwendung der Hauptersetzungsliste

4. **Spezielle 2-Zeichen-Ersetzungen**:
   - Zweimal angewendet, um verschachtelte Strukturen zu behandeln

5. **Wiederherstellung der geschützten Bereiche**:
   - Umkehrung der Platzhalterersetzungen

6. **HTML-Formatierung** (falls erforderlich):
   - Konvertierung von Zeilenumbrüchen zu `<br>`
   - Ersetzung von Leerzeichen durch `&nbsp;` wo nötig

Das Modul enthält auch Funktionen für die parallele Verarbeitung, was bei großen Texten zu erheblichen Leistungsverbesserungen führen kann.

### 3.4 esp_replacement_json_make_module.py

Dieses Modul enthält Hilfsfunktionen für die Generierung der JSON-Ersetzungsregeln, insbesondere für die formatierte Ausgabe und die parallele Verarbeitung der Daten.

```python
# Ähnliche Konversionswörterbücher wie im Ersetzungsmodul
x_to_circumflex = {'cx': 'ĉ', 'gx': 'ĝ', ...}
# ...

# Funktionen für die Textformatierung
def output_format(main_text, ruby_content, format_type, char_widths_dict):
    # Erzeugt formatierte Ausgabe basierend auf dem gewählten Format
    if format_type == 'HTML格式_Ruby文字_大小调整':
        # Komplexe Logik zur Anpassung der Ruby-Größe basierend auf Textbreiten
        # ...
    elif format_type == 'HTML格式':
        return f'<ruby>{main_text}<rt>{ruby_content}</rt></ruby>'
    # ... weitere Formate
```

#### Wichtige Funktionen:

1. **Textbreitenmessung und -formatierung**:
   ```python
   def measure_text_width_Arial16(text, char_widths_dict: Dict[str, int]) -> int:
       # Berechnet die Gesamtbreite eines Textes basierend auf Zeichenbreiten
       # ...
   
   def insert_br_at_half_width(text, char_widths_dict: Dict[str, int]) -> str:
       # Fügt einen <br>-Tag in der Mitte des Textes ein (basierend auf Textbreite)
       # ...
   ```

2. **Ruby-Formatierung**:
   ```python
   def capitalize_ruby_and_rt(text: str) -> str:
       # Großschreibung nur für den ersten Buchstaben in Ruby-Tags
       # Verwendet reguläre Ausdrücke zur Identifizierung von Ruby-Elementen
       # ...
   
   def remove_redundant_ruby_if_identical(text: str) -> str:
       # Entfernt Ruby-Tags, wenn der Ruby-Text mit dem Haupttext identisch ist
       # ...
   ```

3. **Parallele Verarbeitung für die JSON-Erstellung**:
   ```python
   def parallel_build_pre_replacements_dict(
       E_stem_with_Part_Of_Speech_list: List[List[str]],
       replacements: List[Tuple[str, str, str]],
       num_processes: int = 4
   ) -> Dict[str, List[str]]:
       # Verarbeitet die Daten parallel zur schnelleren JSON-Erstellung
       # ...
   ```

Dieses Modul ist entscheidend für die Effizienz der JSON-Generierung, insbesondere bei großen Datensätzen. Es verwendet eine Kombination aus mehreren Techniken:

- **Reguläre Ausdrücke** für Mustererkennung in Texten
- **Zeichenbreiten-Berechnung** für die optimale Formatierung von Ruby-Anmerkungen
- **Parallelverarbeitung** für die effiziente Verarbeitung großer Datensätze
- **Zweistufige Ersetzung** (Original → Platzhalter → Ziel) zur Vermeidung von Konflikten

## 4. Zentrale Algorithmen und Datenstrukturen

### 4.1 Die dreistufige Ersetzungsstrategie

Das Herzstück der Anwendung ist die **dreistufige Ersetzungsstrategie**:

1. **Erkennung und Schutz spezieller Muster**:
   - Identifizierung von %...%-Mustern (zu schützende Texte)
   - Identifizierung von @...@-Mustern (lokale Ersetzungen)
   - Temporäre Ersetzung durch eindeutige Platzhalter

2. **Hauptersetzungsprozess**:
   - Globale Ersetzungen (längere Texte zuerst)
   - Spezielle 2-Zeichen-Ersetzungen (für Präfixe, Suffixe, eigenständige Wortstämme)
   - Verwendung von Platzhaltern zur Vermeidung von Konflikten

3. **Wiederherstellung und Formatierung**:
   - Rücktransformation der Platzhalter
   - Anwendung des ausgewählten Ausgabeformats

Diese Strategie ist notwendig, um Konflikte bei verschachtelten Ersetzungen zu vermeiden. Ohne sie könnten bereits ersetzte Texte bei späteren Ersetzungen beschädigt werden.

### 4.2 Datenstrukturen für Ersetzungsregeln

Die Ersetzungsregeln sind in drei Hauptlisten organisiert:

1. **replacements_final_list**:
   - Format: `(original, ersetzung, platzhalter)`
   - Beispiel: `("amik", "<ruby>amik<rt>Freund</rt></ruby>", "$12345$")`
   - Verwendet für die meisten Ersetzungen

2. **replacements_list_for_2char**:
   - Format: `(original, ersetzung, platzhalter)`
   - Speziell für 2-Zeichen-Wortstämme, Präfixe, Suffixe
   - Wird in einem separaten Durchlauf angewendet

3. **replacements_list_for_localized_string**:
   - Format: `(original, ersetzung, platzhalter)`
   - Für lokale Ersetzungen innerhalb von @...@-Markierungen

Jede dieser Listen wird nach der Länge der Originaltexte sortiert, um sicherzustellen, dass längere Texte vor kürzeren ersetzt werden. Dies verhindert unerwünschte Teilersetzungen.

### 4.3 Platzhaltersystem

Ein kritischer Aspekt des Ersetzungsprozesses ist das Platzhaltersystem:

```python
# Laden von Platzhaltern aus Dateien
placeholders_for_skipping_replacements = import_placeholders(
    './Appの运行に使用する各类文件/占位符(placeholders)_%1854%-%4934%_文字列替换skip用.txt'
)
placeholders_for_localized_replacement = import_placeholders(
    './Appの运行に使用する各类文件/占位符(placeholders)_@5134@-@9728@_局部文字列替换结果捕捉用.txt'
)
```

Diese Platzhalter sind eindeutige Zeichenketten, die temporär im Text verwendet werden, um bereits verarbeitete Teile zu schützen. Sie spielen eine entscheidende Rolle bei der Vermeidung von Konflikten während des mehrstufigen Ersetzungsprozesses.

### 4.4 Prioritätsbasierte Ersetzung

Ein weiterer wichtiger Algorithmus ist die prioritätsbasierte Ersetzung:

```python
# Sortieren der Ersetzungen nach Priorität (längere zuerst)
pre_replacements_list_2 = sorted(pre_replacements_list_1, key=lambda x: x[2], reverse=True)
```

Die Priorität wird hauptsächlich auf Basis der Wortlänge berechnet, aber es gibt wichtige Anpassungen:

- Verben und ihre Konjugationen erhalten höhere Prioritäten
- Spezielle Wortklassen (wie Wörter mit den Suffixen -an oder -on) werden gesondert behandelt
- Benutzerdefinierte Prioritäten aus den JSON-Konfigurationsdateien werden berücksichtigt

## 5. Parallelverarbeitung

Für die Verarbeitung großer Texte implementiert die Anwendung eine parallele Verarbeitungsstrategie:

```python
def parallel_process(
    text: str,
    num_processes: int,
    # ... weitere Parameter
) -> str:
    # Teilt den Text in Segmente auf
    lines = re.findall(r'.*?\n|.+$', text)
    # Verteilt die Segmente auf mehrere Prozesse
    # ...
    with multiprocessing.Pool(processes=num_processes) as pool:
        results = pool.starmap(
            process_segment,
            [
                (
                    lines[start:end],
                    # ... weitere Parameter
                )
                for (start, end) in ranges
            ]
        )
    # Verbindet die Ergebnisse wieder
    return ''.join(results)
```

Der Parallelisierungsansatz funktioniert wie folgt:

1. **Aufteilung des Textes** in Zeilen
2. **Gruppierung der Zeilen** in Segmente (basierend auf der Anzahl der Prozesse)
3. **Parallele Verarbeitung** jedes Segments mit dem `multiprocessing`-Modul
4. **Zusammenführung der Ergebnisse** in der richtigen Reihenfolge

Diese Strategie kann die Verarbeitungszeit erheblich reduzieren, insbesondere bei großen Texten.

## 6. Anpassungs- und Erweiterungsmöglichkeiten

### 6.1 Hinzufügen neuer Ausgabeformate

Um ein neues Ausgabeformat hinzuzufügen, müssen Sie die `output_format`-Funktion in `esp_replacement_json_make_module.py` erweitern:

```python
def output_format(main_text, ruby_content, format_type, char_widths_dict):
    # Bestehende Formate...
    
    # Neues Format hinzufügen
    elif format_type == 'Mein_Neues_Format':
        return f'<span data-original="{main_text}">{ruby_content}</span>'
```

Aktualisieren Sie auch die Optionen in beiden Streamlit-Schnittstellen.

### 6.2 Optimierung der Ersetzungsalgorithmen

Die aktuelle Implementierung verwendet eine einfache string.replace()-Methode für Ersetzungen, was bei sehr großen Texten ineffizient sein kann. Mögliche Optimierungen:

1. **Verwendung von Tries (Präfixbäumen)** für effizientere Wortsuche
2. **Tokenisierung** des Textes vor der Ersetzung
3. **Caching häufiger Ersetzungen** für wiederholte Wörter

### 6.3 Erweiterung der Sprach- und Schriftsystemunterstützung

Die Anwendung unterstützt derzeit hauptsächlich Esperanto → Deutsch/Kanji-Ersetzungen. Um die Unterstützung zu erweitern:

1. **CSV-Import für neue Sprachpaare** hinzufügen
2. **Sprachspezifische Verarbeitungsregeln** implementieren
3. **Spezielle Formatierungen** für andere Schriftsysteme hinzufügen

### 6.4 Verbesserung der Benutzeroberfläche

Mögliche UI-Verbesserungen:

1. **Echtzeit-Vorschau** während der Eingabe
2. **Interaktive Bearbeitung** der Ersetzungsregeln
3. **Erweiterte Statistiken** über die durchgeführten Ersetzungen

---

Diese Dokumentation bietet einen umfassenden Überblick über die technische Implementierung des Esperanto-Kanji-Konverters und Ruby-Anmerkungstools. Es deckt die Architektur, Datenflüsse, Algorithmen und Erweiterungsmöglichkeiten der Anwendung ab, was Ihnen als Programmierer mittlerer Qualifikation ein tiefes Verständnis der internen Funktionsweise ermöglichen sollte.