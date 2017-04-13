<properties 
    pageTitle="Integrare un'app con una rete virtuale Azure" 
    description="Viene illustrato come connettere un'applicazione di servizio App Azure a una rete virtuale Azure nuova o esistente" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor="cephalin"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="ccompy"/>

# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrare l'app con una rete virtuale Azure #

In questo documento viene descritta la funzionalità di integrazione virtuali servizio App Azure e viene illustrato come è stata configurata con le applicazioni di [Servizio App Azure](http://go.microsoft.com/fwlink/?LinkId=529714).  Se si ha familiarità con le reti virtuali Azure (VNETs), questa è una funzionalità che consente di inserire molte delle risorse di Azure in una rete routeable internet non è possibile controllare l'accesso a.  Queste reti possono quindi essere collegate alle tue reti locale attivato con una vasta gamma di tecnologie VPN.  Per informazioni su altre informazioni su reti virtuali Azure iniziare con le seguenti informazioni: [Informazioni generali sui rete virtuale Azure][VNETOverview].  

Il servizio di App Azure include due forme.  

1. I sistemi multi-tenant che supportano la gamma completa di prezzi piani
1. La caratteristica di premium App servizio ambiente (base) che distribuisce nel VNET.  

In questo documento attraversa VNET integrazione e non App servizio ambiente.  Se si desidera acquisire familiarità con la funzionalità di base e quindi con le seguenti informazioni: [Introduzione ambiente servizio App][ASEintro].

Integrazione di VNET consente l'accesso app web alle risorse della rete virtuale ma non concedere l'accesso privato all'app web dalla rete virtuale.  Accesso ai siti personali è disponibile solo con un ASE configurato con un interno carico bilanciamento (ILB).  Per informazioni dettagliate sull'uso di un ASE ILB, iniziare con l'articolo: [creazione e utilizzo di un ASE ILB][ILBASE]. 

Uno scenario comune nel punto in cui utilizzare l'integrazione di VNET è consentire l'accesso da un'applicazione web a un database o un servizio web installato in un computer virtuale della rete virtuale Azure.  Con l'integrazione di VNET non è necessario per esporre un endpoint pubblico per le applicazioni di una macchina virtuale ma può utilizzano invece gli indirizzi di routing internet non privati.  

La funzionalità di integrazione VNET:

- richiede un server Standard o Premium prezzi piano 
- siano compatibili con Classic(V1) o VNET Manager(V2) delle risorse 
- supporta porte TCP e UDP
- funziona con le applicazioni Web, mobili e API
- Consente di connettersi a solo 1 VNET contemporaneamente un'app
- Consente di VNETs fino a 5 per l'integrazione con un piano di servizio App 
- consente la stessa VNET che verrà utilizzato da più applicazioni in un piano di servizio App
- supporta un contratto di servizio 99,9% a causa di una certa nel Gateway VNET

Esistono alcune operazioni che l'integrazione di VNET non supporta inclusi:

- montaggio un'unità
- Integrazione di Active Directory 
- NetBios
- accesso ai siti personali

### <a name="getting-started"></a>Guida introduttiva ###
Ecco alcuni aspetti da tenere in considerazione prima di collegare un'app web a una rete:

- Integrazione di VNET funziona solo con App in un **Standard** o **Premium** prezzi piano.  Se si abilita la caratteristica e quindi ridurre il piano di servizio App a un piano di prezzo non supportato le app andranno perse le connessioni a VNETs in uso.  
- Se la rete virtuale di destinazione esiste già, è necessario che VPN punto al sito attivato con un gateway routing dinamico prima che possono essere connessi a un'app.  Se il gateway è configurato con il routing statico, è possibile attivare punto al sito rete privata virtuale (VPN).
- Il VNET deve essere nello stesso abbonamento Plan(ASP) il servizio di App.  
- Le app che si integrano con un VNET utilizzerà il DNS specificato per tale VNET.
- Per impostazione predefinita le app di integrazione solo indirizza il traffico nel VNET in base alle route definite nel VNET.  


## <a name="enabling-vnet-integration"></a>Attivazione dell'integrazione VNET ##

In questo documento è focalizzato principalmente sulla tramite il portale di Azure per l'integrazione di VNET.  Per attivare l'integrazione di VNET con l'app tramite PowerShell, seguire le istruzioni visualizzate: [connettere le app per la rete virtuale tramite PowerShell][IntPowershell].

Viene offerta la possibilità di connettersi l'app a una rete virtuale nuova o esistente.  Se si crea una nuova rete come parte dell'integrazione quindi oltre a creare il VNET, un gateway routing dinamico sarà preconfigurato automaticamente e verrà attivata, scegliere Site VPN.  

>[AZURE.NOTE] Configurazione di una nuova integrazione virtuali può richiedere diversi minuti.  

Per attivare l'integrazione di VNET Apri l'app impostazioni e quindi selezionare la rete.  L'interfaccia utente visualizzata offre tre opzioni di rete.  Questa guida è solo passando integrazione VNET attraverso le connessioni ibrida e App servizio ambienti sono descritti più avanti in questo documento.  

Se l'app è non il corretto prezzi piano l'interfaccia utente ha consentono di ridimensionare il piano a un piano prezzo superiore di propria scelta.


![][1]
 
###<a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Attivazione dell'integrazione VNET con un VNET preesistenti###
L'interfaccia utente integrazione VNET consente di selezionare un elenco del VNETs.  VNETs classica indica che sono ad esempio la parola "Classica" tra parentesi accanto al nome VNET.  L'elenco è ordinata in modo che Manager delle risorse VNETs sono elencati per primi.  Nell'immagine seguente è possibile vedere che è possibile selezionare un solo VNET.  Esistono diversi motivi che un VNET verrà disattivato tra cui:

- il VNET si trova nell'abbonamento a un altro che l'account ha accesso a
- il VNET non ha punto al sito abilitato
- il VNET non è un gateway routing dinamico


![][2]

Per attivare l'integrazione è sufficiente fare clic su VNET che si desidera integrare con.  Dopo aver selezionato il VNET, l'app verrà riavviato rendere effettive le modifiche.  

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Abilitare punto al sito in un VNET classica #####
Se il VNET non è un gateway né né punto al sito è necessario configurare che la prima.  Per ripetere l'operazione per un VNET classica, passare al [Portale di Azure] [ AzurePortal] e visualizzare l'elenco di Networks(classic) virtuale.  Da qui fare clic sulla rete si vuole integrare con e fare clic sulla casella grande in Essentials chiamato connessioni VPN.  Da qui è possibile creare il punto per VPN del sito e anche creare un gateway.  Dopo aver scelto attraverso il punto del sito con esperienza di creazione di gateway sarà circa 30 minuti prima che sia pronto.  

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Attivazione punto al sito in un VNET Manager delle risorse #####

Per configurare un VNET Manager delle risorse con un gateway e scegliere sito è necessario utilizzare PowerShell come descritto in questo caso, [configurare una connessione punto a sito a una rete virtuale tramite PowerShell][V2VNETP2S].  Interfaccia utente per eseguire questa funzionalità non è ancora disponibile. 

### <a name="creating-a-pre-configured-vnet"></a>Creazione di un VNET preconfigurato ###
Se si desidera creare un nuovo VNET configurata con un gateway e punto al sito, il servizio di App dell'interfaccia utente di rete è in grado di farlo ma solo per una gestione risorse VNET.  Se si desidera creare un VNET classica con un gateway e un punto al sito è necessario eseguire questa operazione manualmente tramite l'interfaccia utente di rete. 

Per creare un VNET Manager delle risorse tramite l'interfaccia utente integrazione VNET, è sufficiente selezionare **Crea nuovo virtuali** e fornire la:

- Nome di rete virtuale
- Blocco di indirizzi virtuali
- Nome subnet
- Blocco di indirizzi subnet
- Blocco di indirizzi di gateway
- Blocco di indirizzi punto al sito

Se si desidera che questo VNET per connettersi a una delle altre rete è consigliabile evitare rileva spazio di indirizzi IP che si sovrappone a tali reti.  

>[AZURE.NOTE] Creazione di Manager delle risorse VNET con un gateway dura circa 30 minuti e non è attualmente si integra la VNET con l'app.  Dopo aver creato il VNET con il gateway è necessario tornare all'app dell'interfaccia utente integrazione VNET e selezionare il nuovo VNET.

![][3]

Azure VNETs normalmente vengono create all'interno di indirizzi di rete privata.  Per impostazione predefinita l'integrazione di VNET caratteristica indirizza il traffico destinato a tali intervalli di indirizzi IP nel VNET.  Intervalli di indirizzi IP privati sono:

- 10.0.0.0/8 - questo è diverso da quello di 10.0.0.0 - 10.255.255.255
- 172.16.0.0/12 - questo è diverso da quello di 172.16.0.0 - 172.31.255.255 
- 192.168.0.0/16 - questo è diverso da quello di 192.168.0.0 - 192.168.255.255
 
Lo spazio di indirizzi VNET deve essere in notazione CIDR.  Se si ha familiarità con la notazione CIDR, è un metodo per specificare blocchi di indirizzi con indirizzo IP e un numero intero che rappresenta la maschera di rete. Come riferimento rapido, tenere presente che 10.1.0.0/24 sarebbe 256 indirizzi e 10.1.0.0/25 128 indirizzi.  Indirizzo IPv4 con un /32 è pari a 1 indirizzo.  

Se si imposta qui le informazioni sul server DNS che verrà impostata per la VNET.  Dopo la creazione di VNET è possibile modificare queste informazioni dalle esperienze utente VNET.

Quando si crea un VNET classico tramite l'interfaccia utente integrazione VNET, verrà creato un VNET nello stesso gruppo di risorse dell'applicazione. 

## <a name="how-the-system-works"></a>Come funziona il sistema ##
Dietro le quinte questa funzionalità si basa sulla tecnologia VPN punto da sito a cui connettersi il VNET l'app.   Le applicazioni di servizio App Azure dispone di un'architettura di sistema multi-tenant che esclude il provisioning di un'app direttamente in un VNET come avviene con macchine virtuali.  Grazie al punto al sito tecnologia è limitato accesso alla rete per la macchina virtuale che ospita l'app.  Accesso alla rete con restrizioni in tali host app ulteriormente in modo che le app possono accedere solo le reti a cui si configurarli per accedere.  

![][4]
 
Se non è stato configurato un server DNS alla rete virtuale sarà necessario utilizzare indirizzi IP.  Durante l'uso di indirizzi IP, tenere presente che i vantaggi principali di questa funzione sono che consente di utilizzare gli indirizzi privati interni alla rete privata.  Se si imposta l'app fino a utilizzare gli indirizzi IP pubblico di una delle proprie macchine virtuali, non si usa la funzione di integrazione VNET e comunicare in internet.


##<a name="managing-the-vnet-integrations"></a>Gestire le integrazioni VNET##

La possibilità di connettere e disconnettere un VNET è a un livello di app.  Operazioni che possono influire sulla integrazione VNET tra le applicazioni più sono un livello di ASP.  Dall'interfaccia utente che verrà visualizzata a livello di app è possibile ottenere informazioni dettagliate sul VNET.  La maggior parte delle informazioni viene visualizzato anche a livello di ASP.  

![][5]

Nella pagina stato della caratteristica rete è possibile visualizzare se l'app è connesso al VNET.  Se il gateway VNET verso il basso per qualsiasi motivo quindi seguente verrebbe visualizzati come non connesso.  

Le informazioni che sono ora disponibili nell'app che corrisponde al livello dell'interfaccia utente integrazione VNET le informazioni di dettaglio che viene visualizzato dalla pagina ASP.  Ecco gli elementi:

- Nome VNET - questo collegamento apre la rete dell'interfaccia utente
- Posizione, questo riflette la posizione del VNET.  È possibile per l'integrazione con un VNET in un'altra posizione.
- Lo stato del certificato - sono disponibili i certificati utilizzati per proteggere la connessione VPN tra l'app e la VNET.  Questo riflette un test per verificare che siano aggiornati.
- Stato del gateway - il gateway è sarà verso il basso per qualsiasi motivo quindi l'app non è possibile accedere alle risorse di VNET.  
- Spazio di indirizzi VNET - si tratta di spazio di indirizzi IP per il VNET.  
- Scegliere spazio indirizzo sito - questo è il punto di spazio di indirizzi IP del sito per il VNET.  L'app viene visualizzata la comunicazione come provenienti da uno degli indirizzi IP nello spazio di indirizzi.  
- Sito allo spazio di indirizzi di sito, è possibile utilizzare VPN da un sito per connettere il VNET per le risorse locale attivato o altri VNETs.  Devono avere configurato quindi gli intervalli di indirizzi IP definiti con che connessione VPN verrà visualizzato di seguito.
- Server DNS - se si dispone di server DNS configurati con le VNET quindi sono elencati di seguito.
- Indirizzi IP instradati a VNET - tale posizione sono un elenco di indirizzi IP che il VNET sono definite per il routing.  Qui verranno visualizzati gli indirizzi.  

L'unica operazione che è possibile eseguire nella visualizzazione app dell'integrazione VNET consiste nello scollegare l'app da VNET è connesso a.  Per eseguire questa operazione è sufficiente fare clic su Disconnetti nella parte superiore.  Questa azione non viene modificato il VNET.  La configurazione VNET e le relative inclusi i gateway rimarrà invariata.  Se si desidera eliminare il VNET è necessario prima di tutto eliminare le risorse in essa inclusi i gateway.  

La visualizzazione App servizio piano è disponibile un numero di operazioni aggiuntive.  È anche possibile accedervi in modo diverso rispetto a dall'app.  Per raggiungere l'interfaccia utente di rete di ASP aprire semplicemente l'interfaccia utente di ASP e scorrere verso il basso.  Esiste un elemento di interfaccia utente chiamato lo stato della funzionalità di rete.  Dà alcuni dettagli secondarie all'interno dell'integrazione VNET.  Fare clic su questa interfaccia utente, verrà visualizzata l'interfaccia utente lo stato di funzionalità di rete.  Se si fa clic su "Fare clic qui per gestire" si aprirà la dell'interfaccia utente che elenca le integrazioni VNET questo ASP.

![][6]

È consigliabile tenere presenti quando si esamina le posizioni delle VNETs si integra con la posizione dell'ASP.  Quando il VNET si trova in un'altra posizione si è molto probabile visualizzare i problemi di latenza.  

VNETs integrato con è un promemoria per VNETs quanti le app sono integrate con questo ASP e il numero è consentito.  

Per vedere i dettagli aggiunti ogni VNET, scegliere solo VNET che interessa.  Oltre i dettagli che sono stati indicati in precedenza che verrà anche visualizzato un elenco delle app di questo ASP che usano tale VNET.  

Per quanto riguarda le azioni sono disponibili due azioni principale.  Il primo è la possibilità di aggiungere route che indirizzare il traffico uscire dall'applicazione nel VNET.  La seconda azione è la possibilità di sincronizzare i certificati e informazioni relative alla rete.

![][7]

**Routing** Come indicato in precedenza indirizza definiti nel VNET è cosa viene utilizzato per indirizzare il traffico nel VNET dell'app.  Esistono alcuni usi anche se nel punto in cui i clienti da inviare il traffico in uscita aggiuntivo da un'app nel VNET e cercandole questa funzionalità è disponibile.  Che cosa succede al traffico che una volta fino a come l'utente configura loro VNET.  

**Certificati** Lo stato di certificato corrisponde una verifica viene eseguita dal servizio di App per convalidare i certificati che si sono utilizzando per la connessione VPN sono comunque utile.  Quando l'integrazione di VNET abilitato, quindi se si tratta di integrazione prima a tale VNET da qualsiasi apps ASP, è presente un richiesta lo scambio di certificati per garantire la sicurezza della connessione.  Insieme i certificati è possibile ottenere la configurazione del DNS, cicli e altre operazioni simili che descrivono la rete.
Se viene modificata tali certificati o informazioni relative alla rete sarà necessario fare clic su "Rete di sincronizzazione".  **Nota**: quando si fa clic "Sincronizzazione rete", quindi si verificherà un breve arresto in connettività tra l'app e le VNET.  Mentre l'app non essere riavviato la perdita di connettività potrebbe causare il sito non funzionare correttamente.  

##<a name="accessing-on-premise-resources"></a>Accesso alle risorse locale##

Uno dei vantaggi della funzionalità di integrazione VNET è che se il VNET è connesso alla rete locale attivato con una connessione VPN da un sito, quindi le app possono avere accesso alle risorse locale attivato dell'app.  Per questa operazione anche se potrebbe essere necessario aggiornare il gateway VPN locale attivato con le route per il sito IP, scegliere intervallo.  Quando la connessione VPN da un sito prima di tutto è configurata l'archiviazione gli script utilizzati per configurarlo devono configurare route tra il punto di VPN sito.  Se si aggiunge il punto di Site VPN dopo il creare la rete VPN da un sito, quindi è necessario aggiornare manualmente le route.  Informazioni dettagliate su come eseguire questa operazione variano per gateway e non sono descritti di seguito.  

>[AZURE.NOTE] Durante la funzionalità di integrazione VNET sarà compatibili con una connessione VPN da un sito per accedere alle risorse locale attualmente non funzionerà con un VPN ExpressRoute per lo stesso scopo.  Questo è valido durante l'integrazione con un Classic o Manager delle risorse VNET.  Se è necessario eseguire l'accesso tramite un VPN ExpressRoute è possibile utilizzare un ASE che possono essere eseguiti nel VNET. 

##<a name="pricing-details"></a>Informazioni sui prezzi##
Esistono alcuni dettagli da tenere presenti quando si utilizza la funzionalità di integrazione VNET prezzi.  Sono disponibili 3 in base alle tariffe correlati all'uso di questa caratteristica:

- Requisiti per il livello di prezzi ASP
- I costi di trasferimento dei dati
- Costi di Gateway VPN.

Per le app di essere in grado di utilizzare questa caratteristica, devono essere in un server Standard o Premium App servizio pianificare.  È possibile visualizzare altri dettagli sui tali costi qui: [Prezzi assistenza App][ASPricing]. 

Perché le punto al sito VPN vengono gestiti, avere sempre a disposizione un costo per i dati in uscita tramite la connessione a VNET integrazione anche se il VNET in dello stesso data center.  Per vedere quali tali spese sono consultare qui: [Dettagli prezzi trasferire dati][DataPricing].  

L'ultimo elemento è il costo dei gateway VNET.  Se non è necessario il gateway, ad esempio VPN da un sito che si stava cercando sono pagamento per i gateway supportare le funzionalità di integrazione VNET.  Sono disponibili informazioni dettagliate sui tali costi qui: [Prezzi Gateway VPN][VNETPricing].  

##<a name="troubleshooting"></a>Risoluzione dei problemi##

Mentre la caratteristica è facile da configurare non significa che l'esperienza dell'utente sarà problema gratuito.  In caso problemi di accesso l'endpoint desiderato sono dipendono alcune utilità che è possibile utilizzare per testare la connettività dalla console di app.  Sono disponibili due esperienze console che è possibile utilizzare.  Uno dalla console di Kudu e l'altro console che è possibile contattare nel portale di Azure.  Per accedere alla console di Kudu dell'App strumenti -> Kudu.  Questo è uguale passando al [nomesito]. scm.azurewebsites.net.  Una volta che si apre è sufficiente fare clic sulla scheda console di Debug.  Per accedere alla console di contenuto portale Azure quindi dell'App strumenti -> Console.  


####<a name="tools"></a>Strumenti####

Riquadro strumenti ping, nslookup e tracert non funzionano tramite la console per motivi di sicurezza.  Per riempire void qui hanno avuto due strumenti aggiunto.  Per verificare la funzionalità DNS abbiamo aggiunto uno strumento denominato nameresolver.exe.  La sintassi è:

    nameresolver.exe hostname [optional: DNS Server]

È possibile utilizzare nameresolver per controllare i nomi host che dipende dall'app.  In questo modo è possibile verificare se è installato nulla erroneamente configurato con il DNS o forse non hanno accesso al server DNS.

Lo strumento successivo consente di eseguire il test della connettività TCP a una combinazione di host e porta.  Questo strumento è denominato tcpping.exe e la sintassi è:

    tcpping.exe hostname [optional: port]

Questo strumento che indica se è possibile contattare un host specifico e la porta ma non verrà eseguita la stessa attività che viene visualizzato con i pacchetti ICMP in base a utilità ping.  L'utilità di ping ICMP che indica se l'host è attiva.  Con tcpping scoprire se è possibile accedere a una porta specifica su un host.  


####<a name="debugging-access-to-vnet-hosted-resources"></a>Il debug di accesso a VNET ospitato risorse####

Esistono alcune cose che possono impedire l'app di raggiungere un host specifico e la porta.  La maggior parte dei casi è uno dei tre elementi:

- **Esiste un firewall nel modo**  Se si dispone di un firewall nel modo verrà raggiunto il timeout TCP.  In questo caso è 21 secondi.  Usare lo strumento tcpping per verificare la connettività.  Timeout TCP possono dipendere da molti aspetti oltre firewall ma iniziare la.  
- **Il DNS è non è accessibile**  Timeout DNS è di 3 secondi al server DNS.  Se si dispone di 2 server DNS che è 6 secondi.  Utilizzare nameresolver per verificare se funziona DNS.  Tenere presente che che non usino il DNS è configurato il VNET con non è possibile usare nslookup.
- **Intervalli IP P2S non valido** Il punto di intervalli IP sito deve essere nella casella intervalli IP privati RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255) se l'intervallo utilizza IP esterno che il non funziona.  

