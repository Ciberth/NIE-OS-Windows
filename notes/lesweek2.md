# Lesweek 2

(Op 2/10/2017)
(Geen les op 9/10)


## Methodeparameterqualifiers (parameters van de methodes)

* out, optional en ID zijn specifiek voor deze qualifiers
* ID - bepaalt de volgorde waarin ze opgeroepen moeten worden
* out - mijn parameter is geen invoerparameter maar een uitvoerparameter (meestal pointer naar gegevenstructuur doorgeven, zodat er extra info kan meegegeven worden), vaak in scripts wat meer werk. Pointer naar tabel is vaak voldoende. 
* optional - Parameters die niet meegegeven moeten worden 

### Vb WMI klasse (StdRegProv)

Heeft methodes bvb **EnumKey**. Daar rechts op klikken > Edit Method Parameters - dit is een overzicht van klassequalifiers. Hier kan je weer rechts klikken en op parameter qualifiers klikken!

sNames is bvb een uitvoerparameter (heeft out op true)

### Vb Win32_Volume

**Defrag** - parameters zijn Force en DefragAnalysis(pijltje naar links wil zeggen uitvoerparameter)


Bij COM-programmatie programmeer je niet rechtstreeks op het object. Je maakt een object die het object representeert (in je locale ruimte). Er is een afscherming. De protocollen (LDAP, WMI) zelf zijn niet van de simpelste en die afscherming zorgt er dus voor dat je niet aan netwerkprogrammatie moet doen. Dit heeft voor- en nadelen. 


**Format** - een van de infoerparameters is Version (welke ntfs versie) > parameter qualifiers - Values zie je maar er is weer een ValuesMap en Values. Je zoekt op in de Values wat je wil en dan zoek je de sleutel in de hash en dat moet je dan meegeven -> meest recente versie is bvb 592 (key). 0 is vaak default waarde. 


### Vb Win32_Process

**Create** - array Values zijn terugkeerwaarden. Maar wat bvb interessant is, is het process id. Dat is iets typisch wat je zal moeten meegeven met uitvoerparamter (ProcessId) da zal nen pointer zijn naar een scalaire waarde!

!! Zeker een deel van de vraagstelling voor de complexiteit op de test!

### Vb Win32_Share

vb van optional

**Create** - Path geef je emee - MaximumAllowed heeft optional (vaak eerste x verplicht en laatste y optional)

### Vb Win32_Directory

**CompressEx** - deze methode uitvoeren op bepaald path > edit method par > recursive heeft ook optional (komt erop neer moet ik submappen ook comprimeren of niet)


## Associatorklassen

Wrm hebben we da nodig - een veel voorkomend fenomeen is ik heb bvb netwerkinterface met ip en mac adres
``ipconfig /all`` -> eth3 heeft mac en IP.

"Ik zou die informatie via wmi willen bekomen". Hoe is u toestel daar aan gekomen via ipconfig -(ook via wmi)

Als je browst door klasses zoek je naar iets met netwerk -> **Win32_NetworkAdapter**

In attributen zie je onder meer **MACAddress** de naam eth3 zal staan in **NetConnectionID** dus in principe moet je op zoek gaan naar instantie waar de Netconnectionid eth3 is. 

Lijst van alle interfaces met in de kolom de naam van de attributen en dan zie je bij netconnectionID eth3 en dan kan je daar MACAddress vinden. Wat er **niet** in staat zijn **ip-addressen**.

Merk op dit heeft device.ID gekregen van 22.


Zie ook **Win32_NetworkAdapterConfiguration** staan (dit heeft niets te maken met hierboven). Totaal andere klassen en merkwaardig genoeg evenveel instanties en met een index die evenveel id's heeft als daarnet maar dit is eerder **toeval!** Ze beschrijven andere attributen vanhetzelfde fysieke. Dus neem je id 22 dan krijg je informatie terug die je niet in de networkadapter-object terug vond. Hier vind je items uit hogere protocol lagen komen. IP addressen vind je (array). 

Dus ipconfig doe nie anders dan overzicht geven van deze twee objecten.


