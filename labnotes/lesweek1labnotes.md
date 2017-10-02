# Windows Labo Lesweek 1

(Op 29/09)

## Inleiding

- Labo verplicht en goed op te volgen.
- Verplichte voorbereiding!

### Wat? 

- Automatiseren van taken (mail/FSO/Excel)
- WMI
- LDAP

### Hoe ?

- COM-objecten via ActiveX Scripting-> perlscript (vbscript)
- Inleiding Powershell

### COM (Component Object Model)

- stimuleert hergebruik van softwarecomponenten
- functionaliteit beschikbaar stellen via COM-pbjecten
- elk met hun eigen interface
- in labo enkel COM-cliënt
- In register:  HKEY_CLASSES_ROOT wordt er iets geregistreerd met daaronder takken
- Elke COM component heeft een eigen **ProgId** (zie ook regedit)
- Bvb excel heeft meerdere deelcomponenten (Addin, Chart, ...) Nodig **ExcelApplication** (en application.16 is twee keer zelfde de 16 is de versie) en andere is **Excel.Sheet**

- Let op de Wow6432Node voor 32 bit applicaties in/op 64 bit systemen.
- Niet overal heeft nen Typelib maar in andere componenten zit da bvb wel. Bvb in den Application niet en bij den Sheet wel dus je mag daar nie van uit gaan dat het er niet is. Is hij er ideaal maar misschien zit hij in nen andere.
- FSO (Fyle System Object)
- WMI
- LDAP


### Excel 

zie hierboven

### Mail

Collaboration Data Objects -> CDO
CDO.Message -> CLSID we weten da het 64 bit is anders op 2 plaatsen zoeken
Via dll geprogrammeerd en geen typeid maar hij heeft er wel één 


### COM Client

- Binden via ProgId
- Early binding - tijdens compilatie - snel maar complex
- Late binding - tijdens uitvoering - enkel ProgId - trager - geen controle op argumenten - soms IDispatch interface nodig
- Duale bestaat ook (beide)

Twee keuzes maken : script engine (taal) en script host (manier hoe je het gaat tonen)

``use Win32::OLE;`` voor perl te zeggen dat je met COM objecten gaat werken.


```perl
use Win32::OLE; 								# inladen module
$excel = WIN32::OLE->new("Excel.Application");	# Excel niet zichtbaar! Maar draait

```



## Notes

- LastError() is enkel relevant vlak na een gebeurtenis in het script. Is er iets dat nadien lukt dan ben je de fout kwijt!! 


## TODO volgende les

- Afwerken reeks 0 en reeks 1
- VM