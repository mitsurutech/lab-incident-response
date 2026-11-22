# Lab zu Incident Response und Forensik: einen kompromittierten Linux-Server untersuchen

[English](README.md) | [Français](README.fr.md) | [Español](README.es.md) | [日本語](README.ja.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-incident-response/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-incident-response/actions/workflows/check.yml)

Ein fertiges **Lab zu Incident Response und digitaler Forensik** für den Unterricht, von der [MitsuruTech Learning Platform](https://mitsurutech.co.uk/de/learning), auf der Schulen und Colleges allen Lernenden eine eigene Maschine und ein Terminal im Browser geben. Alle Lernenden erhalten eine eigene Ubuntu-VM mit den **Logs und Dateien eines kompromittierten Webservers**: ein SSH-Log mit einem Brute-Force-Angriff, ein Zugriffslog des Webservers voller Scanner, die Kontendatei, Crontabs und eine Liste der Änderungszeiten von Dateien. Sie finden **die IP-Adresse des Angreifers, das Konto, in das eingebrochen wurde, den Zeitpunkt der ersten Anmeldung und die Persistenz, die der Eindringling hinterlassen hat**, und schreiben ihre Ergebnisse in `answers.txt`. Auf dem Rechner der Lernenden wird nichts installiert.

Es gibt **keine Schadsoftware und keinen Exploit-Code**. Alles ist Text und wird mit den Werkzeugen gelesen, die jede Linux-Maschine hat: `grep`, `awk`, `sort`, `uniq`, `cut`, `less`. Der Fall enthält absichtlich Ablenkungen: Die Adresse mit den meisten fehlgeschlagenen Anmeldungen kommt nie hinein, ein Administrator vertippt sich beim Passwort und hat dann Erfolg, und ein legitimer Benutzer und ein alter SSH-Schlüssel stehen neben den Änderungen des Eindringlings. Ein bequemes `grep` löst den Fall also nicht, ein sorgfältiges Lesen der Zeitleiste schon.

## Was die Lernenden tun

1. Die README lesen, die das Lab im Home-Ordner hinterlässt: Sie nennt die Beweismittel und was zu finden ist, und sie verrät keine der Antworten.
2. `case/var/log/auth.log` und `case/var/log/nginx/access.log` durcharbeiten, um den Angriff vom Rauschen zu unterscheiden und die Adresse und das Konto zu finden.
3. Den Zeitpunkt der ersten erfolgreichen Anmeldung von dieser Adresse ermitteln.
4. Die Crontabs, die Konten, die Schlüssel und `case/mtimes.txt` ansehen, um herauszufinden, was der Eindringling hinterlassen hat, in welcher Datei es liegt und welches Programm es am Laufen hält.
5. Die sechs beschrifteten Zeilen in `answers.txt` schreiben.

## Wie bewertet wird

Das Lab hat eine **Prüfung**, ein Skript, das eine abgegebene VM bewertet, ohne dass die Lehrkraft sie lesen muss: Es liest `answers.txt` (Groß- und Kleinschreibung und Leerzeichen spielen keine Rolle) und vergleicht jede Antwort mit dem Fall. Dabei nennt es das **erste falsche Feld und sagt nie, wie die Antwort lautet**. Es weist außerdem einen Fall zurück, dessen Beweisdateien so verändert wurden, dass sie zu einer Antwort passen. Der Fall ist für alle Lernenden derselbe und wird beim ersten Start der VM aus einem festen Seed erzeugt.

Die Lernenden haben `sudo` auf ihrer eigenen VM. Wer die Dateien von root liest, kann also den Lösungsschlüssel finden. Das wird in Kauf genommen: Das Lab dient der Übung, und bewertet wird die schriftliche Antwort.

## Was in der Datei steht

`incident-response.lab.yaml` ist eine vollständige **Definition eines VM-Labs**, genau das, was die Plattform ausführt:

| Schlüssel | Was er ist |
| --- | --- |
| `cpus`, `memory`, `disk` | die Größe der VM jeder lernenden Person: 1 CPU, 1024 MB und 10 GB |
| `open` | die Ports, die im Browser geöffnet werden: keine, das Lab ist das Terminal |
| `setup` | ein Skript, das einmal als root beim ersten Start der VM jeder lernenden Person läuft: Es erzeugt den Fall, schreibt die README und eine leere `answers.txt` und installiert Python aus dem Ubuntu-Archiv, nur wenn das Image es nicht enthält |
| `check` | das Bewertungsskript, das als root auf einer Kopie der gespeicherten Festplatte nach einem frischen Start läuft; sein Exit-Status ist das Ergebnis, und `0` bedeutet bestanden |

## Auf der Plattform verwenden

**Um es einer Klasse als Aufgabe zu stellen:**

1. Fügen Sie in Ihrem Modul eine Aufgabe hinzu und wählen Sie die Karte **Ubuntu-VM-Lab**.
2. Fügen Sie den Inhalt von `incident-response.lab.yaml` ein, oder klicken Sie auf das Beispiel, wenn Ihre Plattform es aufführt. Ein Beispiel ersetzt den Text, und „Rückgängig“ bringt Ihren zurück.
3. Veröffentlichen Sie und probieren Sie zuerst Ihre eigene Kopie aus. Alle Lernenden erhalten eine eigene VM, die zwischen den Sitzungen erhalten bleibt.
4. Weisen Sie sie zu wie jede Aufgabe. Wenn sie abgegeben ist, führen Sie die Prüfung in der Zeile einer lernenden Person in der Klassenliste aus: Sie startet eine Kopie der gespeicherten Maschine, die nicht zurückgespeichert wird, und zeigt, ob sie bestanden hat und was sie ausgegeben hat.

Die [Seite zu diesem Lab](https://mitsurutech.co.uk/de/help/lab-incident-response) im Hilfehandbuch, sein [Leitfaden zu Ubuntu-VM-Labs](https://mitsurutech.co.uk/de/help/labs) und seine [Seite mit Beispiel-Labs](https://mitsurutech.co.uk/de/help/sample-labs) erklären den Rest. Um einen eigenen Fall zu erstellen, ändern Sie den Generator im `setup`: den Seed, die Adressen, das Konto und die Dateien, die er schreibt.

Noch kein Konto für Ihr College? [Ihr erstes Jahr ist kostenlos](https://mitsurutech.co.uk/de/pricing) für bis zu 50 Lernende, und für die [Registrierung](https://mitsurutech.co.uk/de/signup) genügt eine E-Mail-Adresse.

## Die Datei prüfen

Bei jedem Push prüft `.github/workflows/check.yml`, dass die Datei gültiges YAML mit den fünf Schlüsseln oben ist, dass `setup` und `check` jeweils innerhalb der Grenze der Plattform von 16 KB liegen und dass das Bewertungsskript gültiges Python ist. Es startet keine VM: Das geschieht auf den eigenen Maschinen der Plattform.

## Was in einem Lab nicht funktioniert

Die VM erreicht das Paketarchiv von Ubuntu, PyPI, npm und GitHub und sonst nichts, ein Lab kann also nicht auf einen Host im Internet gerichtet werden. Es ist eine einzige VM mit einem Satz Logs zum Lesen: Es gibt keinen echten Angreifer zu beobachten und keine zweite Maschine anzugreifen.

## Über uns

Erstellt von [Mitsuru Technologies](https://mitsurutech.co.uk/de) für die [MitsuruTech Learning Platform](https://mitsurutech.co.uk/de/learning): Python, Java, Go, Node, SQL und Linux im Browser für Schulen und Colleges, mit Container-Labs, VM-Labs, aus GitHub bereitgestellten Web-Apps und Quizzen, die sich selbst bewerten. Probieren Sie Code ohne Konto im [Playground](https://mitsurutech.co.uk/de/playground) aus.

Weitere Labs vom selben Ort: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) und [lab-sample](https://github.com/mitsurutech/lab-sample).

## Lizenz

Dieses Werk steht unter der Lizenz [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Sie dürfen es zum Lernen und Lehren weitergeben und bearbeiten, wenn Sie Mitsuru Technologies Ltd nennen, es nicht kommerziell nutzen und alles, was Sie daraus machen, unter derselben Lizenz weitergeben. Der vollständige Text steht in der Datei `LICENSE`.

Jede Adresse in dem Fall stammt aus einem für Dokumentation reservierten oder einem privaten Adressbereich, und nichts darin sind echte Daten.
