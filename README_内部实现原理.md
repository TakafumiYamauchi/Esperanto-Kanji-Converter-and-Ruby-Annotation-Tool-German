# Technische Dokumentation: Esperanto-Kanji-Konverter und Ruby-Annotationstool

## 1. Architekturübersicht

Diese Dokumentation richtet sich an Programmierer, die die technische Implementierung und Funktionsweise der Esperanto-Kanji-Konverter-Anwendung verstehen möchten. Die Anwendung ist eine Streamlit-basierte Webanwendung, die Esperanto-Texte mithilfe verschiedener Ersetzungsregeln transformiert.

### 1.1 Hauptkomponenten

Die Anwendung besteht aus vier Hauptkomponenten:

1. **main.py**: Hauptanwendungsdatei, die die primäre Streamlit-Benutzeroberfläche implementiert
2. **Seite zur Erstellung einer JSON-Datei...**: Eine separate Streamlit-Seite (im Pages-Verzeichnis), die zur Generierung von Ersetzungsregeln dient
3. **esp_text_replacement_module.py**: Kernmodul für die Textverarbeitung und Ersetzungsfunktionen
4. **esp_replacement_json_make_module.py**: Modul für die Erstellung und Manipulation von JSON-Ersetzungsregeln

### 1.2 Datenfluss

Der grundlegende Datenfluss der Anwendung ist wie folgt:

```
[JSON-Ersetzungsregeln] + [Esperanto-Text] -> [Verarbeitungspipeline] -> [Transformierter Text]
```

Die JSON-Ersetzungsregeln werden entweder aus einer Standarddatei geladen oder vom Benutzer hochgeladen. Der Esperanto-Text wird eingegeben oder aus einer Datei geladen. Die Verarbeitungspipeline wendet die Regeln auf den Text an und erzeugt den transformierten Text mit den gewünschten Ersetzungen und Formatierungen.

## 2. Modulstruktur und Schlüsselfunktionen

### 2.1 main.py

Diese Datei implementiert die Hauptbenutzeroberfläche und den Kontrollfluss der Anwendung.

#### Wichtige Funktionen:

- **load_replacements_lists()**: Lädt Ersetzungsregeln aus einer JSON-Datei mit Caching (@st.cache_data)
- **orchestrate_comprehensive_esperanto_text_replacement()**: Ruft die Hauptersetzungsfunktion aus dem esp_text_replacement_module auf
- **parallel_process()**: Implementiert die parallelisierte Verarbeitung von Texten

#### Datenstrukturen:

Die Hauptdatenstrukturen in main.py sind:

```python
# Die drei wichtigsten Ersetzungslisten, die aus der JSON-Datei geladen werden
replacements_final_list: List[Tuple[str, str, str]]  # Format: (original, ersatz, platzhalter)
replacements_list_for_localized_string: List[Tuple[str, str, str]]
replacements_list_for_2char: List[Tuple[str, str, str]]

# Platzhalter-Listen für verschiedene Ersetzungstypen
placeholders_for_skipping_replacements: List[str]  # Für %...% umschlossene Bereiche
placeholders_for_localized_replacement: List[str]  # Für @...@ umschlossene Bereiche
```

### 2.2 esp_text_replacement_module.py

Dieses Modul enthält die Kernfunktionalität für die Textverarbeitung und Ersetzung.

#### Schlüsselfunktionen:

- **convert_to_circumflex()**: Konvertiert Esperanto-Text mit verschiedenen Darstellungen (z.B. "cx", "c^") in die Standardform mit Zirkumflex (ĉ)
- **safe_replace()**: Implementiert eine zweistufige Ersetzung mit Platzhaltern, um Interferenzen zu vermeiden
- **orchestrate_comprehensive_esperanto_text_replacement()**: Die Hauptfunktion, die den gesamten Ersetzungsprozess orchestriert
- **parallel_process()**: Teilt den Text in Segmente und verarbeitet diese parallel

#### Datenstrukturen:

```python
# Wörterbücher für die Konvertierung von Esperanto-Zeichen
x_to_circumflex = {'cx': 'ĉ', 'gx': 'ĝ', ...}
circumflex_to_x = {'ĉ': 'cx', 'ĝ': 'gx', ...}
hat_to_circumflex = {'c^': 'ĉ', 'g^': 'ĝ', ...}
# ... und weitere ähnliche Mappings
```

### 2.3 esp_replacement_json_make_module.py

Dieses Modul unterstützt die Generierung von JSON-Ersetzungsregeln und enthält Hilfsfunktionen für die Textformatierung.