Se il problema non rispondono a questi elementi, sarà prima di elementi semplici, come:  

- Il Gateway viene visualizzato come backup nel portale?
- I certificati presentino come vengano aggiornati?
- Chiunque ha modificare la configurazione della rete senza eseguire una rete"Sincronizza" in ASP interessato? 

Se il gateway verso il basso quindi portare eseguire il backup.  Se non è sincronizzati i certificati passare alla visualizzazione ASP dell'integrazione VNET e premere "Rete di sincronizzazione".  Se si sospetta che siano trascorse una modifica apportata alla configurazione VNET e non è stato sincronizzazione da con l'ASP quindi passare alla visualizzazione ASP dell'integrazione VNET e premere "Rete di sincronizzazione" oltre ad un promemoria, in questo modo un breve arresto con la connessione VNET e le app.  

Tutto ciò è fine è necessario approfondire un po':

- Sono disponibili le applicazioni tramite l'integrazione di VNET raggiunga risorse nella stessa VNET? 
- Può accedere alla console di app e consente di raggiungere tutte le altre risorse il VNET tcpping?  

Se vengono soddisfatte le risposte sopra sarà fitta dell'integrazione VNET e il problema è un altro percorso.  Verrà visualizzata nel punto in cui viene ottenuta far più complessa, poiché non sarà più semplice per visualizzare il motivo per cui non è possibile raggiungere una host: porta.  Alcune delle cause includono:

- un firewall si verificano sull'host impedire l'accesso alla porta applicazione dal punto di all'intervallo IP del sito.  Incrocio subnet spesso richiede l'accesso pubblico.
- l'host di destinazione non è disponibile
- l'applicazione è verso il basso
- si ha il problema IP o nome host
- l'applicazione è in attesa su una porta diversa rispetto a quello previsto.  È possibile controllare passando su che ospitano e usando "netstat - aon" dal prompt dei comandi cmd.  Questo illustra il processo ID ascolto cosa porta.  
- i gruppi di sicurezza di rete sono configurati in modo impedire l'accesso per l'applicazione host e la porta dal punto di all'intervallo IP del sito

Tenere presente che non si conosce quali IP il punto all'intervallo di sito IP utilizzati l'app, quindi è necessario consentire l'accesso dall'intero intervallo.  

Debug ulteriori passaggi:

- accedere a un altro macchine Virtuali del VNET e provare a raggiungere la risorsa host: la porta da tale posizione.  Esistono alcune utilità di ping TCP a questo scopo è possibile utilizzare o addirittura possibile telnet se necessario.  Lo scopo di seguito è solo per determinare se è disponibile la connettività da questo altre macchine Virtuali. 
- visualizzare un'applicazione su un altro macchine Virtuali e testare l'accesso a tale host e la porta dalla console dell'App  
####<a name="on-premise-resources"></a>Sulle risorse locale####
Se il non è raggiungibile risorse locale sarà la prima cosa è necessario verificare se è possibile contattare una risorsa nel VNET.  Se che funziona passaggi successivi sono un'operazione semplici.  Da una macchina virtuale nel VNET è necessario provare a raggiungere l'applicazione locale attivato.  È possibile utilizzare telnet o un'utilità di ping TCP.  Se non è possibile raggiungere la risorsa locale attivato quindi prima di tutto assicurarsi che le macchine Virtuali effettui la connessione VPN da un sito.  Se è valida controllare le stesse operazioni indicate in precedenza e la configurazione di gateway locale e lo stato attivato.  

