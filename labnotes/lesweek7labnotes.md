# Notities labo lesweek 7

Hier programma da je zelf moet opstarten tweede deel van de les daarvoor moe je nie ingelogt zijn.

Let op filter vanboven nie na de event. Filteren in de while lus is eigenlijk stom als je kan filteren in de wql doe da daar.

Principe van wmi is da je ook geen processortijd krijgt tot die event dus geen timeding.

Skelet:
- query schrijven
- execnotificationquery
- oneindige while en op de plaats van de print kan alles staan van de vorige les

Nadeel maar 1 filter.

Wil je da skelet voor meerdere queries op te vangen dan is het een ander paar mouwen.

(Oef 54)

```
...

$query = "SELECT * FROM __InstanceCreationEvent WITHIN 1 WHERE TargetInstance ISA 'Win32_Process";

$eventsource = $wbemservice->ExecNotificationQuery($query);

while(1){
	
	$event = $EventSource->NextEvent();
	print $Event->{TargetInstance}->{Name};
}  
```


Programma draait niemeer!! In ander vb zie 58

script in script!

TargetEvent is het eventobject dat je voor de rest kan gebruiken maar het is een vaste naam via win3é:ole

Hoe debuggen? -> console gaat niet

via

my $shell=Win32::OLE->new("Wscript.Shell");
$shell->LogEvent(2,"USB ingeplugt");

--> alles van wmi is beschikbaar maar nie interageerbaar

eventvwr







In oefening 54 is event = eventsource->nextevent(); eigenlijk u targetevent van in de latere oefeningen!!



MERK OP DA BIJ DA SCRIPT IN SCRIPT u diepste script NIETS kent eh. Geen variabelen, geen statussen enzoverder




## Eigen notas labo pc

```

use Win32::OLE 'in';

my $pcname = ".";
my $namespace = "root/cimv2";
my $wbemservice = Win32::OLE->GetObject("winmgmts://$pcname/$namespace");


## DEEL 1

# InstanceCreationEvent -> voor als er nieuwe is
# InstanceOperationEvent -> voor alles en dan filter je in de WQL query (!) dat het subclasses zijn

# my $query = "SELECT * FROM __InstanceOperationEvent WITHIN 1 WHERE TargetInstance ISA 'Win32_Process'";
# 


## DEEL 2 : officedocument

my $query = "SELECT * FROM __InstanceOperationEvent WITHIN 1 WHERE TargetInstance ISA 'Win32_ExcelWorkBook'";

#"
#
#	SELECT * FROM __InstanceCreationEvent WITHIN 5
#	WHERE TargetInstance ISA 'Win32_ExcelWorkbook'
#	OR TargetInstance ISA 'Win32_WordWorkbook'
#"

my $eventsource = $wbemservice->ExecNotificationQuery($query);

while(1){

	my $event = $eventsource->NextEvent();

	# Deel 1
	#printf "%-29s started\n", $event->{TargetInstance}->{Name};
	
	# Deel 2
	printf "%-29s started\n", $event->{TargetInstance}->{Path_}->{Class};
}


use Win32::OLE 'in';
use Win32::OLE qw(EVENTS);

my $pcname = ".";
my $namespacecim = "root/cimv2";
my $namespaceoff = "root/msapps12";

my $sink = Win32::OLE->GetObject("winmgmts://$pcname/namespacecim");
Win32::OLE->WithEvents($sink, \&EventCallBack);

my $wbemservicecim = Win32::OLE->GetObject("winmgmts://$pcname/$namespacecim");
my $wbemserviceoff = Win32::OLE->GetObject("winmgmts://$pcname/$namespaceoff");


my $query1 = "SELECT * FROM __InstanceOperationEvent WITHIN 1 WHERE TargetInstance ISA 'Win32_Process'";

my $query2 = "
		SELECT * FROM __InstanceCreationEvent WITHIN 1 
		WHERE TargetInstance ISA 'Win32_ExcelWorkBook'
		OR TargetInstance ISA 'Win32_Word12Document'
		OR TargetInstance ISA 'Win32_PowerPointPresentation'
"










use Win32::OLE 'in';
use Win32::OLE::Const 'Microsoft WMI Scripting ';

my $ComputerName  = ".";
my $NameSpace = "root/cimv2";
my $WbemServices = Win32::OLE->GetObject("winmgmts://$ComputerName/$NameSpace");

my $InstanceEvent = $WbemServices->Get(__EventFilter)->SpawnInstance_();
$InstanceEvent->{Name}            = 'test';
$InstanceEvent->{QueryLanguage}   = 'WQL';
#Kan ook zonder interne polling, met een Excentric Event (zie onderaan)
$InstanceEvent->{Query} = qq[SELECT * FROM __InstanceCreationEvent WITHIN 1 
             WHERE TargetInstance ISA 'Win32_LogicalDisk' 
                   and TargetInstance.Name<>'B:' and TargetInstance.Name<>'A:' ];

$Filter = $InstanceEvent->Put_(wbemFlagUseAmendedQualifiers);
$Filterpad = $Filter->{path};

#Reactie - Kan ook uitgewerkt worden met NTEventLogEventConsumer (maar is omslachtiger om te testen) :
my $InstanceConsumer = $WbemServices->Get("LogFileEventConsumer")->SpawnInstance_();
$InstanceConsumer->{Name}="test";
$InstanceConsumer->{FileName} = 'C:\\\\temp\\log.txt';
$InstanceConsumer->{Text} = "USB (%TargetInstance.Name%) inserted";
$Consumer = $InstanceConsumer->Put_(wbemFlagUseAmendedQualifiers);
$Consumerpad=$Consumer->{path};  

my $Instance = $WbemServices->Get(__FilterToConsumerBinding)->SpawnInstance_();
$Instance->{Filter}   = $Filterpad;
$Instance->{Consumer} = $Consumerpad;
$Result = $Instance->Put_(wbemFlagUseAmendedQualifiers);
print $Result->{path},"\n"; #ter controle

#Tweede oplossing met Excentric Event - wijzig de oplossing op twee plaatsen:
$Instance->{Query} = qq[ SELECT * FROM Win32_VolumeChangeEvent
     WHERE EventType = 2 and DriveName <> 'B:' ];

$InstanceConsumer->{Text}    = "USB (%DriveName%) inserted op %__SERVER% - %__CLASS%"; #DriveName-attribuut van Win32_VolumeChangeEvent , enkel __CLASS in ingevuld







```

