# Lesweek 4

(Op 16/10/2017)
(Geen les op 9/10 lesweek 3 geen les)


Event gestuurde programma's! 

Je geeft dat aan via een notification query. En je zegt hou dat in de gaten. De wmi service zal ook het consumerprogramma via het os alle communicatie verrichten. Dat systeem is niet voor alle wmi klasses gerealiseerd. Er is soms nood aan een extra mechanisme. Als wmi merkt da de faciliteiten niet aangeboden wordt dan komt er iets tussen (?)

Het intern mechanisme is wa langdradiger en minder performant maar langs consumer kant is er geen probleem.


Query van vorige keer:

```
select * from __InstanceCreationEvent within 1
where
TargetInstance ISA 'Win32_Process'
and (TargetInstance.Name='calc.exe'
or TargetInstance.Name='notepad.exe')

```

Probeer zo precies mogelijk aan te geven, anders fataal voor u systeem.
**__ExtrensicEvent** daar moet je zoeken voor op den test voor u events. Spijtig genoeg vind je er wel nie veel. Ook alles van register vind je daar. (RegistryEvent). Voor labos en testen die je op lokaal toestel kan uitvoeren is het allemaal wa beperkt.

Indien niet dan moede kijken naar **__InstanceOperationEvent** en dan daar kijken naar de afgeleide klassen.

Twee aanpassingen:
(vorige versie was gebasseerd op ``__Event > __ExtrensicEvent > Win32_SystemTrace > Win32_ProcessTrace en dan beide stop en starttrace``)
- ook interesse in het verwijderen (want hier enkel creation) > instance operation event
- en dan die CLASS (hier zijn we geinteresseerd in creation/deletion)

```
select * from __InstanceOperationEvent within 1
where
(__CLASS ='__InstanceCreationEvent'
or __CLASS ='__InstanceDeletionEvent')
and
TargetInstance ISA 'Win32_Process'
and (TargetInstance.Name='calc.exe'
or TargetInstance.Name='notepad.exe')

```

en dan via wbemtest > async > notification query
nog uitbreiding met group by


```
select * from __InstanceOperationEvent within 1
where
(__CLASS ='__InstanceCreationEvent'
or __CLASS ='__InstanceDeletionEvent')
and
TargetInstance ISA 'Win32_Process'
and (TargetInstance.Name='calc.exe'
or TargetInstance.Name='notepad.exe')
GROUP BY TargetInstance.name within 10
```


```
select * from __InstanceOperationEvent within 1
where
(__CLASS ='__InstanceCreationEvent'
or __CLASS ='__InstanceDeletionEvent')
and
TargetInstance ISA 'Win32_Process'
and (TargetInstance.Name='calc.exe'
or TargetInstance.Name='notepad.exe')
GROUP BY __CLASS, TargetInstance.name within 10
```

Vanaf da je group by gebruikt krijg je aggregate objecten, als je er op klikt (of in den browser) dan zie je da ihj twee dingen heeft, representative en number of events.

**having clausule**


```
select * from __InstanceOperationEvent within 1
where
(__CLASS ='__InstanceCreationEvent'
or __CLASS ='__InstanceDeletionEvent')
and
TargetInstance ISA 'Win32_Process'
and (TargetInstance.Name='calc.exe'
or TargetInstance.Name='notepad.exe')
GROUP BY __CLASS, TargetInstance.name within 5
having numberofevents>=5
```

Attribuut moet dus gemeenschappelijk zitten 


Wij: in perl zeggen "ik ben geinteresseerd in die query" op da ogenblik word je wakker en moet u programma code bevatten die actie moet ondernemen.

Microsoft heeft ook configureerbare consumerprogramma's die vrij elementair zijn.
Maar als je weet dat ze bestaan en je weet hoe ze werken dan kan je die gebruiken. 


Oracle service -> goe te doen daarin: Da consumerprogramma ook in CIM repo. 
Telkens wmi opstart dat de wmi service in die repo ga kijken, zijn er zo event objecten/consumerprogramma geregistreerd. Met als voordeel dat je ze maar 1 keer moet registreren en dat da dan altijd werkt. Da wordt automatisch opnieuw opgestart, zolang het erin blijft zal da werken. En hierdoor vergeet je als systeembeheerder dat je da gedaan hebt die correctie.

