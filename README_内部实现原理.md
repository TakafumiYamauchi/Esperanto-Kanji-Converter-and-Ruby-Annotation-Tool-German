# Technische Dokumentation: Esperanto-Kanji-Konverter und Ruby-Annotations-Tool

## Für fortgeschrittene Programmierer und Entwickler

Diese technische Dokumentation bietet einen detaillierten Einblick in die Architektur und Funktionsweise des Esperanto-Kanji-Konverters. Die Anwendung wurde mit Streamlit in Python entwickelt und besteht aus vier Hauptmodulen mit komplexen Funktionen für die Textverarbeitung und -transformation.

## Inhaltsverzeichnis

1. Übersicht der Anwendungsarchitektur
2. Hauptkomponenten und ihre Beziehungen
3. Detaillierte Analyse von `main.py`
4. Analyse der JSON-Generierungsseite
5. Tiefgehende Betrachtung des `esp_text_replacement_module.py`
6. Analyse des `esp_replacement_json_make_module.py`
7. Wichtige Algorithmen und Datenstrukturen
8. Performance-Optimierungen und Parallelverarbeitung
9. Erweiterungsmöglichkeiten

## 1. Übersicht der Anwendungsarchitektur

Die Anwendung ist als Streamlit-Webanwendung konzipiert und besteht aus zwei Hauptseiten:
- `main.py`: Die Hauptseite für die Textumwandlung
- Eine zweite Seite für die Erstellung benutzerdefinierter JSON-Ersetzungsregeln

Die Funktionalität ist auf vier Python-Module verteilt:

1. **main.py**: Enthält die Hauptbenutzeroberfläche und den Einstiegspunkt der Anwendung
2. **Seite zur Erstellung einer JSON-Datei...**: Enthält den Code für die zweite Streamlit-Seite zur JSON-Generierung
3. **esp_text_replacement_module.py**: Enthält Kernfunktionen für die Textverarbeitung
4. **esp_replacement_json_make_module.py**: Enthält Funktionen zur Erstellung und Verarbeitung von Ersetzungsregeln

Die Anwendung arbeitet mit mehreren Datentypen:
- CSV-Dateien für Esperanto-Wortstamm-zu-Übersetzung-Mappings
- JSON-Dateien für Ersetzungsregeln und Wortstammzerlegungsdefinitionen
- Textdateien für Platzhalter und andere Hilfsdaten

## 2. Hauptkomponenten und ihre Beziehungen

### Datenfluss im System:

```
[Benutzereingabe/CSV-Upload] → [JSON-Generierung] → [Ersetzungsregeln] → [Textumwandlung] → [Output]
```

### Komponenten-Interaktion:

- Die Hauptseite (`main.py`) importiert Funktionen aus `esp_text_replacement_module.py`
- Die JSON-Generierungsseite importiert Funktionen aus beiden Hilfsmodulen
- Beide Hauptmodule greifen auf gemeinsame Ressourcendateien zu (Platzhaltertexte, Zeichenbreiten-JSON)
- Die Parallelverarbeitungsfunktionen in beiden Modulen nutzen das `multiprocessing`-Modul von Python

Die Anwendung verwendet mehrere fortgeschrittene Konzepte:
- Caching von Ergebnissen mit `@st.cache_data`
- Parallelverarbeitung für Performance-Optimierung
- Komplexe Regex-Operationen für Textmanipulation
- Spezialisierte Platzhalter-Mechanismen für sichere Textersetzung

## 3. Detaillierte Analyse von `main.py`

### Initialisierung und Imports

```python
import streamlit as st
import re
import io
import json
import pandas as pd
from typing import List, Dict, Tuple, Optional
import streamlit.components.v1 as components
import multiprocessing
```

Bemerkenswert ist die Verwendung von `multiprocessing` mit dem expliziten Setzen des Start-Modus auf "spawn":

```python
try:
    multiprocessing.set_start_method("spawn")
except RuntimeError:
    pass
```

Diese Konfiguration verhindert `PicklingError`-Ausnahmen bei der Verwendung von Multiprocessing in Streamlit.

### Kern-Datenstrukturen

Die Anwendung arbeitet mit drei Haupttypen von Ersetzungslisten:

