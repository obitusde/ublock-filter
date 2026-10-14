# CLAUDE.md – uBlock-Filterliste "Mobile News Reading"

Statische uBlock-Origin-Kosmetikfilter für mobiles Lesen (Android, Firefox/Chrome mit uBlock Origin). Ziel: auf News- und Kleinanzeigen-Seiten im Wesentlichen nur den Artikelinhalt sehen – kein Header-/Footer-Rauschen, keine Empfehlungs-, Werbe- und Newsletter-Boxen, keine generischen Stockfotos.

- Einzige Datei mit Regeln: `ublock-statische-filter.txt`
- Abo-URL (in uBlock unter *Filterlisten → Eigene* eingetragen): `https://raw.githubusercontent.com/obitusde/ublock-filter/main/ublock-statische-filter.txt`
- Das Repo ist die einzige gültige Quelle. Die alte kuratierte Datei im Google-Drive-Ordner "ublock" wird nicht mehr gepflegt; dort landen nur noch die Handy-Exporte (`ublock-statische-filter_<Zeitstempel>.txt`).

## Arbeitsregeln

- **Sprache:** Deutsch. Antworten knapp und direkt. Ehrlich mit Unsicherheit umgehen, keine falsche Sicherheit.
- **Versionsnummer bei jeder Änderung** hochzählen (`! Version: JJJJ.MM.TT.N` im Dateikopf). Commit-Messages auf Deutsch mit Versionsnummer.
- **Ordnung der Datei:** Abschnitte nach Domain alphabetisch sortiert, je ein Kommentar-Header `! --- domain (Status/Datum) ---`. Innerhalb eines Abschnitts ist die Reihenfolge egal. Ungetestete oder nicht bestätigte Seiten kommen ans Ende in einen eigenen Block, mit kurzem Kommentar.
- **Nutzerregeln nur entfernen, wenn sie offensichtlich falsch sind.** Was nicht reproduzierbar ist, bleibt drin und wird als "nicht reproduzierbar" markiert. Die eigene Beobachtung des Nutzers am Gerät hat Vorrang vor einem 0-Treffer-Ergebnis in der Sandbox (Beispiel: `zeit.de##.volume-teaser`).
- **Selbst sinnvolle Regeln ergänzen ist erwünscht**, aber nur geprüft.
- **Domain-Präfix:** ohne `www.` schreiben (`golem.de##…` gilt auch für `www.golem.de`). Ausnahme: bestehende `www.20min.ch,20min.ch##…`-Zeilen so lassen.
- **Syntax-Check vor jedem Commit:**
  - keine doppelten Regelzeilen
  - jede Nicht-Kommentarzeile enthält `##` oder beginnt mit `||`
  - keine Leerzeichen am Zeilenende
  - Nutzerregeln sind alle noch vorhanden (Diff prüfen)
- **Laufender Ablauf:** Der Nutzer pickt neue Regeln am Handy mit dem uBlock-Element-Picker. Sie landen in *Meine Filter*. Er kopiert sie in den Chat ("Neue Regeln vom Handy: …"). Claude prüft sie, sortiert sie ein, bumpt die Version und pusht. Der Nutzer löscht sie danach in *Meine Filter* und tippt in uBlock auf *Jetzt aktualisieren*.
- **Gut zu wissen:** Der uBlock-Import unter *Meine Filter* hängt nur an (identische Zeilen werden zusammengeführt). Er ersetzt nichts. Darum das Abo per Raw-URL – dabei ersetzt uBlock die Liste bei jedem Update komplett.

## Prüfmethode

(Ersetzt den früheren Block "HINWEISE FUER KUENFTIGE FILTER-PRUEFUNGEN" aus der Filterdatei.)

Neue oder geänderte Regeln vor dem Einbauen live prüfen. Geprüft wird mit mobilem Viewport/User-Agent (375×812), weil die Liste auf dem Handy genutzt wird.

