UWR Game Hardware
=================

Inhalt
======
1. [Bestandteile][]
1. [Peripherie][]
1. [Basishupe][]
1. [PC / Tablet][]
1. [Ausfallsicherheit][]
1. [Sonstiges][]


Bestandteile
=================
[Bestandteile]: #bestandteile

Die Hupanlage besteht im Wesentlichen aus drei Komponenten:

* Peripherie: nicht programmierbare Hardware wie Drücker, Hupe, Batterie, Anzeigen.
* Die Basishupe, die die Hardware steuert und die wichtigsten Hupenfunktionen übernehmen kann.
* Einer PC/Tablett Erweiterung, die aus der Hupe einen automatischen Protokolltisch macht.


Peripherie
=================
[Peripherie]: #peripherie

Die "dumme" Hardware könnte man erstmal von der Bamberger Hupanlage übernehmen und die entsprechenden Pins von dem Parallelport-Kabel abgreifen.
Optimal wäre es, wenn zum Testen ein Parallelbetrieb möglich wäre.

* 3 Drücker. Kabel bis zu 50m lang.
* 3 oder mehr Hupen (Signalhörner), angesteuert über ein Relais (1 ÜW, mind. 2 UW).
* 12V (Auto)Batterie.
* Ersatzteile für alles.

Optionale Peripherie
--------------------
Weitere Anzeigen stellen Informationen für die Mannschaften und die Zuschauer bereit.
Die Zuschauer-Anzeigen können irgendwo im Bad stehen.

* Indikator, ob die Zeit gerade läuft (z.B. rote & grüne LED).
* Anzeige für Spielzeit und Spielstand am Protokolltisch.
* Anzeigen für Strafzeiten an der Strafbank (3 LEDs, kabelgebunden?)
* Anzeigen für Mannschaftsnamen.
* Anzeigen für Zuschauer (Infos: Mannschaften, Zeit, Tore; angebunden per Funk/Eth)
* Anzeige für Verspätung zum Spielplan.
* (LED) Laufband für alles mögliche, z.B. Mannschaftsnamen, letzter Protokolleintrag.
* Anzeige des Spielplans.


Basishupe (Arduino)
=================
[Basishupe]: #basishupe

Die Basis der Hupanlage bildet ein Mirkocontroller, z.B. ein Arduino Uno, der die wichtigsten Funktionen der Hupanlage übernehmen kann.
Falls ein PC angeschlossen ist, reduzieren sich die Aufgaben des Arduino auf die Steuerung der Hardware.
Sollte der PC ausfallen, kann der Arduino übernehmen und das Spiel beendet werden.

Anforderungen an die Basishupe
------------------------------
* stromsparend
* ausfallsicher
* relativ einfach benutzbar

Konfiguration
-------------
* Spielzeit pro Halbzeit [15 m]
* Anzahl Halbzeiten [2]
* Dauer einer Auszeit [60 s]
* Dauer eines Strafwurfs [45 s]
* Dauer einer Strafzeit [120 s]
* Verlängerung [ja]

Die Konfiguration lässt sich per PC oder über den Arduino direkt einstellen.
Die Standard Liga Konfiguration ist eingespeichert und lässt sich schnell laden.

Falls ein PC angeschlossen ist, wird dessen Konfiguration benutzt.
Eine manuelle Konfiguration am Arduino ist dann nicht möglich.

Nach dem Einschalten sucht der Arduino den PC.
Falls einer verbunden ist bekommt er die Konfiguration vom PC und sperrt die Möglichkeit der manuellen Eingabe.
Ansonsten geht er in Setup, oder lädt die letzte Konfiguration, oder lädt die Liga Konfiguration.

Hinweis: Der PC lässt sich nur verwenden, wenn er beim Starten des Arduino schon an und mit diesem verbunden war.

Hardware
--------
* Arduino
* Mehrzeiliges LCD Display
* Stromversorgung von Batterie, nicht über USB.

