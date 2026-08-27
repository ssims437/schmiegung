# Schmiegung

**Jeder Verlust einzeln gemessen.** Verlustfreie Kompression macht die Reihe anderswo — hier wird
geworfen, damit weniger übrig bleibt, und hingeschrieben, wie viel es war. Der Kern der JPEG-
Bildkompression: Farbraum, Teilabtastung, Transformation, gewichtete Rundung — und eine Bitfolge
samt Dateikopf, die dieses Blatt **komplett von Hand** schreibt. Der Browser liest dieselben Bytes
als unabhängiger Zeuge.

→ **[Anschauen](https://ssims437.github.io/schmiegung/)**

- **Vier Tafeln** — Original, Rekonstruktion, verstärkter Unterschied und der Browser-Decodierung
  der selbst geschriebenen Datei, Seite an Seite
- **Stufenzeilen** — jede Stufe mit dem Schaden, den sie **allein** beiträgt: Farbe 0,33, Halbierung
  2,24, Rundung 3,88 mittlerer |Δ| (Güte 75, 4:2:0); die Entropiecodierung dazwischen verliert nichts
- **In einen Block hineinsehen** — aufs Bild klicken: 64 Frequenzen, jede mit ihrem gerundeten
  Quotienten und ihrer Tabellenstufe
- **Eigener strenger Deuter** — parst Markierungen, Tabellen und Codesatzbücher echt aus den Bytes
  und wirft bei allem, was er nicht herleiten kann
- **Güte-Regler und 4:2:0-Schalter** — von ×16 Kompression bei PSNR 30 dB bis zur Grenze, an der
  die Tabelle bei 255 deckelt
- **Prüflauf** — acht Zeilen, zwei Gegenproben, 0,2 Sekunden

## Warum das überhaupt prüfbar ist

Jede Stufe ist ein **definierbarer Übergang** — also wird jede einzeln gegen ihren Wortlaut
gestellt, nicht das Format als Ganzes gegen ein Bauchgefühl. Und der schärfste Zeuge steht außerhalb:
der Browser bekommt die selbst geschriebenen Bytes vorgesetzt und muss daraus mit eigenem Decoder
ein Bild machen. Wenn selbstgebaute Markierungen, selbst synthetisierte Codesatzbücher und selbst
gestopfte Bitfolgen vom fremden Werkzeug akzeptiert und nahezu deckungsgleich zurückgegeben werden
(Korrelation 0,999 gegen den eigenen Rückweg), sprechen **zwei unabhängige Implementierungen** —
und die Gegenproben zeigen, dass der Vergleich auch dann zuschlägt, wenn er soll.

## Was der Prüflauf zeigt

| Behauptung | Ergebnis |
|---|---|
| zwei Farbformulierungen, ein Ergebnis | **24 311** Farbstellen durch Koeffizientenvorschrift und Kr/Kb-Bau · **0** Abweichungen · Grauachse: alle 256 Graustufen farblos |
| Halbieren darf nicht mehr als die Schiefheit kosten | sämtliche **65 536** Byte-Paare: die Rückgabe liegt nie über der Hälfte der Differenz · **0** Übertretungen |
| Transform gegen ihren Wortlaut | 3 016 Blockfiguren: Doppelsumme ↔ Schnellfassung **2,3·10⁻¹³**, Rückweg **6,8·10⁻¹³**, Energiebilanz **1,3·10⁻¹⁵** — Parseval hält |
| Runden innerhalb der halben Stufe | alle **520 455** Paare (Stufen 1…255, Werte −1020…1020) hin und zurück · **0** Übertretungen |
| Codesätze halten fremdem Wortlaut stand | 8 veröffentlichte Kodewörter (u. a. „0/0“ = 1010, „5/2“ = 11111110111) treffen buchstabengetreu · Journal 324 = 162 + 162 |
| Bitströme kehren bitweise zurück | **12** eigene Dateien durch den strengen Deuter: alle **18 432** Quotienten identisch, Struktur frisch aus den Bytes geparsed |
| Gegenprobe: das gekippte Bit | **317** Einzelbit-Störungen · **317** bemerkt (86 verworfen, 231 anders decodiert) — der Vergleich schlägt an |
| Gegenprobe: der Farben-Tauscher | Cb ↔ Cr vertauscht: mittlerer |Δ| **30,6** gegen 3,9 sauber · der Browser sieht **30,3** — zwei Zeugen, ein Befund |

## Was mich das gekostet hat

**Die fehlenden 128.** Das schlimmste war das leiseste: JPEG verlangt vor der Transformation den
Pegelversatz −128 und nach der Rückkehr +128. Ich hatte beides vergessen — und der eigene
Roundtrip blieb **grün**, weil Schreiber und Deuter denselben Blindfleck teilten: hin ohne Versatz,
zurück ohne Versatz, Differenz null. Erst der Browser-Zeuge fiel um: Grau 150 kam als gesättigtes
Magenta zurück (mittlerer |Δ| 82). Gefunden über ein 8×8-Einfarbig-Bild — Grau, Grün, zwei Blöcke.
Die Lektion steht jetzt als Struktur im Blatt: Ein Vergleich zwischen zwei Dingen, die denselben
Fehler machen, ist kein Vergleich. Deshalb liest hier ein fremder Decoder dieselben Bytes.

**Energie verschwand auf exakt ein Viertel.** Die DCT-Gewichte hatten doppelt schief: DC bekam
√½/√8 statt √⅛, die übrigen Frequenzen 1/√8 statt ½. Der Prüflauf meldete Rot, aber die Zeile
verschwieg ihre Messwerte — fest eingetippte Nullen statt Zahlen, der alte Fehler in neuer
Verkleidung. Der Einzelnadel-Test brachte es auf einen Blick: Eingangsenergie 262 144, Ausgangs-
energie 65 536 — Parseval verletzt um exakt Faktor 4, also zwei Achsen à Faktor 2. Nach der
Korrektur: Wortlaut 2,3·10⁻¹³, Energie 1,3·10⁻¹⁵. Seitdem schreibt die Zeile ihre gemessenen
Exponenten selbst hin.

**Cr polsterte aus der falschen Ebene.** Beim 4:2:0-Weg bog ich die Variable für Cb auf die halbierte
Ebene um — und vergaß Cr. Das Polstern las daraus mit halber Breite: räumlicher Versatz, im Bild ein
Farbschleier, mittlerer |Δ| auf Rot **72**. Gefunden über die Kanal-Isolierung: ΔR 71,6 · ΔG 36,3 ·
ΔB 6,7 — nur B war unbeteiligt, also war es Cr. Danach: 4,3. Die 4:4:4-Zeile war die Ankerprobe:
blieb sie sauber, konnte der Fehler nur im Halbierungszweig sitzen.

**Ein Fix, der den Zustand verschlechterte.** Als die 4:4:4-Rekonstruktion schleierig aussah, habe
ich die Dequantisierung „repariert“: Tabellenstelle über die Zickzack-Ordnung indiziert. Die PSNR
fiel von 33 auf 20 dB — der Fix war die Ursache. Die Quotienten stehen im Merker bereits im
Blockraster, richtig ist schlicht die Identität. Zurückgerollt anhand der Zahl, nicht des Gefühls.

**EOI mitten im Strom.** Der erste Schreiber sammelte die Scan-Bits in einem eigenen Puffer und
hing sie **ans Dateiende** — nach dem EOI. Mein Deuter brach mit „Scan endet mitten im Symbol“ ab,
und die Spur zeigte `FF D9` an sechster Stelle des Scans. Seit dem fließen fertige Bytes direkt in
den Markerstrom; die Stopfung nach 0xFF entsteht am selben Ort.

**Der vorschnelle Fix an den Segmentlängen.** Als der Deuter bei DQT warf, habe ich erst die
Längen „korrigiert“ (67 → 65, 3+16+n → 17+n) — laut Lesart, nicht nach Messung. Die Meldung blieb
exakt gleich, und die Spur zeigte den wahren Befund: Der Deuter las Präzision und Tabellenkennung
als **zwei** Bytes, die Norm schreibt sie als **ein** kombiniertes Pq/Tq-Byte. Beide Änderungen
wurden zurückgenommen; die Zickzack-Ordnung der DQT dagegen blieb — sie war der zweite,
echte Fund aus demselben Symptom (eigene Datei in fremden Decodern falsch, intern konsistent).

**Die Verwandten des Norm-Anhangs.** Die AC-Codesatzbücher stehen als BITS- plus WERTE-Liste in
der Norm; aus einer selbst gestutzten WERTE-Liste fehlten beim Chroma-Book die hinteren Einträge —
`codesatz()` wirft, sobald die BITS-Zahl nicht zur Werteliste passt. Korrekturfassung: die
vollständige 162er-Fassung, und der Prüflauf hält acht veröffentlichte Kodewörter gegen die eigene
Synthese, damit das Buch nicht nur groß, sondern auch richtig ist.

## Technik

Eine einzelne HTML-Datei. Kein Build, keine Bibliothek, nichts verlässt den Browser.
Farbraum ganzzahlig (BT.601), DCT-II orthogonal normiert in zwei Stufen neben ihrem
Doppelsummen-Wortlaut, Basistabellen aus dem Normanhang K mit IJG-Skalierung, Huffman-Synthese nach
der BITS/HUFFVAL-Vorschrift, Bytestopfung im Bitstrom, JFIF-Kopf von Hand. Der eigene Deuter parst
Markierung für Markierung aus denselben Bytes; der Browser-Decodierung dient als unabhängiger Zeuge.
Hell und dunkel über `prefers-color-scheme` und `[data-theme]`.

## Die ganze Sammlung

Alle Blätter nach Feld geordnet, jedes mit eigenem Repo:
**[ssims437.github.io](https://ssims437.github.io/)**

## Lizenz

MIT