#### Hauptfunktionen:

- **output_format()**: Erzeugt formatierte Ausgabestrings basierend auf dem gewählten Format (HTML, Klammern, etc.)
- **capitalize_ruby_and_rt()**: Großschreibung für HTML-Ruby-Tags
- **process_chunk_for_pre_replacements()**: Verarbeitet Datenblöcke für die Parallelverarbeitung
- **parallel_build_pre_replacements_dict()**: Baut ein Ersetzungswörterbuch parallel auf

#### Datenstrukturen:

Diese ähneln denen im esp_text_replacement_module, enthalten aber zusätzliche Funktionen für die Textformatierung.

### 2.4 Seite zur Erstellung einer JSON-Datei...

Diese Streamlit-Seite implementiert eine komplexe Logik zur Generierung von JSON-Ersetzungsregeln aus verschiedenen Quellen.

#### Wichtige Funktionen:

- Laden und Verarbeiten von CSV-Dateien mit Esperanto-Wortstämmen und deren Übersetzungen
- Generierung von Ersetzungsregeln basierend auf Wortstammzerlegung
- Zusammenführung von Regel-Listen zu einer JSON-Datei

## 3. Kernalgorithmen und -konzepte

### 3.1 Platzhalterbasierte Ersetzung

Ein zentrales Konzept der Anwendung ist die Verwendung von Platzhaltern, um eine korrekte Ersetzungsreihenfolge sicherzustellen und Konflikte zu vermeiden.

```python
def safe_replace(text: str, replacements: List[Tuple[str, str, str]]) -> str:
    """
    (old, new, placeholder) Tripel werden verwendet:
    1. Ersetze 'old' durch 'placeholder'
    2. Ersetze 'placeholder' durch 'new'
    """
    valid_replacements = {}
    for old, new, placeholder in replacements:
        if old in text:
            text = text.replace(old, placeholder)
            valid_replacements[placeholder] = new
    for placeholder, new in valid_replacements.items():
        text = text.replace(placeholder, new)
    return text
```

Dieser zweistufige Prozess ist entscheidend, um Probleme zu vermeiden, die bei einer direkten Ersetzung auftreten können:

1. Wenn ein String sowohl als Ganzes als auch als Teil eines anderen Strings ersetzt werden soll
2. Wenn die Ersetzung eines Strings zu einem neuen String führt, der selbst ein Ersetzungsziel ist

### 3.2 Text-Verarbeitungspipeline

Die Hauptverarbeitungsfunktion `orchestrate_comprehensive_esperanto_text_replacement()` führt mehrere Schritte aus:

1. Normalisierung von Leerzeichen und Esperanto-Zeichen
2. Identifizierung und vorübergehende Ersetzung von durch % umschlossenen Texten
3. Identifizierung und lokale Ersetzung von durch @ umschlossenen Texten
4. Globale Ersetzungen anhand der Hauptersetzungsliste
5. Spezielle Ersetzungen für 2-Buchstaben-Wortstämme
6. Wiederherstellung der Platzhalter für % und @ umschlossene Texte
7. HTML-spezifische Formatierung (wenn erforderlich)

### 3.3 Parallelverarbeitung

Für große Texte implementiert die Anwendung eine Parallelverarbeitung mit Python's `multiprocessing`:

```python
def parallel_process(text: str, num_processes: int, ...) -> str:
    # Text in Zeilen aufteilen
    lines = re.findall(r'.*?\n|.+$', text)
    # Zeilen in Blöcke für jeden Prozess aufteilen
    chunks = [lines[i:i+chunk_size] for i in range(0, len(lines), chunk_size)]
    # Parallel verarbeiten
    with multiprocessing.Pool(processes=num_processes) as pool:
        results = pool.starmap(process_segment, [(chunk, ...) for chunk in chunks])
    # Ergebnisse zusammenführen
    return ''.join(results)
```

### 3.4 Prioritätsbasierte Ersetzungsordnung

Die Anwendung implementiert eine komplexe Logik zur Bestimmung der Ersetzungsreihenfolge, wobei längere Strings vor kürzeren ersetzt werden, um unbeabsichtigte Teilersetzungen zu vermeiden:

```python
# Ersetzungslisten nach Länge der zu ersetzenden Strings sortieren
pre_replacements_list_2 = sorted(pre_replacements_list_1, 
                                key=lambda x: x[2], reverse=True)
```