A questo punto se il VNET ospitata macchine Virtuali possono raggiungere il sistema locale attivato ma l'app non è possibile quindi il motivo è probabilmente una delle operazioni seguenti:
- i percorsi non sono configurati con il punto di intervalli di indirizzi IP del sito in gateway locale attivato
- i gruppi di sicurezza di rete bloccano l'accesso per il punto di intervalli IP del sito
- il firewall locale attivato bloccano il traffico dal punto di all'intervallo IP del sito
- si dispone di un Route(UDR) definiti dall'utente nel VNET che impedisce il punto di traffico del sito in base a raggiungere la propria rete locale attivato

## <a name="hybrid-connections-and-app-service-environments"></a>Servizio di App ambienti e connessioni ibrido##
Sono disponibili 3 funzionalità che consentono l'accesso alle risorse VNET ospitato.  Sono:

- Integrazione di VNET
- Connessioni ibrido
- Servizio di App ambienti

Connessioni ibrida è necessario installare un agente di inoltro chiamato Manager(HCM) di connessione ibrido nella rete.  Il HCM deve essere in grado di connettersi a Azure e anche a un'applicazione.  Questa soluzione è particolarmente efficace da una rete remota, ad esempio la rete locale attivato o anche un'altra cloud ospitato rete poiché non richiede un endpoint accessibile internet.  Il HCM può essere eseguito solo in Windows e possono includere fino a 5 istanze in esecuzione per fornire disponibilità.  Connessioni ibrido supporta solo TCP attraverso ogni endpoint HC è in modo che corrispondano a una combinazione di host: porta specifica.  

