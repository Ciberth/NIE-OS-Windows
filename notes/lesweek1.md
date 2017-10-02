# Lesweek 1 

(Op 25/09/2017)

## Inleiding

30% theorie (8 weken)
Focus ligt op labo's: 40% WMI (onafh van theorie) & 30% LDAP

Oefeningen WMI zijn in oktober al ten einde -> 17 november oefeningexamen hierover.

3e oefeningweek -> na-apen van theorie
maar 4, 5, 6e week dat zijn cruciale oefeningen want week erna is den test !!

(Daarna kan je windows wat uitstellen (?))

## Intermezzo (Windows versies)

In weze is er maar 1 windows kernel (NT).

Ingewikkelde naamgeving -> interne nummering van NT was nog vrij consistent.
Windows 10 + Server 2016 -> 6.4 
Maar om dwaze/commerciele redenen hebben ze da nu 10.0 genoemd. Onterecht!
In labo's Windows Server 2012 R2 (2014)

(Zal enkel verslechten, Microsoft focust zich nu meer op Cloud Computing/Azure/...)


## WMI

Eerder ~ netwerkgerelateerd (verlengde)
(Zie nog smnp)

### WBEM

Van microsoft/hp/solaris voor beheer

wbem -> spreekt men uit als webm

~> WMI bij Windows 
(HP Unix en Unix versies van Sun (Solaris))

Zelfde concept zul je terugvinden bij de anderen

### WMI versus SNMP

Basis snmp concept (polling) blijft met een aantal veranderen.

"Als we dingen opvragen gaan we da doen op een OO-manier". We gaan de agent informatie laten bijhouden in de vorm van objecten. 

Wijzigingen kon bij SNMP, gaat ook bij WMI maar is niet de gangbare zaak. 
Het idee is dat ze procedures hebben en dat je die kan oproepen.
Je vraagt dus aan een agent om een object op te vragen en daar de procedure opvragen met alle systeemaanroepen van dien. Grote vooruitgang tov snmp.


!STOKPAARDJE: Vooral het agent-getriggerede gebeuren is beter ondersteund! Niet vanuit de agent! (Ik ben een fan)


1 Masteragent (deze doet beroep op) meerdere subagents

De beheerapplicatie heeft geen weet van de subagents, enige aanspreekpunt is de masteragent.

* WMI Consumer -> de beheerder van een applicatie die alles in handen heeft
* CIM Object Manager (Common Information Model) noemen we nu WMIservice (enige die voor de buitenwereld zichtbaar is) = masteragent
* Subagents (snmp) noemen we Providers (wmi)
* CIM Repository == de databank waar alles in staat (attribuut van een klasse) + wordt ook nog gebruikt voor de structuurverzameling van de klasses/attributen in op te slaan (de bits die we nodig hadden bij snmp) omdat we OO werken. Beetje equivalent van mibfiles. 

De WMI objecten zitten niet in de CIM repo, met een paar uitzonderingen. Er zitten enkel de klasses en attributen enzo in.

Eventobjecten zijn de enige objecten die in de repository zitten.


### CIM Studio

-> allowed blocked content
-> connect to namespace -----> root\CIMV2 (bijna voor alles is dit voldoende)
--> geen probleem om met 2 tegelijk te connecteren!
-> Login scherm (ok) (zelfde als waarmee je bent ingelogt op u machien)



Er zijn 100'n wmi klasses en de beslissing is genomen om die in namespaces (verzameling van tabellen) onder te verdelen.
"Ge moet u connecteren met een namespace en dan heb je alles hier onder controle"

De onderverdeling is op basis van hun overerving-eigenschappen (ookal hebbe ze soms niets me mekaar te maken)


2 prefixen: CIM_ en WIN32_

Cim is nog van den tijd van HP/Microsoft/Sun er mee gestart zijn. In gebruik maakt da geen verschil.

Elke file wordt gezien/gerepresenteerd als een wmi-object -> Datafile.


3 Tabbladen (Properties & Methods & Associations)

Register -> maar 1 object -> theeft methodes voor de sleutels.

Ge weet dus eigenlijk niet a priori hoe het geprogrammeerd is


## WMI Qualifiers

4 Soorten:

- Klassequalifiers (objectqualifiers): eigenschappen van de klasse zelf
- Attribuutqualifiers
- Methodequalifiers
- Methodeparameterqualifiers: de parameters waarmee je methode oproept zitten apart

Die qualifiers zijn het begin van een WMI-script.

Je vraagt aan je repository wa je allemaal moet hebben


### Klassequalifiers

Bvb: Win32_LocalTime

-> rechtermuisknop buiten alles > object qualifiers en dan krijg je overzicht