Anzeige am Gerät
----------------
* Restspielzeit oder Restpausezeit oder Auszeit oder Strafwurfzeit [5-stellig].
* Indikator, was gerade angezeigt wird.
* Anzeigen für die jeweils älteste Strafzeit pro Mannschaft.
* Im Setup: Strings, Zeiten [3-stellig], Bool [Ja/Nein oder J/N]

Tasten
------
* Drehrad
* Taste für Reset
* Taste "Setup" (evtl. ident. mit Reset)
* Enter / OK
* Zurück / Abbrechen / Exit
* Zeit start/stop
* Auszeit
* Strafzeit w, Strafzeit b
* Strafwurf
* (?) Tor w, Tor b

Funktionen
----------
* Drückersignalerkennung (Anhupen/Abhupen oder Anhupen/Tor/Foul)
* Hupensteuerung (über Relais)
* Spielzeit stoppen
* Auszeiten stoppen
* Strafzeiten stoppen
* Strafwurf stoppen

### Spielzeit stoppen ###
Arduino stoppt die Zeit.  
PC stoppt die Zeit auch (vermutlich genauer) und schickt seine Zeit an den Arduino (mit dem aktuellen Zustand, s.u.).
Der Arduino korrigiert dann seine Abweichung (übernimmt die PC Zeit).
Falls der PC ausfällt, läuft der Arduino ohne weitere Korrekturen einfach weiter.


PC / Tablet
=================
[PC / Tablet]: #pc-tablet

Hardware
--------
Optimal wäre ein Lösung, die auf einem Tablet oder einem PC/Laptop laufen kann.  
Bediengung entsprechend über Touchscreen oder Tastatur und Maus.

Protokoll-Backup
----------------
Der PC speichert das gesamte Protokoll regelmäßig. Außerdem schickt er den aktuellen Zustand an den Arduino, so dass im Falle eines Ausfalls auf die wichtigsten Daten zugegriffen werden kann. Das schicken kann regelmäßig erfolgen, oder bei jedem Protokoll-Event.

### Zustand von PC an Arduino ###
* Spielzeit
* Spielstand

Events von Arduino an PC
------------------------
Nur Hupsignale: Anhupen, Foul und Tor. Oder: Anhupen und Abhupen.

Funktionen am PC
----------------
(TODO: noch unvollständig)
Siehe dazu auch UWRGameLogic Git

* Elektronisches Protokoll
* Verspätung zum Spielplan errechnen
* aktueller Protokolleintrag steht zur Verfuegung (z.B. per WLAN oder ETH)
* Unterschrift wie bei Post, per Stift
* LED-Laufband, das aktuelle Daten anzeigt (Mannschaften, Spielstand und Zeit, Protokolleintrag)
* Mehrere Anzeigen per WLAN oder Netzwerk ansteckbar (s.o., "Optionale Hardware")


Ausfallsicherheit
=================
[Ausfallsicherheit]: #ausfallsicherheit

Die Funktionalität des Arduino ist bewusst klein gehalten, um ihn möglichst ausfallsicher zu machen.
Seine Funktionen sind aber ausreichend, um ein Spiel komplett zu steuern.
Wir nehmen an, dass er genauso zuverlässig ist wie existierende Hupanlagen.

Falls der PC ausfällt, kann das Protokoll auf Papier weitergeführt und das Spiel zu Ende gebracht werden.
Das Protokoll bis zum Ausfall ist noch auf dem PC gespeichert und kann im Nachhinein mit dem Papierprotokoll zusammengeführt werden.


Sonstiges / Offene Fragen
=========
[Sonstiges]: #sonstiges

* Mehrsprachigkeit?
* Umsetzung der Drücker (Active-Low Port?, Reed-Kontakte?)
* Welche Stecker
* Was passiert bei Einschalten ohne PC? (Hinweis + Setup/letzte/default Konfig)