Zusätzlich werden spezielle Regeln für Verbformen, Endungen und andere grammatikalische Konstrukte implementiert, um eine korrekte linguistische Verarbeitung zu gewährleisten.

## 4. Datenstrukturen im Detail

### 4.1 Ersetzungslisten

Die Hauptersetzungsregeln werden in drei Listen organisiert:

1. **replacements_final_list**: Hauptliste für globale Ersetzungen
2. **replacements_list_for_localized_string**: Liste für lokale Ersetzungen (@...@)
3. **replacements_list_for_2char**: Liste für 2-Buchstaben-Wortstämme

Jeder Eintrag in diesen Listen ist ein Tupel mit drei Elementen:
```
(original_text, replacement_text, placeholder)
```

### 4.2 Grammatikalische Regelkonstrukte

Die Anwendung enthält spezielle Datenstrukturen für die grammatikalische Verarbeitung:

```python
# Verbliste für Verbendungen
verb_suffix_2l = {
    'as':'as', 'is':'is', 'os':'os', 'us':'us','at':'at','it':'it','ot':'ot',
    'ad':'ad','iĝ':'iĝ','ig':'ig','ant':'ant','int':'int','ont':'ont'
}

# Listen für spezielle Suffixe und Präfixe
suffix_2char_roots=['ad', 'ag', 'am', ...]
prefix_2char_roots=['al', 'am', 'av', ...]
standalone_2char_roots=['al', 'ci', 'da', ...]
```

Diese Strukturen ermöglichen eine morphologische Analyse und Ersetzung, die die grammatikalischen Regeln des Esperanto berücksichtigt.

## 5. Die JSON-Generierung in Detail

Der Prozess zur Generierung der JSON-Ersetzungsregeln ist besonders komplex:

1. Einlesen von Esperanto-Wortstämmen aus CSV
2. Anwendung von benutzerdefinierten Stammzerlegungsregeln
3. Generierung von zusätzlichen Formen basierend auf Grammatikregeln
4. Sortierung nach Priorität
5. Zusammenstellung zu einer einheitlichen JSON-Struktur

Die resultierende JSON-Datei enthält die drei Hauptersetzungslisten, die von der Hauptanwendung verwendet werden.

Wichtige Teile des Prozesses sind:

```python
# Schritt 1-3: CSV-Daten in temporäre Ersetzungsliste konvertieren
for *, (E*root, hanzi_or_meaning) in CSV_data_imported.iterrows():
    if pd.notna(E_root) and pd.notna(hanzi_or_meaning) \
       and '#' not in E_root and (E_root != '') and (hanzi_or_meaning != ''):
        temporary_replacements_dict[E_root] = [
            output_format(E_root, hanzi_or_meaning, format_type, char_widths_dict),
            len(E_root)
        ]

# Schritt 4: Nach Länge sortieren
temporary_replacements_list_2 = sorted(temporary_replacements_list_1, 
                                     key=lambda x: x[2], reverse=True)

# Schritt 5: JSON-Struktur zusammenstellen
combined_data = {}
combined_data["全域替换用のリスト(列表)型配列(replacements_final_list)"] = replacements_final_list
combined_data["二文字词根替换用のリスト(列表)型配列(replacements_list_for_2char)"] = replacements_list_for_2char
combined_data["局部文字替换用のリスト(列表)型配列(replacements_list_for_localized_string)"] = replacements_list_for_localized_string
```

## 6. Esperanto-spezifische Verarbeitung

Die Anwendung berücksichtigt besondere Aspekte der Esperanto-Sprache:

### 6.1 Zeichenkonvertierung

Esperanto verwendet spezielle Buchstaben mit diakritischen Zeichen (ĉ, ĝ, ĥ, ĵ, ŝ, ŭ), die in verschiedenen Formaten dargestellt werden können. Die Anwendung unterstützt drei Hauptformate:

1. Unicode-Zeichen mit Zirkumflex: ĉ, ĝ, ĥ, ĵ, ŝ, ŭ
2. X-System: cx, gx, hx, jx, sx, ux
3. Dach-System: c^, g^, h^, j^, s^, u^

### 6.2 Morphologische Verarbeitung

Esperanto ist eine agglutinierende Sprache mit regelmäßiger Wortbildung. Die Anwendung nutzt diese Eigenschaft, indem sie:

1. Wortstämme identifiziert
2. Präfixe und Suffixe erkennt
3. Grammatikalische Endungen verarbeitet (z.B. -o für Substantive, -a für Adjektive)
4. Verbzeiten und -modi verarbeitet (-as, -is, -os, -us, etc.)