1. `replacements_final_list`: Für globale Textersetzungen im gesamten Dokument
2. `replacements_list_for_localized_string`: Für lokale Ersetzungen innerhalb spezieller Markierungen
3. `replacements_list_for_2char`: Für die Behandlung von zweistelligen Wortstämmen

Diese Listen haben ein einheitliches Format als Tupel `(old, new, placeholder)`, wobei:
- `old`: Der zu ersetzende Originaltext
- `new`: Der Ersetzungstext
- `placeholder`: Ein einzigartiger Platzhalter für den Zwischenschritt der Ersetzung

### Hauptfunktionen

#### `load_replacements_lists`

```python
@st.cache_data
def load_replacements_lists(json_path: str) -> Tuple[List, List, List]:
    """
    JSONファイルをロードし、以下の3つのリストをタプルとして返す:
    1) replacements_final_list
    2) replacements_list_for_localized_string
    3) replacements_list_for_2char
    """
```

Diese Funktion lädt die Ersetzungsregeln aus einer JSON-Datei und nutzt `@st.cache_data` für Performanceoptimierung.

#### Orchestrierung der Textverarbeitung

Die Hauptlogik der Textverarbeitung liegt in den importierten Funktionen:

```python
processed_text = orchestrate_comprehensive_esperanto_text_replacement(
    text=text0,
    placeholders_for_skipping_replacements=placeholders_for_skipping_replacements,
    replacements_list_for_localized_string=replacements_list_for_localized_string,
    placeholders_for_localized_replacement=placeholders_for_localized_replacement,
    replacements_final_list=replacements_final_list,
    replacements_list_for_2char=replacements_list_for_2char,
    format_type=format_type
)
```

Oder in der parallelen Version:

```python
processed_text = parallel_process(
    text=text0,
    num_processes=num_processes,
    placeholders_for_skipping_replacements=placeholders_for_skipping_replacements,
    replacements_list_for_localized_string=replacements_list_for_localized_string,
    placeholders_for_localized_replacement=placeholders_for_localized_replacement,
    replacements_final_list=replacements_final_list,
    replacements_list_for_2char=replacements_list_for_2char,
    format_type=format_type
)
```

### Benutzeroberfläche

Die Hauptseite verwendet Streamlit-Komponenten für:
- Datei-Upload (JSON und Text)
- Textbearbeitung
- Ausgabeformat-Auswahl
- Parallelverarbeitungseinstellungen
- Vorschau und Download von Ergebnissen

Besonders interessant ist die Verwendung von `st.components.html()` für die Vorschau von HTML-Ausgaben mit Ruby-Annotationen.

## 4. Analyse der JSON-Generierungsseite

Die zweite Seite dient zur Generierung von benutzerdefinierten JSON-Ersetzungsregeln.

### Wichtige Datenstrukturen

```python
# Daten für Verbsuffixe
verb_suffix_2l = {
    'as':'as', 'is':'is', 'os':'os', 'us':'us','at':'at','it':'it','ot':'ot',
    'ad':'ad','iĝ':'iĝ','ig':'ig','ant':'ant','int':'int','ont':'ont'
}

# Listen für spezielle Suffixe "an" und "on"
AN=[['dietan', '/diet/an/', '/diet/an'], ['afrikan', '/afrik/an/', '/afrik/an'], ...]
ON=[['duon', '/du/on/', '/du/on'], ['okon', '/ok/on/', '/ok/on'], ...]

# Zweistellige Wortstammlisten
suffix_2char_roots=['ad', 'ag', 'am', 'ar', 'as', 'at', ...]
prefix_2char_roots=['al', 'am', 'av', 'bo', 'di', 'du', ...]
standalone_2char_roots=['al', 'ci', 'da', 'de', 'di', ...]
```

### Generierungsprozess

Der Kernprozess besteht aus:

1. Laden von CSV- und JSON-Eingabedaten
2. Erstellen temporärer Ersetzungswörterbücher
3. Anwenden benutzerdefinierter Regeln
4. Parallelverarbeitung für große Datensätze
5. Erstellung der endgültigen Ersetzungslisten
6. Exportieren als JSON-Datei

Ein komplexer Schritt ist die Prioritätsanpassung für verschiedene Worttypen. Zum Beispiel:

```python
if "名词" in j[1]:
    for k in ["o","on",'oj']:
        if not i+k in pre_replacements_dict_2:
            pre_replacements_dict_3[i+k]=[j[0]+k,j[2]+len(k)*10000-3000]
```