La funzionalità dell'ambiente servizio App consente di eseguire un'istanza del servizio di App Azure nel VNET.  In questo modo le app accedere alle risorse dei VNET senza qualsiasi passaggi aggiuntivi.  Alcuni dei vantaggi di un ambiente di servizio App è che è possibile utilizzare 8 worker core dedicato con 14 GB di RAM.  Un altro vantaggio è che è possibile adattare il sistema secondo le proprie esigenze.  Diversamente da quanto succede ambienti multi-tenant nel punto in cui dimensione è limitata la pagina ASP, in un ASE possibile controllare il numero di risorse da assegnare al sistema.  Per quanto riguarda lo stato attivo alla rete di questo documento uno dei si ottiene con un ASE non con integrazione VNET però è che funziona con un VPN ExpressRoute.  

Con che alcuni utilizzano sovrapposizione maiuscole e minuscole, nessuna di queste funzionalità possibile sostituire tutti gli altri.  Sapere quali funzionalità da utilizzare è collegato alle proprie esigenze e come verranno vuole usarlo.  Per esempio:

- Se si è uno sviluppatore e si desidera semplicemente eseguire un sito di Azure e automaticamente accedere al database workstation sotto alla scrivania cosa più semplice da utilizzare è ibrido connessioni.  
- Se ci si trova una grande organizzazione che desideri inserire un numero elevato di proprietà web di pubblico cloud e gestirli nella propria rete quindi che si desidera accedere con l'ambiente di servizio App.  
- Se si dispone di un numero di servizio App ospitato App e si desidera semplicemente accedere alle risorse il VNET VNET integrazione sarà possibile passare.  

