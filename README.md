# Einführung in git und Github
Für das Zusammenarbeiten an und das Weiterentwickeln Code ist es überaus wichtig, immer eine *stabile*, d.h. lauffähige Version des Codes online zu haben und zu vermeiden, dass lokale Änderungen globale *überschreiben*. Weiterhin ist es teilweise nicht abzusehen, wie ein bestimmter Zustand von Codespäter noch gebraucht werden kann: Es kann sein, dass wir als Nutzer zu bestimmten, *alten Versionen* von Dateien zurück möchten. 
Hier sind drei Anforderungen an ein Protokoll, mit dem wir gerne zusammenarbeiten möchten: 
1. Die Unterhaltung einer stabilen Softwareversion zu **jeder Zeit**.
2. Das **sichere Einbringen** lokaler Änderungen in die stabile Softwareversion.
3. Die Möglichkeit, ausgewählte alte Versionen von Dateien **zurückzubekommen**. 

Zusammenarbeitstools wie GDrive scheitern auf mehrere Weisen an diesen drei Aufgaben. Wir schauen uns das Protokoll git und die Cloud-Plattform github an. Alternativen für das Protokoll sind mercurial und subversion, für die Cloud-Plattform sind gitlab und bitbucket Alternativen. Wir beginnen mit git, welches alle drei oben formulierten Anforderungen erfüllt - wenn man alleine arbeitet.
## git: Versionskontrolle offline
Git ist ein Open-Source-Protokoll zur Speicherung von Versionen. Es ermöglicht, verschiedene Versionen von Software gleichzeitig zu speichern und sogar Software-Anteile, die sich gerade in Entwicklung befinden, gesondert zu speichern. 
### Versionen speichern
Da dies noch reichlich unspezifisch ist, nehmen wir ein konkretes Beispiel: Wir wollen gerne ein Programm schreiben, welches verschiedene Zahlensysteme ineinander umwandelt, also etwa das Dezimal- in das Dualsystem. Wir entscheiden uns, unser Projekt in einem Ordner `Zahlenwandler` anzulegen. Bevor wir zu programmieren beginnen, öffnen wir die Kommandozeile und navigieren in den Ordner `Zahlenwandler`. Wir initialisieren wir die Git-Versionskontrolle
```
git init
```
Dies fügt einen unsichtbaren Ordner `.git` im Ordner `Zahlenwandler` hinzu. In diesem werden alle parallelen und verganenen Versionen gespeichert. Nun beginnen wir, unser Projekt zu starten. Wir fügen ein paar Dateien mit einer Minimalimplementierung von einem Wandler von Dezimal- zu Dualsystem hinzu, z.B. 
```cs
using System;

namespace Zahlenwandler
{
    class MainClass
    {

        public static void Main(string[] args)
        {
            ...
        }
        ...
    }
    ...
}
```
Da wir nun zufrieden sind und wir meinen, dass die Dateien in ihrem jetzigen Zustand erinnerungswürdig sind, speichern wir sie. Bevor wir für den Code eintreten, oder *commit*en, fügen wir erst die Dateien hinzu, die aktualisiert werden sollen, etwa
```
git add Program.cs
git add README.md
```
Es werden nicht automatisch alle Änderungen an Dateien hinzugefügt, da es sein kann, dass bestimmte Änderungen inhaltlich zusammengehören andere nicht (dies können wir uns in einem größeren Projekt besser vorstellen). Schauen wir einmal nach, was bisher passiert ist
```
git status
```
Er sollte anzeigen, dass drei Dateien hinzugefügt wurden. Nun sind wir bereit, die Speicherung tatsächlich vorzunehmen:
```
git commit -m "Initial commit"
```
Der String nach `-m`, `Initial commit` ist der *Name* des Commits - unter diesem kann er später wiedergefunden werden. Schreiben wir lediglich `git commit`, wird die Kommandozeile den Texteditor auf der Kommandozeile öffnen (an welchen ich mich gewöhnen musste; auch nach der Gewöhnung finde ich `-m` einfacher). Nach wiederholtem `git status` sollten jetzt nur noch Dateien angezeigt werden, die ihr im vorigen Schritt nicht hinzugefügt habt. Habt ihr alle Dateien hinzugefügt, sollte `working directory clean` angezeigt werden. Wenn ihr weiter entwickelt, könnt ihr mit `git add`, `git commit` immer wieder Schnappschüsse Eures Codes speichern. Nun ist jedoch die Frage wie wir Features, die noch nicht sehr sicher sind, einbinden, oder wie wir eine stabile Version überhaupt unterhalten.
### Lasst uns Experimentell werden
Hierfür gibt es in git sogenannte branches, Äste. Jeder Ast entspricht einem Entwicklungsstrang. Zu Beginn gibt es nur einen Branch, master. Dieser Branch ist der stabile Branch, auf dem "sicherer" Code liegen sollte. Ihr könnt Euch die Liste der derzeitigen Branches mit
```
git branch -l
```
anzeigen lassen. Wir wollen nun einen experimentellen Ast hinzufügen. Wie nennen ihn "experimental":
```
git branch experimental 
```
Dies erstellt einen Entwicklungsast "experimental" - wir sind aber immer noch auf "master". Wir wechseln auf einen anderen Branch, z.B. "experimental":
```
git checkout experimental
```
Nun können wir beliebige Änderungen einfügen, etwa erlauben, dass von beliebigen Zahlenformate transformiert wird. Dies fügen wir wie gewohnt mit `git add` und `git commit` hinzu. Nun haben wir zwei Softwareentwicklungsstränge: "master" und "experimental". Was passiert, wenn wir nach unseren Änderungen auf "master" zurückwechseln? Es hat sich nichts getan! master ist wie vor unseren Änderungen. Genauso ist es aber auch sinnvoll bei experimentellen Features: Der stabile Ast des Codes sollte in keiner Weise beeinflusst werden von den aberwitzigen Änderungen, die wir im Branch "experimental" machen. Sobald wir uns sicher sind, dass unser experimenteller Code sicher genug ist, können wir ihn zu master hinzufügen (und hier passieren die spannenden Dinge, denn hier kann es hässlich werden). Wir wechseln auf master 
```
git checkout master
```
und wir führen die Änderungen zusammen, wir *merg*en, von Englisch "to merge". 
```
git merge experimental
```
Dies fügt die Änderungen auf "experimental" dem master-Branch hinzu. Hier kann es zu einem sogenannten Merge-Conflict kommen: Wenn an beiden Branches an derselben Stelle Code weitergearbeitet wurde ist nicht klar, welche Änderungen behalten werden sollen. Dies zeigt der git-Client in den Dateien an. Diese Stellen müssen ausgeräumt werden, bevor der Merge zu Ende geführt werden kann. Wenn das aber klappt, ist ein neues Feature auf dem stabilen Branch und kann ausgeliefert, oder Englisch "deployed", werden. Dies ist alles, was in der Offline-Welt zu wissen ist, nun betreten wir Neuland: Das Internet. 
## Online-Kollaboration mit github. 
Hier ist es notwendig, dass ihr einen github.com-Account erstellt. Mit einem solchen Account könnt ihr ein neues "Repository" erstellen. Die Ordner, in denen .git-Ordner liegen (also solche wie `zahlenwandler`), nennt man auch *Repositorys*. Erstellen wir mit einem Klick ein neues Repository und stellen wir ein, dass es ein leeres sein soll. Nun würden wir gerne eine Online-Kopie unseres Repositories. Wir müssen github noch sagen, wo wir gerne unsere Online-Version hätten. Dies machen wir mit
```
git remote add origin https://github.com/bschulengg/intro_to_git
```
wobei https://github.com/bschulengg/intro_to_git ein Beispiel für die Internetadresse eines Online-Repositories ist. Mit dieser konkreten wird es jedoch nicht funktionieren, da dieses nicht leer ist - es enthält das README.md in dem dieser Satz steht. 
Nun drücken wir unsere Änderungen auf unser Repository:
```
git push
```
wobei URL die Webseite ist, die auf der Seite nach der Erstellung der Repositorys angezeigt wird. Nun können wir weiterhin `git add`, `git commit` und `git merge` benutzen und unsere Änderungen mit `git push` ins Internet bringen. Mit `git pull` werden die Änderungen aus dem Internet auf das eigene Repository übertragen.
Ein klassischer Ablauf von Befehlen ist also 
```
git add
git commit
git pull 
git push
```
Hier kommt `git pull` zuerst, da github (und andere Protokolle auch) nicht erlaubt, dass zuerst neue Änderungen hinzugefügt werden, bevor alte Änderungen heruntergeladen wurden. Dies stellt sicher, dass alle Dateien aktuell sind.
## Aller guten Dinge sind drei
Bevor es zu den Aufgaben geht, hier noch drei Anmerkungen:
### Ein Repository herunterladen, das bereits besteht
Falls ein Repository bereits auf github liegt (z.B. wenn ihr an einem anderen Rechner weiterprogrammieren wollt), dann wollt ihr es klonen. Hierfür könnt ihr auf der Github-Website bei "clone or download" einen Link, nennen wir ihn Linki, kopieren und das Repository lokal speichern mit
```
git clone Linki
```
### Wie merkt git, wenn ich eine Datei umbenenne oder bewege
Wenn ihr eine Date im Explorer bewegt, kann git sie nicht mehr speichern und denkt, dass die Datei gelöscht und eine neue Datei desselben Namens hinzugefügt wurde. Dies kann aber geändert werden, indem Konsolenbefehlen für das Umbenennen bzw. bewegen ein `git` vorangestellt wird. Würde man auf der Kommandozeile normalerweise `mv textx.txt text.txt` benutzen, um `textx.txt` in `text.txt` umzubenennen, so geht dies in git mit
```
git mv textx.txt text.txt
```
### Wie kriege ich jetzt aber eine alte Version meiner Datei?
Hier kann man ebenfalls `git checkout` benutzen. Zuerst schauen wir uns an, was bisher passiert ist
```
git log
```
Dies zeigt alle Commits mit Namen. Diese haben auch einen Hash-Wert, nennen wir ihn 9ccd1c313c5dd80d36c6d55eff341f7d7e27cce7, der hinter dem Wort "commit" angezeigt wird. Mit 
```
git checkout 9ccd1c313c5dd80d36c6d55eff341f7d7e27cce7
```
gehen wir zur Version des Commits 9ccd1c313c5dd80d36c6d55eff341f7d7e27cce7.
## Aufgaben
- Besuche die Seite https://github.com/bschulengg/intro_to_git und klicke oben rechts auf der Seite auf "Fork". Hierfür benötigst Du einen Github.com-Account
- Clone das Repository auf Deinen Rechner
- Ergänze eine Textdatei, in der Du kurz beschreibst, wie Du Dir den Moment vorstellst, in der Du Deine letzte Zeile Code produzierst (oder schreibe einen anderen, weniger gedankenschweren Text)
- adde die Datei
- Commite sie (in anderen Worten *bekenne Dich zur Änderung*)
- pushe Sie auf den Server.
- Füge einen neuen Ast mit Namen erstprogrammieren und bleibe auf ihm. 
- Erstelle eine Datei, in der Du Dein erstes Programmier-Erlebnis schilderst (oder einen anderen Text)
- Füge sie hinzu. 
- Commite Sie. 
- Pushe die Änderungen. Hierbei wirst Du gebeten, einen Upstream für diesen Branch zu benennen und Dir wird ein Befehl vorgeschlagen; benutze den vorgeschlagenen Befehl.
- Wechsle auf den Branch master. 
- Beobachte, dass auf dem Branch Master Deine Datei nicht ist. 
- Merge Deine Änderungen des Branchs erstprogrammieren in master. 

## Verweise
+ [Git Cheat Sheet](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf)
+ [TryGit-Tutorial](https://try.github.io/levels/1/challenges/1)