Dies erstellt Einträge für Substantive (名词) mit verschiedenen Endungen und passt die Priorität an.

## 5. Tiefgehende Betrachtung des `esp_text_replacement_module.py`

Dieses Modul enthält Kernfunktionen für die Textverarbeitung.

### Umwandlung von Esperanto-Zeichen

```python
x_to_circumflex = {
    'cx': 'ĉ', 'gx': 'ĝ', 'hx': 'ĥ', 'jx': 'ĵ', 'sx': 'ŝ', 'ux': 'ŭ',
    'Cx': 'Ĉ', 'Gx': 'Ĝ', 'Hx': 'Ĥ', 'Jx': 'Ĵ', 'Sx': 'Ŝ', 'Ux': 'Ŭ'
}
```

Diese Wörterbücher definieren die Umwandlung zwischen verschiedenen Esperanto-Zeichendarstellungen.

### Safe-Replace-Mechanismus

```python
def safe_replace(text: str, replacements: List[Tuple[str, str, str]]) -> str:
    """
    (old, new, placeholder) のリストを受け取り、
    text中の old → placeholder → new の段階置換を行う。
    """
    valid_replacements = {}
    # まず old→placeholder
    for old, new, placeholder in replacements:
        if old in text:
            text = text.replace(old, placeholder)
            valid_replacements[placeholder] = new
    # 次に placeholder→new
    for placeholder, new in valid_replacements.items():
        text = text.replace(placeholder, new)
    return text
```

Diese Funktion ist entscheidend für die sichere Textersetzung. Sie vermeidet Probleme bei überlappenden Ersetzungen durch einen zweistufigen Prozess:
1. Ersetze alle Originaltexte durch einzigartige Platzhalter
2. Ersetze dann alle Platzhalter durch die Zieltexte

### Orchestrierung der Textverarbeitung

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
```

Diese Funktion führt den gesamten Textverarbeitungsprozess durch:
1. Normalisierung von Leerzeichen und Esperanto-Zeichen
2. Markierung von zu überspringenden Textbereichen
3. Lokale Ersetzungen
4. Globale Ersetzungen
5. Zweistellige Wortstammersetzungen
6. Wiederherstellung markierter Bereiche
7. HTML-Formatierung (falls erforderlich)

### Parallelverarbeitung

```python
def parallel_process(
    text: str,
    num_processes: int,
    # weitere Parameter...
) -> str:
```

Diese Funktion teilt den Text in Segmente auf und verarbeitet diese parallel.

## 6. Analyse des `esp_replacement_json_make_module.py`

Dieses Modul enthält Funktionen für die Erstellung und Verarbeitung von Ersetzungsregeln.

### Textformatierung

```python
def output_format(main_text, ruby_content, format_type, char_widths_dict):
    """
    エスペラント語根(main_text) と それに対応する訳/漢字(ruby_content) を
    指定の format_type で繋ぎ合わせる
    """
```

Diese Funktion erstellt formatierte Texte basierend auf dem gewählten Ausgabetyp:
- HTML mit Ruby-Annotationen
- HTML mit angepassten Ruby-Größen
- Klammer-Formate
- Einfache Ersetzung

### Zeichenbreiten-Berechnung

```python
def measure_text_width_Arial16(text, char_widths_dict: Dict[str, int]) -> int:
    """
    JSONで読み込んだ {文字: 幅(px)} の辞書を使い、
    text の合計幅を算出する
    """
