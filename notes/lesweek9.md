# Lesweek 9

(Op 20/11/2017)

Meerdere functionele niveaus. 
Het is maar als je een nieuw domein maakt dat je standaardwaarde voor functioneel niveau krijgt en da je moet beseffen ben ik tevreden of niet.

Vele vergeten dat en zien den helft van het schoon weer nietmeer. En da heeft louter te maken da u functioneel niveau nie goe ingesteld is.

Als je u functioneel te hoog insteld dan kun je niet lager gaan (dus als je oudere DC's hebt mag je da nie hoog zetten). Hoe hoger functioneel niveau hoe meer functionaliteit je hebt. 

Iets da werkt mag blijven werken dus je werkt soms wel met een oude DCs. Wij zijn conservatieve mensen.
Ook sommige netwerkcontrollers worden bvb niemeer ondersteunt op nieuwe software.

Bvb nu zijn de 2003 afgeschreven dus die zullen we weg doen maar de 2008s zullen er nog wel zijn. 


Een domein met bepaald functioneel niveau kan opgenomen worden als het forest functioneel niveau laag genoeg is. Merk op tis nie omda je nen 2008 hebt da je geen windows 10 kan bezigen. KUNNEN VERWOORDEN in de vragenlijst. 

Geef overzicht van de verschillende concepten van functioneel niveaus. Zie slides.

Vraag is niet om perfect te weten die eig zit in da functioneel niveau.
Vraag is elk nieuwe faciliteiten ongeveer te zeggen wat data betekent. 

Kapstokvraag; vertel over elk van die faciliteiten iets. 

Laagste: windows nt4 controllers (das niemeer opportuun), maar 2003 zal nog wel bestaan.
Als ge u functioneel niveau verhoogt naar windows 2000 dan krijg je wat faciliteiten (zie 1. windows 2000 native). Als ge in windows 2000 zit heb je in principe maar 1 node. Elk GC moet onderling recypleren. Heel mijn forest over al mijn domein dus 1 hebben is genoeg. 
Alle DCs kunnen zelfstandig SPN objecten maken; bedoeling is dat ze evenwaardig zijn (lezen en schrijven), voor sommige objecten is da een probleem (sommige controllers: securityID). Er is 1 controller die verzameling van sIDs zal beschikbaar stellen voor dcs. Maakt verzameling van 500 elementen en als da op is opnieuw. Da is dus iets nieuws in da functioneel niveau bij lagere zou je da nie hebben.

sIDHistory -> bij verhuizing van domein w een attribuut ingesteld. 


Windows 2003 niveau: weer wa aanvullingen
- typisch attributen waarvan het niet nodig was da ze zouden bestaan bvb het laatste moment van inloggen da wordt niet geindexeerd en gerecypleerd. En microsoft heeft dan toegegeven en er is nu een tweede attribuut die wel gerecypleerd word terwijl ge da als sysadmin zelf kon toestellen (dus ge hebt nu twee lastlogon attributen)

- locatie van standaard gebruiker en computer kan nie slechter gekozen zijn want er is geen OU en op OU kan je GPOs toepassen maar op die domme user/pc container kan da niet. Drm bestaat er de opdracht redirusr, redircmp zodanig da elke nieuwe gebruiker default in die container komt waar wel machtigingen en dergelijke op toepassen. Zodat je niet altijd alles manueel moet verzetten (das de moeite waard).

- veranderen van DC kon oorspronkelijk niet (je moest degraderen en promoveren)

- GC is niet meer noodzakelijk bij inloggen (adhv caching)

- Policies zijn moeilijk te beheren (soms) laat je policies filteren door lidmaatschap van groepen dan is debuggen vrij moeilijk.

nu bij hogere dingen altijd wa kleiner en inder en minder

2008 

fine-grained password policies
het is logischd a sommige groepen zwaardere restricties opgelegd krijgen en vroeger moest je daar apart domein voor hebben voor die ene groep me zwaardere restricties 

DFS replication is een nieuw alternatief mogelijk voor replicatie, je kan die keuze dan maken.

2008 r2 (2010)
windows server 2012
windows server 2012 r2 (2014)
laatste nieuwe niveaus zijn functioneel leeg, als er iets staat ist op vlak van security

functioneel stopt het ergens bij 2009

Zelfde verhaal bij forests:

2003
schemaobjecten verwijderen ga nie, uitschakelen wel da was altijd al maar nu kan je die opnieuw gebruiken

optimalere gc replicatie (als een attribuut wijzigt zal atomair enkel de wijziging doorgevoerd w)

KCC = knowledge consistency checker (wie moet me wie repliceren)

veranderen van domeinstructuur in een forest - je mag snoeien - de root blijft de root altijd tis de structuur eronder die je kan veranderen

read-only (bestaat slechts via 2008) maar je moet tenminste nen 2003 hebben op forest niveau

replicatie van de invidivuele waarden van multi valued attributen dus vanaf 2000 was het enkel attributen en niet objecten in zijn gehele en vanaf 2003 was het enkel de inhoud van de wijzigingen ipv de attributen in hun geheel.


2008
security dingen

je gaat vaak een RODC (read only DC) maken van plaatsen waar de DC op gevaarlijke plaats staat dus waar mensen op kunnen inloggen (een safety measure dus). We kunne attributen wel toelaten dat die copy krijgen van andere controllers maar voor gevaarlijke attributen gaat men zeggen we laten niet toe dat RODC een copy krijgt (vaak wachtwoorden bvb). Maar als nen persoon wil inlogge heeft die zijn ww niet dus er is een vorm van caching wel beschikbaar. Hier heeft men ene zware fout gemaakt dus je moet naar 2008 gaan als je da caching toe laat (bij voorkeur stel die caching niet aan).

Die geen automatisch site covering is wel nen goede. Site covering is mechanisme (vrij goed, maar hier niet) dat de controllers de controllers in de gaten gaan houden als ze er nie zijn en dan pakken ze de rol van die controller over. Dus die 2003 herkende nie da er een RODC was (2008) en die nam dus de rol van die 2008 over wa eigenlijk nie de bedoeling is. Die fout moest manueel int register aangepast worden vroeger. Maar bij nieuw functioneel niveau wast goed.


2008 r2
online restore van thombstone objects;
bij ad heb je ook een prullenbak. Als je iets kapot maakt dan wordt het eigenlijk gelabeled om verwijderd te worden. Dus hier laat je toe dat je thombstone objects terug kan plaatsen (dus eigenlijk prullenbak van bij bestanden).

## Trustrelaties

Als je meerdere domeinen hebt kunnen gebruikers daar zelf niets aan doen. Beslissing van beheerders. 
Ge zijt gedefineerd in het groen domein, je logt in op het rood domein en ze wil kunnen gebruik maken op dingen van het rode toestel. Da wil zeggen dat een DC moet gaan aankloppen bij een DC waarin die user wel is gedefineerd. Een trust relatie wil niet zeggen poort opengooien. 

Ook unidirectionele relatie (trusted en trusting).

Van zodra je trust niveau hoog genoeg is dan is ze automatisch.
Dan kan alles via de takken de van den boom eraan. Er wordt dan een vertrouwenspad afgelopen (in stapkes) niet per se rechtstreeks dus maar het is wel mogelijk.

Bij meerdere bomen in hetzelfde forest dan zit er ook een vertrouwensrelatie tss de roots als functioneel niveau hoog genoeg is. Anders moet je al die vertrouwensrelaties zelf manueel overal leggen.

extra expliciete trustrelaties. 

Stel 2 onafhankelijke forests. -> Forest trust relaties das nen pijl tss de twee roots van de forests.

Bij hoog genoeg functioneel niveau (standaard kerberos) dan kan het zelf via ldap (niet per se dus twee windows maar nen linux kan ook) -> kerberos realm 
Niet transitief. Dus wil je er 3 dan moet je alle roots allemaal op alle mogelijke manieren verbinden. A vertrouwt B en B vertrouwt C maar nie per se A die C vertrouwd.
meerdere forest maken een realm.

Merk op de meerdere types

type 2 verkorte vertrouwensrelaties 

shortcut relatie maken tss twee niveaus die veel gebruikt worden (zie da je nie nog eens erbovenop dan gaat shortcutten)

type 3 externe vertrouwensrelaties (vaak tijdelijk?)
niet-transitief automatisch




Je bent eigenlijk verplicht om die trustrelaties continu te bekijken. 
Netquery trust die kijken is een trustrelatie nog in tact of niet. 

merk op dat je ook moet beslissen of je de user trustrelaties ook op alle toestellen wil toeging geven. 

als je iemand opneemt in de groep dan zeg je ik ga rekening houden met zijn sID en sIDHIstory (sid filtering). Doordat hij zijn eigen attributen kan aanpassen. En sid filtering zegt dan ik ga geen rekening houden met de history van die accounts (want stel da hij doet alsof, of als hij ooit admin is geweest dan zou hij toch mogen).

Vroeger waren er veel meer redenen om domeinen toe te voegen.
(beheerzaken, sam-database, fysieke locaties, ...)

Nu blijft er van die redenen nie veel meer over door de OUs. DOor te zegegn alle admins hebben volledige toegang op alle objecten, of een deel geef ik extra toegang binnen bij mijn domein. Ipv meerdere domeinen werk je dus met meerdere OUs. Da kan vanalles zijn, gebruikers, pcs, printers...

Maak zo weinig mogelijk domeinen en verdeel die in zoveel mogelijke OUs. Ingevoerd om structuur te brengen en om die te beveiligen en tweede reden is voor de groep policies. Om op verschillende gebruikers en computers andere GPOs. 


Vroeger had men dan een master domain en resource domain (nt verplicht maar in de praktijk zo gegroeid.)

ook multiple master domain da kom je nu precies nog meeste tegen. 

Elk object overheven in het nieuw domein (ou).

Minder en minder domeinen om machtigingen over te dragen naar andere gebruikers. Elke OU kan je in detail vertellen wie wa mag wijzigen; Veel replicatieverkeerd van DCs zijn kan je opvangen met sites.
Die passwoord restricties die je kan invoeren; dus je hebt nu werkelijk een lagere kans om nieuwe domeinen te maken.


Stel je koopt licentie wa doe je ermee?
- Sowieso opnemen in domein server
- maak je er DC van of niet (vroeger ja want ge moest kiezen tijdens installatie)
- domeinlokale groepen waren enkel beschikbaar als je toestel dc was (nu niemeer)
(dus bovenstaande twee waren de redenen om da sowieso als dc op te nemen vroeger niemeer)
- nu zo weinig mogelijk dcs 

Als je 1 maakt via dcpromo 

eens je van toestel dc gemaakt hebt maak je er gc van of niet? (voeg hier ook de info van gc toe op testvraag)

dus ook hoe enzo

randvwn:
- in elke site zorg ervoor dat je per site 1 gc hebt want bij inloggen moet men altijd kijken bij gc 
en voor al de rest gebruik zo weinig mogelijk gc maak er genoeg voor belasting te spreiden en redundancy te hebben maar maak er zo weinig mogelijk om onnodig replicatie te hebben.


beslis ik of toestel read only moe zijn:
zie security inbreuken
beslis welke attributen je tegenhoud en welke je lokaal wil cachen

"alle dcs zijn gelijk" maar das dus nie waar -> sommge dcs zijn meer waard dan anderen

per domein 3 specifieke functies 

in elk groentje heb je 3 in gans forest 2

het schma mag maar gewijzigd worden via 1 controller (schema master)

domain naming master

hoe kun je in 1 opslagweten hoeveel partities zijn

ga naar Partitions container daar staan er evenveel objecten als er partities zijn
die twee bovenste zijn typisch applicatie partities

Partitions container mag maar aangepast worden op 1 controller ongeacht hoeveel je er hebt en da is de domain naming master


3 per domein (rid master, pdc emulator master, infrastructure master)

pdc emulator master -> nagenoeg waardeloos geworden, als er nog nt4 zijn (die werken in master/slave verhouding) en er moet dus 1e master zijn. Door die oude nt4 controllers

en er zitten nieuwe functies ook op nl tijdsynchroniatie, dus tijdsyncroniseren da moet enkel op de pdc emulator master want alle controllers gaan naar de pdc emulator master.

urgent replicatie -> als ik password wijzig dan gaat hij met de pdc onmiddelijk aan urgent replicatie doen

infrastructure master referentiele integriteit: ge hebt nen groep die leden bevat. Groep kan je leden aan toevoegen maar nie omgekeerd (backlink doet da zelf voor u). Gij wijzigt leden van de groep de ad services gaan voor u de memberof attribuut wijzigen.

Stel da je over domeinen gaat dus da uw ad services geen rechten hebben om da aan te passen. Dan gebeurt het domein da vaststelt ik zou iets willen aanpassen aan iets in ander domein dan bestaan er spoke objecten. En ge wijzigt da in u eigen domein (da heeft geen enkele onmiddelijke invloed). En die infrastrucutre kijkt dus zijn er spookobjecten en die leest da uit global catalog. 

Ge moet opletten wie je infrastrucutre laat worden. Als iedereen GC is dan is role van infrastructure master waardeloos. Maar het is de situatie waar je meerdere domeinen hebt en nie iedereen is gc dan mag je die infrastructure NIET laten lopen op een toestel dat GC is want anders kan hij geen vergelijking maken tss zijn versie en de versie van GC. 


fSMORoleOwner die gaat verwijzen wie DC is dus 5 identieke attributen in 5 verschillende objecten
functioneel niveau staat er ook 	

msDS-behavior-version is 2 = win2003

als je die vijf functies wil veranderen naar andere pc 
bij spreiden msdsutility of mtdsutility
die heeft transfer mogelijkheid parameter, netjes overdragne
andere situatie is da controller uit ligt
en je weet niet of hij terug aan de praat gaat liggen

dan ga je de functie overdragen zonder goedkeuring (want je kan niet) 
msdssyls ofzoiets en das overdragen zodner meer maar dan mag je die oude NIETMEER actief brengen.