Die permanente registratie is een drie stapsprocess
3 objecten aanmaken die in cim repo komen

Eerste object da je moet maken is **filterobject** (in welke situatie ben ik geinteresseerd)
En dat is een kindobject van **__EventFilter**

Query

```
select * from __InstanceOperationEvent within 1
where
TargetInstance ISA 'Win32_Process'
and TargetInstance.Name='calc.exe'
```

EERST NIEUWE INSTANTIE VAN DIE KLASSE VRAGEN EN DAN DIE WIJZIGEN niet in de huidige

Eerste knop in den studio

dan zie je van boven staan new instance of ...
3 attributen:
```
Name:f
QueryLanguage: WQL
Query: select * from __InstanceOperationEvent within 1 where TargetInstance ISA 'Win32_Process' and TargetInstance.Name='calc.exe'

```
druk dan op save
nu gebeurt er nog niets eigenlijk

Nu moet ge het path overnemen (copy in studio, programmatisch moe je da ook doen je moet da dus opvragen eh!) **__PATH**

en eventueel naam van de computer vervangen door een .
``\\pcnaam\\ROOT`` naar ``\\.\\ROOT``

Wmi zal maar reageren als er reactie is en da moe je configuren.

En da moe in de tak: **__EventConsumer** daar kun je da dus registreren.
(minder interessant: nteventlogeventconsumer, logfileeventconsumer)

Meer algemene is de **CommandLineEventConsumer**: als er iets gebeurt ga ik net alsof ik op cmd ben, ga ik reactie uitvoeren op de cmd

bvb hoe kan je process kille da je nie aan staat: via taskkill

```
# bvb start calc
tlist (lijst van processen)
# procesid is 720
taskkill /F /PID 720 
# f van force
```



In **__InstanceCreationEvent** zit TargetInstance
en in **__Win32Process** is de pid **Handle**
zie ook **__CommandLineEventConsumer** en daar nieuwe instantie maken:

```
CommandLineTemplate: taskkill /F /PID %targetinstance.handle%
Name: c1
```

En saven -> object is gemaakt

PATH weer kopieren

We hebben nu twee objecten maar ze zijn nog nie gelinkt
```
\\.\ROOT\CIMV2:__EventFilter.Name="f"
\\.\ROOT\CIMV2:CommandLineEventConsumer.Name="c1"

```
Nu hebben we associator klasse nodig
en er is een klasse die een schuine pijl heeft (associator klasse) dus twee pointers bevatten naar objecten die moeten gelinkt worden naar mekaar. 

Zie **__FilterToConsumerBinding** daar heb je twee sleutels nl. consumer en filter

Van zodra 1 van de drie nie bestaat zal het mechanisme niet werken. 

Dus je gaat die paden gaan invullen in nieuwe instance

```
Filter:\\.\ROOT\CIMV2:__EventFilter.Name="f"
Consumer:\\.\ROOT\CIMV2:CommandLineEventConsumer.Name="c1"

```

vanaf nu is het geregistreerd als je nu calc opstart ga hem sebiet erna weer afsluiten

Nen anderen interessante is **SMTPEventConsumer** -> stelt u in staat om u mail te sturen


Nieuwe instantie van SMTPEventConsumer:

```
# zeggen van wie er een komt en naar wie
FromLine:joris.moreau@ugent.be
ToLine:joris.moreau@ugent.be
SMTPServer: smtp.ugent.be
Subject:%targetinstance.name%
Message:%targetinstance.handle%
Name:c2
```

save en path kopieren: ``\\.\ROOT\CIMV2:SMTPEventConsumer.Name="c2"``

Maar er moet wel een koppeling zijn!

Nieuw koppelingsobject filtertoconsumebinding

zelfde filder: f en tweede consumer c2
saven 

overzicht van instanties geeft er dan 5
wil je verwijderen vraag instanties en verwijder dan daar


Den beste is **ActiveScriptEventConsumer**

Oef: Meerdere calcs -> de laatste moe de voorlaatste killen maar zelf actief blijven!

Ik moet periodiek in de gaten houden, opkuis/controle

zie **__EventGenerator** -> absolute <> interval (periodiek wekkerke)