1. **Trefferzahl** auf mehreren Artikelseiten **und** auf Start- und Übersichtsseiten prüfen. Generische Klassen werden oft seitenweit wiederverwendet. Beispiel: Auf SRF traf `.collection.js-collection` 17 Elemente auf der Startseite statt nur die Meistgelesen-Box.
2. **Isolation:** `pCount` (Anzahl `<p>`) und `textLen` des Treffers ansehen. Es darf nur Widget-Text sein, kein echter Artikeltext.
3. **Hash-Klassen** (MUI `mui-style-*`, styled-components `sc-*-*`, CSS-Modules `Xyz_component__hash`) brechen bei jedem Redeploy der Seite. Wenn möglich stabile ID, `data-testid`, semantische Tags oder Link-Ziele nutzen (Beispiel digitec.ch: `header` und `#pageFooter` statt Hash-Klassen). Sonst im Kommentar vermerken, dass ein Redeploy die Regel brechen kann.
4. **Geteilte Wrapper-Klassen:** Dieselbe Komponente kann erwünschten und unerwünschten Inhalt tragen (Beispiel 20min.ch: "Darum gehts"-Zusammenfassung vs. Werbebox in derselben InfoBox). Nie eine Klasse blind übernehmen, ohne alle ihre Vorkommen auf der Seite zu prüfen. Eingrenzen über `:has()`, `:has-text()`, `:nth-of-type()` oder ein Link-Ziel (`a[href*=…]`) auf ein eindeutiges Unterelement.
5. **`:has-text()` ist uBlock-Syntax**, kein natives CSS. Beim Testen per JS nachbauen mit `el.textContent.includes(…)`; `querySelectorAll` versteht es nicht.
6. **Bei 0 Treffern** im gesamten HTML nach dem Klassen-**Token** suchen, nicht nach einem Teilstring. Beispiel: digitec `yVsAy41` steckt als Teilstring in `yVsAy411`. So unterscheidet man "auf dieser Seite gerade nicht vorhanden" (evtl. nur bei bestimmten Artikeltypen) von "tot".
7. **Cookie-/Consent-Banner:** die datenschutzfreundlichste Option wählen (nur notwendige Cookies). Nichts akzeptieren ohne Zustimmung des Nutzers. Für die Filter-Funktion selbst spielt der Consent-Status keine Rolle.
8. **Flache Leisten** wie Lesefortschrittsbalken: nach jedem sehr flachen Element suchen (≤ 8 px), nicht nur nach `position: fixed/sticky`.
9. **Nie selbst einloggen.** Inhalte hinter Login oder Paywall kann der Nutzer bei Bedarf in einer anderen Umgebung freigeben (dafür die Cowork-Session mit Browser-Fenster nutzen).
10. **Grenzen der Sandbox:** Manche Elemente erscheinen nur eingeloggt, nach Consent, per JavaScript oder im A/B-Test. Dann als "nicht reproduzierbar" markieren statt löschen.

Praktischer Trick: Auf einer geöffneten Seite derselben Domain mit `fetch()` und `DOMParser` 20–40 Artikel auf einmal prüfen. Das geht schneller als jede Seite einzeln zu öffnen.

## Seitenwissen (Stand 13.10.2026)

- **20min.ch:** `div[data-testid="InfoBox"]` trägt sowohl die erwünschte "Darum gehts"-Zusammenfassung als auch Werbe- und Hinweisboxen. Darum über Icon (`info-box-bell`) oder Text eingrenzen. Die FR-Ausgabe hat andere Hash-Klassen. Der Lesefortschrittsbalken ist `.sc-835cd513-6`.
- **anibis.ch / tutti.ch:** gleiche MUI-Plattform, gleiche Hashes.
  - `.mui-style-t4u9pe` ist der Block "Ähnliche Suchanfragen" unter Inseratslisten (verifiziert auf beiden Seiten).
  - `xtxbqo`, `1x1x4w3` und `6ka9oi` waren in der Sandbox nicht reproduzierbar (Startseite, Listen und Inserate geprüft). Vermutlich nur eingeloggt oder in einem bestimmten Zustand sichtbar.