```

Diese Funktion berechnet die Textbreite basierend auf einem Zeichenbreiten-Wörterbuch.

### Parallelverarbeitung für die Vorverarbeitung

```python
def parallel_build_pre_replacements_dict(
    E_stem_with_Part_Of_Speech_list: List[List[str]],
    replacements: List[Tuple[str, str, str]],
    num_processes: int = 4
) -> Dict[str, List[str]]:
```

Diese Funktion verarbeitet große Mengen von Esperanto-Wortstamm-Daten parallel.

## 7. Wichtige Algorithmen und Datenstrukturen

### Platzhalter-Mechanismus

Die Anwendung verwendet vordefinierte Platzhalter aus Textdateien, um Konflikte bei Ersetzungen zu vermeiden:

```python
placeholders_for_skipping_replacements: List[str] = import_placeholders(
    './Appの运行に使用する各类文件/占位符(placeholders)_%1854%-%4934%_文字列替换skip用.txt'
)
```

### Prioritätsbasierte Ersetzung

Die Anwendung verwendet numerische Prioritäten, um die Reihenfolge der Ersetzungen zu steuern:

```python
pre_replacements_dict_3[i+k]=[j[0]+k,j[2]+len(k)*10000-3000]
```

Die Priorität basiert oft auf der Textlänge (längere Texte haben Vorrang) und wird durch bestimmte Regeln angepasst.

### Regex-Muster für spezielle Markierungen

```python
PERCENT_PATTERN = re.compile(r'%(.{1,50}?)%')
AT_PATTERN = re.compile(r'@(.{1,18}?)@')
```

Diese Muster identifizieren speziell markierte Textteile, die anders behandelt werden sollen.

## 8. Performance-Optimierungen und Parallelverarbeitung

### Textaufspaltung und parallele Verarbeitung

```python
def parallel_process(text: str, num_processes: int, ...):
    # ...
    lines = re.findall(r'.*?\n|.+$', text)
    # ...
    with multiprocessing.Pool(processes=num_processes) as pool:
        results = pool.starmap(process_segment, [...])
```

Der Text wird in Zeilen aufgeteilt und auf mehrere Prozesse verteilt.

### Caching von Ersetzungslisten

```python
@st.cache_data
def load_replacements_lists(json_path: str) -> Tuple[List, List, List]:
```

Der `@st.cache_data`-Dekorator speichert die Ergebnisse für wiederholte Aufrufe.

### Zeichensatz-Normalisierung

```python
def unify_halfwidth_spaces(text: str) -> str:
    pattern = r"[\u00A0\u2002\u2003\u2004\u2005\u2006\u2007\u2008\u2009\u200A]"
    return re.sub(pattern, " ", text)
```

Diese Funktion optimiert die Textverarbeitung durch Normalisierung verschiedener Leerzeichen-Varianten.

## 9. Erweiterungsmöglichkeiten

### Hinzufügen neuer Ausgabeformate

Um ein neues Ausgabeformat hinzuzufügen:
1. Erweitern Sie das `options`-Wörterbuch in `main.py`
2. Aktualisieren Sie die `output_format`-Funktion in `esp_replacement_json_make_module.py`
3. Aktualisieren Sie die `apply_ruby_html_header_and_footer`-Funktion falls nötig

### Mehrsprachige Unterstützung erweitern

Die Anwendung unterstützt bereits mehrere Sprachen, wie die URLs am Ende von `main.py` zeigen. Um weitere Sprachen hinzuzufügen:
1. Erstellen Sie neue CSV-Dateien mit Wortstamm-Übersetzungen für die Zielsprache
2. Aktualisieren Sie die UI-Texte in der Streamlit-Anwendung

### Performance-Optimierungen

Mögliche Verbesserungen:
1. Implementierung eines inkrementellen Updates der Ersetzungslisten
2. Verwendung effizienterer Datenstrukturen für große Textmengen
3. Vorverarbeitung häufig verwendeter Ersetzungsmuster

### Neues Merkmal: Statistische Analyse

Ein interessantes Feature wäre die Implementierung einer Analyse der Verwendungshäufigkeit von Wortstämmen:
1. Zählen der Häufigkeit von Wortstämmen im Original- und umgewandelten Text
2. Visualisierung der häufigsten Ersetzungen
3. Vorschläge für Optimierungen der Ersetzungsregeln

## Zusammenfassung

Die Esperanto-Kanji-Konverter-Anwendung zeigt einen komplexen und durchdachten Ansatz zur Textverarbeitung mit mehreren Verarbeitungsschichten. Die Kernkonzepte sind:

1. **Sichere Textersetzung**: Durch den zweistufigen Platzhalter-Mechanismus
2. **Flexible Anpassung**: Durch JSON-basierte Konfiguration und Benutzerschnittstellen
3. **Performance-Optimierung**: Durch Parallelverarbeitung und Caching
4. **Ausgabeformate**: Vielfältige HTML- und Textformate mit Ruby-Annotationen

Als Entwickler können Sie diese Konzepte für eigene Textverarbeitungsprojekte nutzen oder die Anwendung um neue Funktionen erweitern.