Hoe gebeurt het linken nu tussen twee objecten (want in wmi zit het er vol van). Wel niet echt zoals hierboven met een sleutel. Men heeft in wmi gekozen om standaard iets anders te doen. Er is geen enkele verplichting. 

Hoe doe je het dan wel? Wel zie databanken (doorsneetabellen voor m-n relaties). In principe gebruikt men dus associate objecten. 

De icoontjes met pijl van rechts boven naar linksonder - die hebben enkel als doel om te verwijzen naar verschillende of gelijke klasses. 


Welke klasse moe ik nu vinden en daarvoor dient de derde tab - associations. 

Zie bvb bij Win32_NetworkAdapterConfiguration -> Associations. 

Dan zie je via figuurke hoe da gelinkt is.

Als je hovert zie je **Win32_NetworkAdapterSetting** als je daar op klikt dan ga je doorverwezen worden en focus verleggen naar da koppelingsobject en als je nu properties bekijkt zie je da van da koppelingsobject. 

2 verwijzingen (pointers) naar klasses die aan mekaar gekoppeld moeten worden. Typisch een binaire relatie. Er is geen enkele reden om hogere orde relaties te leggen. 

"Geef keer alle klasses die meer dan 2 verwijzingen te hebben" - test vraag en er bleek er maar 1 te zijn (die verwijst naar 3 objecten). 


Bij het maken van associaterklasse is het object waarnaar je verwijst wel da ligt vast.

"Het is een verwijzing naar dezelfde of een andere klasse". Typisch bvb subdirectory. 


**Win32_protocolbinding** die heeft drie verwijzingen (enige waar we weet van hebben). Al de andere zijn binaire relaties. 

Ook extra attributen bij de associatie da heb je ook weinig wel bij **Win32_DependentService**. TypeOfDependency heeft nen values typisch relatie leggen tussen twee services. Merkwaardig genoeg hebben de meeste da niet.


Elke file/bestand daar zijn 2 wmi-objecten aan gekoppelt. Hoe kan je nu 1 aan zijn nekvel pakken.

cim - dus microsoft heeft er geen extra dingen aan gehangen. Geen verfijning gekomen.
**CIM_DataFile** - zodanig veel objecten dus nie in u hoofd halen da op te vragen.

Gelukkig is het voor sommige bestanden zinvol geweest om er meer om die te laten representeren door een meer specifiekere klasse. Dus microsoft heeft toch kindklasses gemaakt bvb **Win32_NTEventlogFile**. Daar kunnen we het ons veroorloven om alle instanties op te vragen. 

Dan kan je er ook 1 willekeurige van nemen. Bvb Ossession (office session)
Met wat is da geassocieert - zie associations tab. Hij heeft daar blijkbaar wel wa werk mee. In wmi kan je de interne databank zelf nie wijzigen in ldap wel! (Indexen leggen bvb). En dan krijg je dat het gekoppeld is met 6 andere objecten. 

Die vier binnenste da zijn vier effectieve events in da object. Dus elke entry (event) in die log is ook een wmi object!! Overkill! Da maakt da systeem eigenlijk minder/niet bruikbaar. Koppeling is bvb **Win32_NTLogEventLog** (associaterklasse) die ga twee attrbiuten hebben en de naam kan je ook weten door te hoveren over de pijlkes (record en log)

Den onderste is security setting van da bestand - **Win32_SecuritySettingOfLogicialFile**. 
De bovenste is **CIM_DirecotryContainsFile** want da bestand zit altijd in een map (via GroupComponent en PartComponent). Als je nu dubbel klikt op die directory zie je alle associaties vanuit het standpunt van die map. Dus allemaal files die erin zitten. En hij zit zelf ook in een map (voorbeeld van recursief). Dus je ziet de oudermap (winevt). Klik je daarop dan zie je "die map heeft twee kindobjecten" en zijn ouder en security settings. Klik je op system32 dan heb je veel kindmappen en veel files en 1 oudermap (ben partcomponent van die map). Dan c drive map juist zelfde. Met daar dan ook device waarop je gekoppeld bent. Alles hangt aan mekaar.