Oltre ai casi di utilizzo sono disponibili alcuni semplicità correlati aspetti.  Se il VNET è già connessi alla rete locale attivato quindi tramite l'integrazione di VNET o un ambiente App è un modo semplice per l'utilizzo di risorse locale.  Mano, se il VNET non è connesso alla rete locale attivato è molto più il sovraccarico di configurare una connessione VPN da un sito con il VNET confrontata con l'installazione di HCM.  

Oltre a differenze funzionali esistono anche prezzi differenze.  La caratteristica di ambiente servizio App è un premio l'offerta di servizi ma offre il maggior numero di rete possibilità di configurazione oltre alle altre caratteristiche.  Integrazione di VNET può essere utilizzato con Standard o ASP Premium ed è ideale per l'utilizzo in modo sicuro di risorse nel VNET da multi-tenant servizio App.  Connessioni ibrido dipende attualmente un BizTalk account che dispone prezzi livelli che iniziare gratuita e quindi gradualmente più costosa in base alla quantità è necessario.  Quando si tratta di utilizzo in molte reti attraverso, caratteristica non è disponibile altri come connessioni ibrido che consentono di accedere alle risorse oltre 100 reti separate.    


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[IntPowershell]: http://azure.microsoft.com/documentation/articles/app-service-vnet-integration-powershell/
[ASEintro]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
