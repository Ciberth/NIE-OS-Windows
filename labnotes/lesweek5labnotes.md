
# Oefening lesweek 5 

Merk op!!

$methode->{"invoerparameters"}->{"properties"}->...

da kan enkel als er wel degelijk invoerparameters zijn want als er geen zijn kan je geen properties opvragen dus altijd in IF test ofwel aparte objecten

if($methode->{"invoerparameters"}){
	$methode->{"invoerparameters"}->{"properties"}->...	
}



oef 38 - 40 belangrijk!!

oef 49!!

```
use strict;
use warnings;
use Win32::OLE "in";
use Win32::OLE::Const "Microsoft WMI Scripting";

$Win32::OLE::Warn = 3;


my $locator = Win32::OLE->new("WbemScripting.SWbemLocator");
my $wbem = $locator->ConnectServer(".", "root/cimv2");

my $class = $wbem->get("Win32_Environment");


print "\n\n --- PRINT ENV VARS --- \n\n";

foreach(in $wbem->ExecQuery("select * from Win32_Environment")){
	print $_->{"Name"}, " --> ", $_->{"VariableValue"}, "\n";
}


print "\n\n --- TOEVOEGEN --- \n\n";

my $new = $class->SpawnInstance_();

$new->{"Name"} = "TestVar2";
$new->{"Username"} = "<SYSTEM>"; # of "Administrator"
$new->{"VariableValue"} = "test2";
$new->{"SystemVariable"} = 1; # of 0 voor uservariable

my $res = $new->Put_;


print "\n\n --- PRINT ENV VARS --- \n\n";

foreach(in $wbem->ExecQuery("select * from Win32_Environment")){
	print $_->{"Name"}, " --> ", $_->{"VariableValue"}, "\n";
}


# verwijderen
# via path niet goed
#$wbem->get("Win32_Environment.name='TestVar',username='Administrator'")->delete;


# via foreach en wql

foreach(in $wbem->ExecQuery("select * from Win32_Environment where name='TestVar'")) {

	print $_

}




```



use Win32::OLE "in";
use Win32::OLE::Variant;

use strict;
use warnings;
$Win32::OLE::Warn=3;

my $locator = Win32::OLE->new("WbemScripting.SWbemLocator");
my $wbem = $locator->ConnectServer(".", "root/cimv2");

my %RootKey = ( HKEY_CLASSES_ROOT   => 0x80000000
              , HKEY_CURRENT_USER   => 0x80000001
              , HKEY_LOCAL_MACHINE  => 0x80000002
              , HKEY_USERS          => 0x80000003
              , HKEY_CURRENT_CONFIG => 0x80000005
              , HKEY_DYN_DATA       => 0x80000006 );

my $reg = $wbem->Get("StdRegProv");
my $args = $reg->{methods_}->{EnumKey}->{inparameters};
$args->{hDefKey}  = $RootKey{HKEY_LOCAL_MACHINE};

my $path = "SYSTEM\\CurrentControlSet\\Services\\Tcpip";
print "$path\n";
printChildren($path, 1);

sub printChildren {
	my ($path, $level) = @_;
	$args->{sSubKeyName} = $path;

	# out / return values komen in de return!
	my $res =  $reg->ExecMethod_("EnumKey", $args);
	return if $res->{returnvalue}; # 0 => success
	return unless $res->{sNames};

	foreach my $x (@{$res->{sNames}}) {
		printf "%s%s \n", "\t" x $level, $x;
		my $subPath = $path . "\\" . $x;
		printChildren($subPath, $level+1);
	}
}