New instance van IntervelTimerInstruction:

TEST/EXAMEN: In een opgave **periodiek** -> tis da hier!!!!

```
IntervalBetweenEvents (in ms) : 2000
TimerId: k2
```

save
nu wordt er om de twee seconden een nieuw event gemaakt
zie -> event -> **__TimerEvent** bij instanties zie je da wel nie staan!
Je gaat da nooit zien staan!!
Bij eventgenerator ding daar wel en ook, daar om te verwijderen

Wa je wel kan zeggen is: ik ga een wql querie schrijven die iets genereert als er een klokje afloopt.
Maak mij wakker als consumer van zodra er een timerevent gemaakt wordt.

Klassieke wmi query:

```
select * from __TimerEvent # dan wordt je door gelijk welk klokje wakker gemaakt
```


```
select * from __TimerEvent
where timerid='k2'
```




## Active Directory

Beveiliging staat cruciaal. De toegang tot objecten wordt zo nauw mogelijk vastgelegd. 

Tijdens u inloggen krijg je een verzameling van id's en da is u toegangsticket. 
In windows moet je eerst een handle openen naar dat object. 
Da vraagt de accessrechten op die aan da object zijn gekoppeld en die maakt een controle wie gij zijt en tot welke groep jij behoort en de accessrechten van da object;

En dan w besloot of meneer x operaties y mag doen of niet. 
Da gebeurt allemaal tijdens het openen van de handle dat da beslist w. 
U toegangsticket is dus bij inloggen en dan bij handle en dan ligt het vast wa je me da object mag doen.

Da wil zeggen dat als admin iets wijzigt zolang je de handle hebt ondervind je niets, pas als je dat loslaat (uitlogt) en derlijke is da actief. 


**Workgroup principe** -> als ge gebruiker zijt en je zet op toestel een bepaalt ww en je gaat u op ander machine ook al die dingen instellen dan zal er synchronisatie zijn. Dus je bent zelf verantwoordelijk en da is dus eigenlijk waardeloos. 

**Domein** verzameling met domaincontrollers. Als je ingelogt bent op DC dan kan je gebruik maken van alle objecten. Meestal meerdere DC's voor loadbalancing en dergelijke. Als je er maar 1 hebt en die ligt eruit dan kan niemand inloggen. Bij een gescheiden groep aan admins dan heb je gescheiden domeinen meestal.

Ze zijn strikt gescheiden zolang er geen overeenkomsten zijn. III en ugent domeinen zijn gescheiden. 

Het is wel mogelijk om meerdere domeinen te hebben -> forest met elk domein een boom


Voor 2000 was het idee: we gaan bijhouden in het register. 
Register is binair bestand -> SAM 

(veel beperkingen)
Trager bijn veel gebruikers en dergelijke. Bij grootschalige bedrijven was da nie super

En dan is men overgestapt naar active directory (AD)



AD is een opslagmechanisme om items zoals toegangticket. "AD is the place to be"

De manier waarop is OO. Beetje gelijkaardig aan wmi.
je gaat ook objecten hebben. Ze vormen boomstructuur. 


AD w ter beschikking gesteld aan iedereen die wil (ook niet microsoft dingen) da was vroger daar nie het geval. Echt idioot!

### Directory access protocol

Iets te ingewikkeld. Meer dan noodzakelijk

client -> directory user agent (bvb)

Het feit dat DAP moest draaien op OSI protocol, toen was tcp nog zo "big" niet. 

Veel te zwaar en een aantal dingen konden po verschillende manieren en niet compatibel met tcp suite

-> fail

Unief in Michigan heeft dan dit verhaal herbeken;
DAP heeft voordelen maar we gaan alle balast over boord gooien en TCP gebruiken 
en het resultaat is ldap
en da was nie meteen een succes (versie 3 fzo) en dan zijn commerciele bedrijven daar gebruik van beginnen maken.

Novel (tegenhanger van Microsoft) is daar gebruik van beginnen maken.

Microsoft heeft bovenop die LDAP programmatie die COM laag toegevoegd voor makkelijkere programmatie. 
En ge moet u dan enkel bezig houden met da COM object;
En dan gaat da vertaald worden naar LDAP taal. 


