<!-- #!/usr/bin/env markdown
-*- coding: utf-8 -*-
region header
Copyright Torben Sickert 16.12.2012

License
-------

This library written by Torben Sickert stand under a creative commons naming
3.0 unported license. See https://creativecommons.org/licenses/by/3.0/deed.de
endregion -->

<!--|deDE:Projekt Status-->
Project status
--------------

[![npm version](https://badge.fury.io/js/archinstall.svg)](https://www.npmjs.com/package/archinstall)
[![downloads](https://img.shields.io/npm/dy/archinstall.svg)](https://www.npmjs.com/package/archinstall)
[![build status](https://travis-ci.org/thaibault/archinstall.svg?branch=master)](https://travis-ci.org/thaibault/archinstall)
[![dependencies](https://img.shields.io/david/thaibault/archinstall.svg)](https://david-dm.org/thaibault/archinstall)
[![development dependencies](https://img.shields.io/david/dev/thaibault/archinstall.svg)](https://david-dm.org/thaibault/archinstall?type=dev)
[![peer dependencies](https://img.shields.io/david/peer/thaibault/archinstall.svg)](https://david-dm.org/thaibault/archinstall?type=peer)
[![documentation website](https://img.shields.io/website-up-down-green-red/https/torben.website/archinstall.svg?label=documentation-website)](https://torben.website/ai)

<!--|deDE:Einsatz-->
Use case
--------

This script provides a full unattended way to install ArchLinux from any live
environment. You can create your own linux distribution based on the rolling
released ArchLinux. A rock solid decorator pattern allows to automate a fully
unattended individual installation process.

<!--|deDE:Inhalt-->
Content
-------

<!--Place for automatic generated table of contents.-->
[TOC]

<!--|deDE:Einstieg-->
Quick-Start
-----------

Simply load the newest version from:
<!--deDE:
    Um die neuste Version zu erhalten sollte man das Bash-Script runterladen:
-->
[archinstall](https://torben.website/archinstall/data/distributionBundle/archinstall.sh)

```bash
>>> wget https://goo.gl/YbxT3F -O archinstall.sh && chmod +x archinstall.sh

```

oder ohne Link-Shortener:

```bash
>>> wget \
        https://torben.website/archinstall/data/distributionBundle/archinstall.sh && \
        chmod +x archinstall.sh
```

archinstall ist das zentrale Modul um eine Reihe von Aufgaben im Zuge der
Vorbereitung, Installation, Konfiguration oder Manipulieren eines
"Linux from Scratch" durchzuführen.

Das Modul kann im einfachsten Fall (z.B. von einer beliebigen
Live-Linux-Umgebung) gestartet werden. Zu beachten ist, dass die Installation
von einem UEFI-System empfohlen wird, da dadurch entsprechende Boot-Einträge
automatisch gesetzt werden können. Ein Bootloader wird nicht mit installiert
und ist nicht notwendig, um das System zu booten.

```bash
>>> ./archinstall.sh
```

In diesem Fall werden alle benötigten Informationen zur Einrichtung
(nur Hostname) des Systems vor Beginn des Installations Prozesses interaktiv
abgefragt. Zu Beachten ist: ohne zusätzliche Parameter gibt das Modul keinen
Feedback über den aktuellen Zustand des Installations Vorgangs. Mit:

```bash
>>> ./archinstall.sh --verbose
```

bekommt man einen etwas geschwätzigeren Installationsvorgang.
Mit:

```bash
>>> ./archinstall.sh --verbose --debug
```

werden alle Ausgaben jeglicher verwendeten Subprogramme mit ausgegeben.

Alle Parameter wie Installations haben Standardwerte. So wird im obigen Fall
einfach in das aktuelle Verzeichnis ein Ordner mit dem Programmnamen erstellt
und darin das Betriebssystem installiert (./archinstall/). ill man lieber eine
unbeaufsichtigte Installation:

```bash
>>> ./archinstall.sh --host-name testSystem --auto-partitioning
```

<!--|deDE:Installation auf Blockgeräte-->
Install to blockdevice
----------------------

Im typischen Fall will man von einer Live-CD booten um das System auf einer
Festplatte oder Partition zu installieren. Hierbei müssen folgende Aufgaben
erfüllt werden.

* Einrichtung einer Internet Verbindung (siehe auch Abschnitt "Offline Installation")
* Partitionierung der Ziel Festplatte
* Formatierung der Ziel Partitionen
* Aufbauen der Dateisystemstruktur
* Konfiguration des Betriebssystems
* Installation und Einrichtung eines Boot-Loaders

Alle Aufgaben bis auf die Einrichtung der Internet Verbindung (wird in der
Regel von der Host Umgebung geregelt) könne mit archinstall automatisiert
durchgeführt werden. Will man z.B auf das Block Device "/dev/sdb" installieren
und sich nicht selber um die Partitionierung kümmern und den kompletten
verfügbaren Platz für das Haupt System verwenden (es soll also keine Swap- oder
Daten Partition erstellt werden).
sieht das z.B. so aus:

```bash
>>> ./archinstall.sh --output-system /dev/sdb --auto-partitioning
```

Auf diese Weise wird eine uefi Boot-Partition mit 512 MegaByte eingerichtet.
Der restliche Platz wird für die Systempartition eingesetzt. Sind noch weitere
Partitionen gewünscht kann man diese während der Installation durch weglassen
des entsprechenden Parameters selber konfigurieren. Die erste Partition wird
dann als Boot-Partition und die Zweite als Systempartition betrachtet. Weitere
Partitionen werden ignoriert. Manuelle Partitionierung:

```bash
>>> ./archinstall.sh --output-system /dev/sdb
```

An dieser Stelle sei noch erwähnt, dass archinstall alle erstellten Partition
automatisch mit Labels in der Partitionstabelle und auf der Partition selbst
versieht. Um dieses Verhalten zu individualisieren einfach folgende Optionen
nutzen:

```bash
>>> ./archinstall.sh --boot-partition-label uefiBoot \
    --system-partition-label system
```

<!--|deDE:Installation auf eine Partition-->
Install to partition
--------------------

Um z.B. aus einem Produktivsystem heraus eine alternative Linux Distribution
auf eine weitere Partition zu installieren kann einfach folgender Befehl
verwendet werden:

```bash
>>> ./archinstall.sh --output-system /dev/sdb2
```

Hier wird auf die zweite Partition des zweiten Block Devices installiert.
archinstall versucht bei der Installation auf ein Blockdevice stets einen
entsprechenden Uefi-Boot-Eintrag für den Kernel mit einem Standard Initramfs
und einem Ausweich-Initiramfs zu konfigurieren. Die folgenden Parameter
definieren dessen Label:

```bash
>>> ./archinstall.sh --output-system /dev/sdb2 --boot-entry-label archLinux \
    --fallback-boot-entry-label archLinuxFallback
```

<!--|deDE:Installation in Ordner-->
Install to folder
-----------------

Um archinstall für komplexere Szenarien zu verwenden oder nachträgliche
Manipulationen vorzunehmen ist es sinnvoll zunächst in einen Ordner zu
installieren. Siehe hierzu "archinstall with Decorator Pattern",
"makeXBMCLinux", "makeRamOnlyLinux" oder "makeSquashLinux" bzw. das Projekt
"archinstallWrapperTemplate".

Dieser Befehl installiert ein vollständiges System in den eigenen Home-Ordner
"test" (siehe auch Installation ohne root Rechte).

```bash
>>> ./archinstall.sh --output-system ~/test
```

<!--|deDE:Automatische Konfiguration-->
Automatic configuration
-----------------------

archinstall konfiguriert das neu eingerichtete System vollautomatisch.
Folgende Tasks wurden automatisiert:

* Tastaturlayout einstellen
* Einrichten der richtigen Zeit Zone
* Setzen des Hostnames
* Setzen des default root Passworts nach "root"
* Erstellen eines Benutzers bzw. Benutzerordner. Das Passwort wird initial wie
  der Name des Benutzers gesetzt.
* dhcp Dienst für alle Netzwerk-Interfaces einrichten (siehe automatisches
  Einrichten von Diensten)
* Installation der Basis Programme (siehe automatische Installation von
  Programmen)
* Einrichten der Signaturen für den Paketmanager "Pacman", um vertrauenswürdige
  Pakete erhalten zu können.
* Einrichten aller in der nähe liegenden Server um schnelle Packet Updates und
  Paketinstallationen zu gewährleisten.
* Einrichten der richtigen Paketquellen abhängig von der aktuellen CPU
  Architektur.

Will man hierauf selber Einfluss nehmen, gibt es folgende Möglichkeiten:

```bash
>>> ./archinstall.sh --host-name test --user-names test \
    --cpu-architecture x86_64 --local-time /Europe/London \
    --keyboard-layout de-latin1 --country-with-mirrors Germany \
    --prevent-using-pacstrap --additional-packages python vim \
    --needed-services sshd dhcpcd apache
```

Um die einzelnen Konfigurationsparameter zu verstehen empfiehlt sich ein Blick
auf:

```bash
>>> ./archinstall.sh --help
```

zu werfen.

<!--|deDE:Dekorator Muster-->
Decorator Pattern
-----------------

Um eigene Betriebssystem Modul zu entwerfen bietet archinstall eine
Vielzahl von Schnittstellen um seine internen Mechanismen separate nach Außen
zugänglich zu machen (siehe hierzu die Decorator Implementierung
"installXBMCLinux", "makeRamOnlyLinux", "makeSquashLinux" oder
"installArchLinxDecoratorTemplate") und unsere Guidelines zum Erstellen eines
Wrappers.

Im einfachsten Fall würde der Code der archinstall sinnvoll erweitert so
aussehen:

    #!/usr/bin/env bash
    # -*- coding: utf-8 -*-

    # Program description...

    source "$(dirname "$(readlink --canonicalize "$0")")"archinstall.sh \
        --load-environment

    # Do your own stuff cli logic here..
    # All your functions and variables are separated from the archinstall
    # scope.

    # Call the main Function of archinstall and overwrite or add
    # additional command line options.
    archinstall "$@" --output-system initramfsTargetLocation

    # Prepare result ...

Beachte, dass trotz des laden von archinstall auf diese Weise keine Konflikte
zwischen dem Wrapper-Scope und dem archinstall-Scope entstehen können. Die
einzige globale Variable ist "archinstall" selbst.

Will man nun von den internen Features von archinstall partizipieren geht
das so:

```bash
>>> source archinstall.sh --load-environment
```

Jetzt haben wir den gesamten Scope auch im Decorator zur Verfügung. Alle
Methoden sind mit dem Prefix "archinstall" ausgestattet, um Namens Konflikte
und versehentlich überschreiben von Methoden zu vermeiden. Will man sich also
einen Überblick über alle verfügbaren Methoden machen, einfach in der shell
folgendes eintippen:

```bash
>>> source archinstall.sh --load-environment

>>> archinstall<TAB><TAB>
...
```

Siehe hierzu auch "archinstall API".

<!--|deDE:Applikations-Interface-->
Application Interface
---------------------

Viele nützlich Umgebungsvariablen und Funktionen können mit

```bash
>>> source archinstall.sh --load-environment
```

geladen werden. Um eine Übersicht zu erhalten sollte man sie die
API-Dokumentation anschauen.

<!--|deDE:Optionen-->
Options
-------

archinstall stellt ein Alphabet voller Optionen zur Verfügung. Während bisher
zum einfachen Verständnis immer sog. Long-Options verwendet wurden, gibt es für
jede Option auch einen Shortcut.

```bash
>>> ./archinstall.sh --user-names mustermann --host-name lfs
```

ist äquivalent zu:

```bash
>>> ./archinstall.sh -u mustermann -n lfs
```

Alle Optionen bis auf "--host-name" und "--auto-partitioning" haben
Standardwerte. Diese beiden werden sofern nicht von vorne herein angegeben
interaktiv abgefragt. Alle Standardwert können mit Hilfe von:

```bash
>>> ./archinstall.sh -h
```

oder

```bash
>>> ./archinstall.sh --help
```

oder

```bash
>>> ./archinstall.sh --keyboard-layout de-latin1 -h
```

eingesehen werden. Letzteres macht Sinn, da sich Standardwerte aufgrund schon
ermittelten Informationen verändern können. So wird der Standardwert von
"--key-map-configuration="KEYMAP=de-latin1\nFONT=Lat2-Terminus16\nFONT_MAP="
nach Eingabe von

```bash
>>> ./archinstall.sh --keyboard-layout us
```

zu: "--key-map-configuration="KEYMAP=us\nFONT=Lat2-Terminus16\nFONT_MAP=".

Der Standardwert von "--cpu-architecture" entspricht beispielsweise immer der
Architektur des aktuellen Systems, um Konfigurationsaufwand zu minimieren.

Man kann Optionen die mehrere Werte annehmen auch mehrfach referenzieren.
So hat:

```bash
>>> ./archinstall.sh --additional-packages ssh --additional-packages vim -f python
```

den gleichen Effekt wie:

```bash
>>> ./archinstall.sh --additional-packages ssh vim python
```

Dies ist im Decorator-Pattern nützlich. Bei einem Doppelt referenzierten Wert
überschreiben spätere Werte zuvor Definierte. Folgendes:

```bash
>>> ./archinstall.sh --host-name A --host-name B
```

entspricht:

```bash
>>> ./archinstall.sh --host-name B
```

Auf diese Weise kann man getrost folgendes tun:

    #!/usr/bin/env bash

    source archinstall.sh

    myTarget='/path/to/expected/result'

    archinstall "$@" --output-system $myTarget

    # Working with result in "$myTarget"

Selbst wenn der Wert von "--output-system" über die CLI gesetzt wurde ist sie
im Wrapper wieder überschrieben. Auf diese weise kann man exklusiven Zugriff
auf Parameter im Wrapper vornehmen.

<!--|deDE:Offline Installieren-->
Install offline
---------------

archinstall erstellt bei jeder Installation automatisch einen Paket-Cache, um
weitere Installationen zu beschleunigen. Ist dieser einmal erstellt oder wird
dieser zusammen mit dem archinstall (z.b. auf einem usb-stick) ausgeliefert
kann Offline installiert werden.

Selbst wenn mit einem bereits vorhandenem pacstrap installiert wird, wird
dieser temporär kopiert, gepatched und anschließend offlinefähig ausgeführt!

Bei Offline Installation müssen natürlich alle zusätzlich ausgewählten
Pakete im Package Cache vorhanden sein. Ist dies nicht der Fall wird
archinstall versuchen diese nach zu laden und im Offline-Fall einen Fehler
zurückgeben.

<!--|deDE:Installieren ohne root Rechte-->
Install without having root permissions
---------------------------------------

Prinzipiell ist es sogar möglich auch ohne root Rechte ein System aufzusetzen.
Hierbei werden jedoch folgende Einschnitte gemacht:

* Die Programm "fakeroot" und "fakechroot" müssen zusätzlich installiert sein.
* Ein bereits installiertes Pacstrap kann nicht eingesetzt werden
* Zusätzlich erstellten Benutzer kann während der Installation kein automatisch
  erstellter Home-Ordner geliefert werden, da die Rechte oder root nicht
  richtig gesetzt werden könnten.
* Es kann nur in "Ordner" (bzw. siehe nächsten Punkt) installiert werden.
* Das System wird in ein tar-Archiv ohne Speicherung entsprechender Datei
  Rechte Attribute gepackt.
* Das Tar Archiv muss als "root" entpackt werden bevor das Ergebnis verwendet
  oder fehlerfrei gebootet werden kann.

<!--|deDE:Nützliche Tipps und Fehlerbehebung-->
Useful tips and debugging informations
--------------------------------------

Während der Entwicklung haben sich eine Reihe von Optionen bewährt um Fehler
bei der Entwicklung von Wrappern zu finden.

Die Option "--prevent-using-pacstrap" oder "-p" verhindert ein bereits
installierten Pacman für die Installation zu verwenden. Dies ist notwendig wenn
man sein Pacman so konfiguriert hat, das z.B. Pakete wie der Kernel oder Pacman
von manipulierten User Repositories abhängen. Mit "--prevent-using-pacstrap"
wird eine neue Version von Pacman in einer Change-Root-Umgebung ausgeführt.

"--prevent-using-native-arch-chroot" oder "-y" ist sinnvoll wenn man Indexing
Dienste wie Ubuntu's "Zeitgeist" oder "Dropbox" verwendet, die das Unmounten
von Mountpoints während der Installation verhindern, da sie auf diesen noch
lesen/schreiben.

Installiert man von einer Live-CD auf ein Block Device bootet das System nach
erfolgreicher Installation automatisch in das neu generierte System. Will man
noch etwas nachbessern oder Überprüfen, bietet sich die selbsterklärende Option
"--no-reboot" bzw. "-r" an.

Möchte man die Pakete "base-devel", "sudo" und "python" haben, geht das mit
dem Shortcut: "--install-common-additional-packages" oder "-z".

Will man eine beliebige Liste von Paketen integrieren:

```bash
>>> ./archinstall.sh --additional-packages ssh python2 vim
```

Sollen Dienste schon beim ersten Start automatisch gestartet werden:

```bash
>>> ./archinstall.sh --needed-services sshd dhcpcd
```

Um die Installation zu beschleunigen kann auf ein zentral verwalteten
Paket Cache verwiesen werden:

```bash
>>> ./archinstall.sh --package-cache-path /var/cache/pacman/pkg/
```

<!-- region vim modline
vim: set tabstop=4 shiftwidth=4 expandtab:
vim: foldmethod=marker foldmarker=region,endregion:
endregion -->