* Provider - WMI service moe weten welke agent (subagent) het is -> systeemaanroep in principe
* Description - Vraagteken bovenaan! eerste stuk is de description / al de rest is description van de attributen (die eronder liggen)
* Dynamic - als het true is kunnen er dynamisch klasses gemaakt worden
* !!Singleton - smeerlapke - als da op true staat wil da zeggen dat er van die klasse maar 1 object kan zijn! Da wil nie zeggen da er 1 is da zegt enkel er KAN er maar 1 zijn. 
* Abstract - Kleurke van ding lings abstract true - wit (geen instanties). Verschil tss bleekgrijs en donkergrijs: Bleekgrijs heeft kinderen die instanties kunnen hebben. Donkergrijs: wel instanties
* Association: zie later



De link is afhankelijk van het feit of het een singleton is of niet :/ spijtig!

De onderste attributen zijn - systeemattributen (komen altijd overal voor) -> ambetant voor de programmeur.

Het is makkelijker om attributen op te vragen dan systeemattributen. Consistente (moeilijkere) manier van programmer!

Systeemattributen hebben dubbele underscore __ 

RELPATH is nen belangrijke -> naam van de klasse is ook het relatieve path 

Server + namespace + relpath moe je aan mekaar plakken en dan krijg je PATH (wa je als url moet gebruiken)!

ALS het singleton is dan moe je vragen via instanties -> je kan maar 1 hebben
je vraagt details van -> je kijkt naar relpath of path en er staat ``=@``. Zo herken je de singleton!

Ander vb is win32operatingsystem

Bij de andere moet er ipv de @ een opsomming komen van sleutelattributen en de waarde.

Win32_Processor --> geen singleton

Geef mij alle instanties -> is er 1 en je vraagt info en je krijgt daar ook PATH en je kijkt naar naamgeving dan ziet het er zo uit ``.DeviceID="CPU0"`` Dus een punt en dan sleutelattribuut en de waarde!


!!! Op test: vraagt zowel singleton als gewoon object in als addertje onder het gras!!!


### Attribuutqualifiers

Sleutelattributen worden bijgehouden op attribuutniveau (helaas) ipv klasseniveau. 

Zie Win32_ip4routetable

Elke instantie zal 1 route zijn!

Sleutelke rechtermuisknop op de lijn property qualifiers. Daar zie je dan staan da het sleutelattributen zijn. 

4 koppels hier via . en dan komma's bvb:

.Destination:"..",InterfaceIndex=12,...


Populair object is Win32_Thread --> 2 sleutelattributen

ThreadState-> 2 & 5 maar wa betekent da? --> Geen documentatie :/ hoe weet je da nu in de repository

Da wordt bijgehouden als qualifier van het attribuut

Dus kijk naar property qualifiers van ThreadState --> Values (type:array) en da geeft u een opeenvolging van strings. Begint vanaf 0 dus 2 --> is hier Running meeste stonden op 5 --> Waiting


Dus heeft hij een Values qualifier dan moet ge da mappen op een array (perl tabel)


Ander vb Win32_LogicalDisk: DriveType weer kijken naar qualifiers van drivetype kijken, ander vb MediaType


(kdenk processor)
Architecture --> Heeft 9 en in Values zijn er zelf geen 9 Maar ge hebt wel ValueMap
en das dan nen hash in perl. Dus in ValueMap krijgde de keys voor de values van Values

Nog zo'n voorbeeld is bij NetworkAdapter is NetConnectionStatus=7 en Availability=3

Weer koppel ValueMap en Values

Sommige icoontjes hebben zo een pen erdoor -> Wil zeggen da het writeable is

Een ander belangrijk attribuut is CIMTYPE geven u de syntax van de specifiek attribuut


### Methodequalifiers

Description
CYMTYPE -> gaat over de returnwaarde. Het type die dan ook weer gemapt kan worden via valuemap/values
ValueMap
Values


Vb bij Win32_OperatingSystem kies Win32_Operatingsystem
Hier zie je bvb ook privileges staan (tis nie omda je het kan opvragen da je ook de handeling mag/kan uitvoeren). Alles is zichtbaar / er is wel een dremper om het uit te voeren. 

Ge krijgt privileges bij inloggen


Calculator nek omwringen >

Win32_Process -> calculator zoeken > calc.exe 
Methods > Terminate > Rechtermuisknop execute method

En je krijgt als ReturnValue 0 -> method qualifiers > ValueMap/Values


Elke methode wordt afgelaten op een instantie (da mag je nie denken) je hebt ook methodes die je moet loslaten op een klasse!! Nie verkeerd redeneren.

Er zijn methodes (static methodes) die je nooit op een instantie moet loslaten maar op de klasse zelf. 

Static bij qualifiers (boolean) en dan heb je nog de parameterqualifiers die daarbij komen niet hier maar dus apart.


Register --> StdRegProv > geen enkel attribuut (enkel systeemattribuut) -> allemaal methodes
-> nie anders dan alles int register opvragen enzo

Dus maar 1 object ipv alles in register -> allemaal objecten

Hierdoor kan je dus op andere machines ook alles opvragen/aanpassen

Al die methodes rechtstreeks toepasbaar op de klasses -> hierdoor is alles static

methodes die je louter toepast op de klasse zoals die in de repository zitten