- **digitec.ch** (Magazin):
  - `section.yI0wRwL` ist der Artikeltext **und** Karussell/Kommentare. Darum `.yd6GJaF > section.yI0wRwL:nth-of-type(2|3)`.
  - `section.yI0wRwL > .ylyXkc41` sind eingeschobene Teaserkarten (verifiziert).
  - `.yVsAy41` hatte 0 Treffer, Hash vermutlich geändert.
  - `header` und `#pageFooter` sind stabil.
- **golem.de:**
  - Jedes Artikelbild ist `figure.go-media` mit `span.go-media__credit` in der `figcaption`. Generische Bilder werden über den Credit-Text erkannt (Pixabay, Shutterstock, iStock, Adobe Stock, Unsplash, Pexels, Freepik, Getty Images, KI-generiert, "(erzeugt mit").
  - **Getty bleibt bewusst drin** (Entscheidung des Nutzers), obwohl darunter auch echte Agenturfotos (AFP via Getty) sind.
  - "Easy Fox" ist Spiel-Artwork, nicht blocken.
  - Eigenwerbe-Absätze (`p > em > a`) werden über das Link-Ziel erkannt: community.golem.de/invites, karrierewelt-newsletter, service.golem.de/products/shop. `p.go-article-end` ist auch der echte letzte Textabsatz, also nie allein verwenden.
- **heise.de:** `www.heise.de##` und `heise.de##` sind gleichwertig. Die Notice-Box (`<details>`) ist verifiziert.
  - Artikelbilder: `a-lightbox.article-image` > `figure` mit `p.a-caption__source` ("(Bild: … / Shutterstock.com)"). Stockfotos über den Credit-Text, analog golem. Stand 14.10. nur Shutterstock (ca. 30 % der Artikel), KI-Bilder ("… / KI / heise medien") bewusst nicht gefiltert.
  - Die sichtbare Kopfleiste ist `#topnavimodule` (stabile ID), nicht `header.theme-ho`. Darin verlinken "Abo"-Button und Menüpunkt "heise+" beide auf `/plus`.
- **srf.ch:**
  - `.expandable-box` NICHT blocken, das ist echter Inhalt.
  - "Diskutieren Sie mit" besteht aus `h3.article-subheading` und `.article-element--bleed` mit Embed (iframe dialog.srf.ch). `.article-element--bleed` wird auch für andere Embeds und Bilder benutzt, deshalb über den Embed-Titel im Attribut `data-cmp-protected-embed` eingrenzen.
- **tagesschau.de:** Bei "Mehr zum Thema"/"Mehr von" greift nur die Variante `div[class*="columns"]:has-text(…)`.
- **thelausanneguide.com:** Die Nutzerregel trifft den kompletten `<footer>` (Newsletter). `footer` ist zusätzlich als stabile Variante drin.
- **toppreise.ch:** `#tpFrame > .d-md-none` entspricht `div#advertising_mobile` (mobiler Werbeplatz). Beide Regeln sind drin.
- **zeit.de:**
  - `.zoner-injection-NSDAP` ist die "NS-Akten"-Rechercheservice-Box, verifiziert mit Login auf einem Z+-Artikel. Das alte Pauschal-`[class*="zoner-injection"]` ist tot.
  - `.volume-teaser` hat der Nutzer am Gerät bestätigt, in der Sandbox gab es 0 Treffer. Behalten.
- **gmx.net:** Die Sandbox sieht nur die Consent-Wall (zustimmen oder zahlen), `.regbanner` war nicht prüfbar.
- **stern.de:** `paid-toaster` wird per JavaScript eingeblendet und war nicht prüfbar.

## Offene Punkte

- **Ungetestete Altregeln** (könnten tot oder zu breit sein): Süddeutsche (3), Portfolio Sharesight, Google Apps Script, Focus.de, AllMusic, NLA catalogue (2), bib-ostschweiz/genios. Prüfen nur, wenn der Nutzer es wünscht.
- **Ältere Hash-Regeln** (20min `sc-*`, digitec `y*`, Tutti): nur neu prüfen, wenn auf der Seite etwas auffällt.
- **Nicht bestätigte Regeln:** digitec `.yVsAy41`, Tutti/anibis `xtxbqo`/`1x1x4w3`/`6ka9oi`, gmx `.regbanner`, stern `paid-toaster`. Bleiben vorerst drin.
