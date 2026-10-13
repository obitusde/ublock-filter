# ublock-filter

Persönliche uBlock-Origin-Filterliste ("Mobile News Reading") mit statischen Kosmetikfiltern für mobiles Lesen: Auf News- und Kleinanzeigen-Seiten (20min.ch, SRF, heise, golem, tagesschau, zeit.de, digitec, tutti/anibis u. a.) bleibt im Wesentlichen nur der Artikelinhalt übrig – ohne Header/Footer, Empfehlungs-, Werbe- und Newsletter-Boxen oder generische Stockfotos.

## Abonnieren

In uBlock Origin: *Dashboard → Filterlisten → Eigene → URL(s) importieren* und diese URL eintragen:

```
https://raw.githubusercontent.com/obitusde/ublock-filter/main/ublock-statische-filter.txt
```

uBlock prüft die Liste täglich (`! Expires: 1 day`); sofort aktualisieren geht über *Jetzt aktualisieren*.

## Neue Regeln hinzufügen

1. **Am Handy picken:** Auf der Seite im uBlock-Menü den Element-Picker (Pipette) öffnen, das störende Element antippen, *Erstellen*. Die Regel landet in *Meine Filter* und wirkt sofort.
2. **An Claude geben:** Irgendwann (gern gesammelt) unter *Dashboard → Meine Filter* die neuen Zeilen kopieren und in einer Claude-Code-Session in diesem Repo einfügen:
   ```
   Neue Regeln vom Handy:
   tagesanzeiger.ch##.newsletter-box
   ```
   Ein kurzer Satz dazu, was weg soll, hilft. Ohne Picker geht auch eine Beschreibung („auf nzz.ch die Newsletter-Box unter dem Artikel“) – Claude sucht die Regel dann selbst.
3. **Claude prüft und pusht:** Trefferzahl und Isolation werden live geprüft, die Regel wird nach Domain einsortiert, die Version (`! Version:`) hochgezählt, committet und gepusht.
4. **Am Handy aufräumen:** Die übernommenen Zeilen in *Meine Filter* löschen und *Änderungen übernehmen*. Dann unter *Filterlisten* bei „Mobile News Reading“ *Jetzt aktualisieren* tippen. Kontrolle: Die angezeigte Version entspricht der in der Datei.

## Regel entfernen oder korrigieren

Wenn eine Regel zu viel wegnimmt (z. B. Artikeltext fehlt): Seite und Beobachtung an Claude geben. Die Regel wird angepasst oder entfernt, gepusht, danach am Handy *Jetzt aktualisieren*. Schnelle Notlösung bis dahin: uBlock auf der Seite kurz deaktivieren (großer Power-Button).

## Gut zu wissen

- Diese Datei ist die einzige gültige Quelle. Nicht direkt in uBlock in der Liste ändern – Abo-Listen werden bei jedem Update komplett ersetzt.
- *Meine Filter* ist nur der Zwischenspeicher für neu gepickte Regeln. Der Import dort hängt nur an und ersetzt nichts.
- Prüfregeln und Seitenwissen für Claude stehen in [`CLAUDE.md`](CLAUDE.md).