Heb je veel instanties dan moet je proberen filteren (want anders teveel instanties).

**Win32_NetworkAdapter** men heeft daar beetje een SQL-syntax voor gebruikt. Je hebt zoiets als SELECT FROM WHERE om attributen te filteren en daar stopt het bij. 


Select + Associatie - je kan dit doen via voorlaatste icoontje (opvragingen doen in de db). 

WQL Query!

```
select * from [naam klasse] 
where [attribuut]='[value]'
```

Vb

```
select * from Win32_NetworkAdapter 
```

Nu krijg je alle instanties van die klasses


```
select * from Win32_NetworkAdapter 
where NetConnectionID='eth3'
```

Nu krijg je enkel die 1e instantie. Like en % is hetzelfde uit sql. 
**Enkel zoektochten in enkelvoudige tabellen!!**

Hoe doe je zo een navigatie nu programmatisch.

dus Cim_dataf opzoeken > en NTEventlogFile > alle instanties > 1 vastpakken (objectsessies)

Als je een object wil vinden waarmee een object verbonden is dan moet je vertrekken van het relatieve path vertrekken. Dus ``__RELPATH``


```
ASSOCIATORS OF {[Volledige relatieve path van het object]}
```



```
ASSOCIATORS OF {Win32_NTEventlogFile.Name="C:\\Windows\\System32\\Winevt\\Logs\\OSession.evtx"}
```


**Merk op** dat de backslash gebackslashed is door de dubbele aanhalingstekens. Hier zou je enkele kunnen gebruiken en niet backslashen maar er zijn verschillen. Bij nen gewone select boeit nie en bij die associators boeit da wel dus consistent blijven!

Je hebt nu 6 objecten. 


```
ASSOCIATORS OF {Win32_NTEventlogFile.Name="C:\\Windows\\System32\\Winevt\\Logs\\OSession.evtx"}
where
RESULTCLASS = Win32_Directory
```

**Merk op** die string waar je mee wil vergelijken **MAG NIE** via quotes!!

Dat is dan het enige object van het type Win32_Directory

```
ASSOCIATORS OF {Win32_NTEventlogFile.Name="C:\\Windows\\System32\\Winevt\\Logs"}
```

```
ASSOCIATORS OF {Win32_NTEventlogFile.Name="C:\\Windows\\System32\\Winevt"}
where
RESULTCLASS = Win32_Directory
```

Stel nu group- en/of partcomponent filter in de where clausule -> dankzij resultrole


```
ASSOCIATORS OF {Win32_NTEventlogFile.Name="C:\\Windows\\System32\\Winevt"}
where
RESULTCLASS = Win32_Directory
and
RESULTROLE = GroupComponent
```

Merk op da is een syntaxfout -> ge moogt zelf die and niet opgeven -.-

**Group**
```
ASSOCIATORS OF {Win32_NTEventlogFile.Name="C:\\Windows\\System32\\Winevt"}
where
RESULTCLASS = Win32_Directory
RESULTROLE = GroupComponent
```

**Part**
```
ASSOCIATORS OF {Win32_NTEventlogFile.Name="C:\\Windows\\System32\\Winevt"}
where
RESULTCLASS = Win32_Directory
and
RESULTROLE = PartComponent
```

Role gebruik je als je naar de bron wil verwijzen!

```
ASSOCIATORS OF {Win32_NTEventlogFile.Name="C:\\Windows\\System32\\Winevt"}
where
RESULTCLASS = Win32_Directory
and
ROLE = PartComponent
```

Men kan niet naar qualifiers verwijzen helaas! Probeer zoveel mogelijk te filteren op de wmi kant met wql en niet programmatisch!



## Interessantste van WMI (auto getrigger van de client)

Consumerprogramma die moet aangeven waar heb je interesse in (**notification query**). 