## 7. Ruby-Annotation und Formatierung

Für die HTML-Ausgabeformate unterstützt die Anwendung Ruby-Annotationen, eine Technik zur Darstellung von Lesehilfen über Text (ähnlich wie Furigana im Japanischen).

```html
<ruby>amiko<rt>Freund</rt></ruby>
```

Die Anwendung implementiert verschiedene Formatierungsoptionen:

1. Einfache Ruby-Annotationen
2. Ruby mit angepasster Größe basierend auf der Textlänge
3. Kanji-Ersetzung mit Ruby-Ursprungstext
4. Einfache Klammernotation

Das Modul `esp_replacement_json_make_module.py` enthält die Funktion `output_format()`, die diese verschiedenen Formate generiert.

## 8. Platzhalter-Management

Die Anwendung verwendet vorbereitete Listen von eindeutigen Platzhaltern, die aus externen Textdateien geladen werden:

```python
imported_placeholders_for_global_replacement = import_placeholders(
    './Appの运行に使用する各类文件/占位符(placeholders)_$20987$-$499999$_全域替换用.txt'
)
```

Diese Platzhalter sind sorgfältig gewählt, um Konflikte mit regulärem Text zu vermeiden. Sie werden in einem zweistufigen Prozess verwendet:

1. Ersetze den Originaltext durch einen eindeutigen Platzhalter
2. Ersetze den Platzhalter durch den gewünschten Ersatztext

## 9. Leistungsoptimierungen

Die Anwendung implementiert mehrere Optimierungen für bessere Leistung:

### 9.1 Caching

Streamlit's `@st.cache_data` wird verwendet, um das wiederholte Laden von JSON-Dateien zu vermeiden:

```python
@st.cache_data
def load_replacements_lists(json_path: str) -> Tuple[List, List, List]:
    # Lädt JSON-Daten und gibt sie als Tupel zurück
```

### 9.2 Parallelverarbeitung

Für große Texte wird Python's multiprocessing verwendet:

```python
if use_parallel:
    processed_text = parallel_process(
        text=text0,
        num_processes=num_processes,
        # weitere Parameter...
    )
else:
    processed_text = orchestrate_comprehensive_esperanto_text_replacement(
        text=text0,
        # weitere Parameter...
    )
```

### 9.3 Optimierte Ersetzungslogik

Die Safe-Replace-Funktion ist so optimiert, dass sie nur gültige Ersetzungen durchführt:

```python
def safe_replace(text: str, replacements: List[Tuple[str, str, str]]) -> str:
    valid_replacements = {}
    # Nur relevante Ersetzungen sammeln
    for old, new, placeholder in replacements:
        if old in text:  # Vorprüfung, ob die Ersetzung überhaupt relevant ist
            text = text.replace(old, placeholder)
            valid_replacements[placeholder] = new
    # Nur die gesammelten relevanten Ersetzungen durchführen
    for placeholder, new in valid_replacements.items():
        text = text.replace(placeholder, new)
    return text
```

## 10. Fazit und Entwicklungshinweise

Die Esperanto-Kanji-Konverter-Anwendung ist ein komplexes System zur Textverarbeitung und -transformation, das spezifisch auf die Eigenschaften der Esperanto-Sprache zugeschnitten ist. Die wichtigsten technischen Aspekte sind:

1. Verwendung von Platzhaltern für sichere, mehrstufige Ersetzungen
2. Morphologische Analyse und Verarbeitung von Esperanto-Texten
3. Flexible Ausgabeformatierung mit verschiedenen Anzeigeoptionen
4. Parallelverarbeitung für bessere Leistung bei großen Texten
5. Benutzerdefinierte Regelerstellung für maximale Flexibilität

Für Entwickler, die an dieser Anwendung arbeiten möchten, sind die folgenden Datei wichtig:

- **main.py**: Für Änderungen an der Hauptbenutzeroberfläche
- **esp_text_replacement_module.py**: Für Änderungen an der Textverarbeitungslogik
- **esp_replacement_json_make_module.py**: Für Änderungen an der Formatierung und JSON-Generierung
- **Seite zur Erstellung...**: Für Änderungen an der Regelerstellungs-Benutzeroberfläche

Die Anwendung bietet zahlreiche Möglichkeiten für Erweiterungen, wie z.B. die Unterstützung weiterer Sprachen, verbesserte Algorithmen für die morphologische Analyse oder zusätzliche Ausgabeformate.