Het op de hoogte stellen van, gebeurt via een object. En da gebeurt in de repository. Pas als er iemand interesse heeft. U event object is dus enkel zichtbaar voor den dienen met interesse. De service zegt ook maak de consumer wakker. En hij kan da object opvragen en aan de attributen zien wa er gebeurt is. Zo ist echt van "vanaf ik wakker word weet ik da er iets gebeurd is". Zie laatste labo (vraag op test!)


**wbemtest oproepen via win + r** 

"Je mag niet op semisyncr drukken maar kies Async"!!

``__SystemClass > __IndiciationRelated > __Event > ExtrinsicEvent > Win32_DeviceChangeEvent > Win32_VolumeChangeEvent``


```
select * from Win32_VolumeChangeEvent

```

Usb inpluggen
Enkel da programma ziet da den browser niet. EventType 2 (arrival) en andere attributen
Plug je uit dan krijg je weer een entry.

Je kan ook verfijnen 


```
select * from Win32_VolumeChangeEvent
where
EventType = 2
```

Het uittrekken niet - het insteken wel 


Win32_SystemTrace zijn goede voor den test! Win32_ProcessStartTrace (en stop)

```
select * from Win32_ProcessStartTrace
where
ProcessName = 'notepad.exe'
or
ProcessName = 'calc.exe'

```

Notificiation query starten 
adobe reader niets gebeurt, windows word weer niets
notepad en ja notification
calculator starten en weer notifications

Zowel start en stop zijn andere klasses dus eig 2 consumerprogrammas
Je hebt dus interesse in ProcessStart en ProcessStop en gelukkig zijn da beide kinders van dezelfde ouderklasse! Je krijgt alle eventobjcten van kindklasses. Je mag wel alleen gemeenschappelijke attributen nemen! Zolang attributen gemeenschappelijk zijn dan kun je die naam gebruiken als filter.

```
select * from Win32_ProcessTrace
where
ProcessName = 'notepad.exe'
or
ProcessName = 'calc.exe'
```



Ander voorbeeld is RegistryEvent (heeft KeyChangeEvent, TreeChangeEvent, ValueChangeEvent)







EventProviders zijn niet altijd voorzien (zelf nie door microsoft zelf)

Er is een middenweg tss niet pollen en pollen.

Als geinteresseerde (gelijk wel object) consumer in eender wat (veranderingen). Voor de consumer verandert er niet veel. De WMI service gaat aan polling doen! Maar het consumerprogramma weet van niets en het gebeurt op de targetmachiene (niet ideaal maar dus toch al beter). De WMI service neemt de rol van poller over. Verandert er iets dan maakt hij een object en maakt hij de consumer wakker. 

Ge moet wel aan de wmi service vertellen om hoeveel tijd hij moet pollen. Afhankelijk van welk object je bekijkt moede korte of lange frequenties kiezen. Als je aan filesysteem vraagt wnr er bestanden bijkomen dan doe je da best nie om de seconde als je geen map specifieert. 

Nu da voorbeeld van notepad herschrijven in de veronderstelling dat er geen specifieke event dingen beschikbaar zijn. Wat moet je dan doen je moet opdracht geven aan provider. 
Provider gaat snapshots nemen om de zoveel tijd. 

En hij vergelijkt de snapshots en is er een verschil dan maak ik object en maak ik consumer wakker. 

**__InstanceOperationEvent** (onder **__Event**)

(zie later (volgende les) om enkel 3 dingen te bekijken ipv alles) 



Vb van notepad:

Object zal zijn van het type **__InstanceCreationEvent**

**merk op:** 
- ISA (ge zijt een instance van een bepaalde klasse)
- ProcessName noemt niet ProcessName in instanceCreationEvent maar wel Name (zie in win32_process)
- je moet vertellen om de hoeveel tijd hij moet pollen (**within**) in seconden

Doe je deze targetinstance verfijning niet dan ligt u systeem plat.

```
select * from __InstanceCreationEvent WITHIN 1
where
TargetInstance ISA 'WIN32_Process'
and
(
TargetInstance.Name = 'notepad.exe'
or
TargetInstance.Name = 'calc.exe'
)
```

En nu hebben we juist dezelfde query van daarnet!