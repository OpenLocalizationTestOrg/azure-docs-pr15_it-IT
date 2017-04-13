<properties 
   pageTitle="Modificare la configurazione del dispositivo StorSimple | Microsoft Azure" 
   description="In questo articolo viene descritto come utilizzare il servizio di gestione di StorSimple per riconfigurare un dispositivo StorSimple che è già stato distribuito." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="09/29/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>Utilizzare il servizio di gestione StorSimple per modificare la configurazione di dispositivi StorSimple

## <a name="overview"></a>Panoramica 

Il portale classico Azure pagina **Configura** contiene tutti i parametri di dispositivo che è possibile riconfigurare su un dispositivo StorSimple che viene gestito da un servizio di gestione StorSimple. In questa esercitazione viene illustrato come è possibile utilizzare la pagina **Configura** per eseguire le attività di livello dispositivo seguenti:

- Modificare le impostazioni del dispositivo 
- Modificare le impostazioni di tempo 
- Modificare le impostazioni DNS 
- Modificare le interfacce di rete
- Sostituire o riassegnare IP

## <a name="modify-device-settings"></a>Modificare le impostazioni del dispositivo

Le impostazioni dispositivo includono il nome del dispositivo e la descrizione del dispositivo.

Un dispositivo StorSimple connesso al servizio di gestione StorSimple viene assegnato un nome predefinito. Il nome predefinito è in genere corrisponde al numero seriale del dispositivo. Ad esempio un nome di dispositivo predefinito che 15 caratteri, ad esempio 8600-SHX0991003G44HT indica quanto segue:

- **8600** – indica il modello.
- **SHX** : indica il sito di produzione.
- **0991003** - indica un prodotto specifico.
- **G44HT**- ultimi 5 cifre sono incrementate per creare i numeri di serie univoci. Questo potrebbe non essere un set di tipo sequenza.

È possibile utilizzare il portale classico Azure per modificare il nome del dispositivo e assegnare un nome univoco per la scelta. Il nome può contenere i caratteri e può contenere un massimo di 64 caratteri.

È inoltre possibile specificare una descrizione del dispositivo. Una descrizione di dispositivo in genere consente di identificare il proprietario e la posizione fisica del dispositivo. Il campo descrizione deve contenere meno di 256 caratteri.
 
## <a name="modify-time-settings"></a>Modificare le impostazioni di tempo

Il dispositivo deve sincronizzare l'ora per l'autenticazione con il provider di servizi di archiviazione cloud. Selezionare il fuso orario dall'elenco a discesa e specificare fino a due server di rete NTP (Time Protocol). Il server NTP primario è necessario e viene specificato quando si utilizza Windows PowerShell per StorSimple per configurare il dispositivo. È possibile specificare il Server di Windows predefinito **time.windows.com** come server NTP. È possibile visualizzare la configurazione del server NTP primaria tramite il portale classica Azure, ma è necessario utilizzare l'interfaccia di Windows PowerShell per modificarlo.

La configurazione del server NTP secondaria è facoltativa. È possibile usare il portale classico per configurare un server NTP secondario. 

Quando si configura il server NTP, accertarsi che la rete consenta il traffico NTP passare dal centro dati a Internet. Quando si specifica un server NTP pubblico, è necessario assicurarsi che siano configurati firewall della rete e altri dispositivi di sicurezza per consentire il traffico NTP viaggio da e verso rete esterna. Se non è consentito il traffico NTP bidirezionale, è necessario utilizzare un server NTP interno (un controller di dominio Windows fornisce questa funzione). Se il dispositivo non è possibile sincronizzare l'ora, potrebbe non essere possibile comunicare con il provider di archiviazione cloud.

Per visualizzare un elenco di server NTP pubblici, passare a [NTP server Web](http://support.ntp.org/bin/view/Servers/WebHome). 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Cosa accade se il dispositivo viene distribuito in un fuso orario diverso?

Se il dispositivo viene distribuito in un fuso orario diverso, verrà modificato il fuso orario dispositivo. Dato che tutti i criteri di backup usare il dispositivo fuso orario, secondo il nuovo fuso orario verranno regolato automaticamente i criteri di backup. Non è necessario alcun intervento dell'utente.

## <a name="modify-dns-settings"></a>Modificare le impostazioni DNS

Viene utilizzato un server DNS quando il dispositivo tenta di comunicare con il provider di servizi di archiviazione cloud. Disponibilità elevata, è necessario configurare primario e i server DNS secondari durante la distribuzione di dispositivo iniziale. Per riconfigurare il server DNS, sarà necessario usare l'interfaccia di Windows PowerShell nel dispositivo StorSimple.

Per modificare i server DNS secondario, è possibile usare il portale classico Azure.



## <a name="modify-network-interfaces"></a>Modificare le interfacce di rete

Il dispositivo ha sei dispositivo le interfacce di rete, quattro delle quali sono 1 GbE e due delle quali sono GbE 10. Queste interfacce sono contrassegnate come dati 0-5 di dati. DATI 0, 1 di dati, dati 4 e 5 dati sono 1 GbE, mentre i dati 2 e 3 dati sono interfacce di rete GbE 10.

Configurare **Le impostazioni dell'interfaccia di rete** per ognuna delle interfacce da utilizzare. Per garantire disponibilità, è consigliabile avere almeno due interfacce iSCSI e due interfacce basate su cloud nel dispositivo. È consigliabile ma non richiedono disattivate le interfacce inutilizzate.

Quando si configura le interfacce di rete, è necessario configurare un IP virtuale (VIP).

DATI 0 sono abilitato cloud per impostazione predefinita. Quando si configura dati 0, anche è necessario configurare due indirizzi IP fissi, uno per ogni controller. Questi indirizzi IP fissi possono essere usati per accedere direttamente a controller di dispositivo e sono utili durante l'installazione degli aggiornamenti del dispositivo o quando si accede a controller per la risoluzione dei problemi.

In StorSimple 8000 serie aggiornamento 1, viene impostata la metrica di routing dei dati 0 al valore più basso; Pertanto, se il dispositivo è in esecuzione StorSimple 8000 serie aggiornamento 1, tutto il traffico cloud instradato tramite dati 0. Se si dispone di più interfacce di rete basate su cloud sul dispositivo StorSimple, prendere nota dell'oggetto.

>[AZURE.NOTE] Indirizzi IP fissi per il controller vengono utilizzati per gestire gli aggiornamenti al dispositivo. Di conseguenza, gli indirizzi IP fissi devono essere routing e grado di connettersi a Internet.

Per ogni interfaccia di rete, vengono visualizzati i parametri seguenti:

- **Velocità** : non è un parametro configurabile dall'utente. DATI 0, 1 di dati, dati 4 e 5 dati sono sempre 1 GbE, mentre i dati 2 e 3 dati sono 10 GbE interfacce.

     >[AZURE.NOTE] Velocità e fronte/retro sono sempre automatico trasferibile. Immagine del frame non sono supportati.
 
- **Stato interfaccia** : un'interfaccia può essere abilitata o disabilitata. Se è attivato, il dispositivo tenterà di usare l'interfaccia. È consigliabile attivare solo le interfacce connessi alla rete e utilizzate. Disattivare tutte le interfacce che non si siano utilizzando.

- **Tipo di interfaccia** : questo parametro consente di isolare il traffico iSCSI dal traffico di archiviazione cloud. Questo parametro può essere una delle operazioni seguenti:

    - **Cloud abilitato** – quando abilitate, il dispositivo utilizzerà questa interfaccia per comunicare con il cloud.
    - **iSCSI abilitato** – quando abilitato, il dispositivo utilizzerà l'interfaccia per comunicare con l'host iSCSI.

    È consigliabile isolare il traffico iSCSI dal traffico di archiviazione cloud. Nota anche se l'host è all'interno della stessa subnet del dispositivo, non è necessaria assegnare un gateway. Tuttavia, se l'host è in una subnet diversa del dispositivo, è necessario assegnare un gateway.

- **Indirizzo IP** -può trattarsi di IPv4 o IPv6 o entrambi. Famiglie di indirizzi IPv4 e IPv6 sono supportate per le interfacce di rete del dispositivo. Quando si utilizza IPv4, specificare un indirizzo IP versione a 32 bit (*xxx.xxx*) in notazione punti decimali. Quando si usa IPv6, è sufficiente fornire un prefisso a 4 cifre e un indirizzo a 128 bit verrà generato automaticamente per l'interfaccia di rete di dispositivo in base a tale prefisso.

- **Subnet** – si fa riferimento alla maschera subnet ed è configurato tramite l'interfaccia di Windows PowerShell.

- **Gateway** : si tratta del gateway predefinito che deve essere utilizzato da questa interfaccia quando si tenta di comunicare con i nodi che non sono contenuti lo stesso spazio indirizzo IP (subnet). Il gateway predefinito deve essere nello stesso spazio di indirizzi (subnet), come l'interfaccia indirizzo IP, in base a maschera subnet.

- **Indirizzo IP fisso** : questo campo è disponibile solo quando si configura i dati 0 interfaccia. Per le operazioni, ad esempio gli aggiornamenti o il dispositivo di risoluzione dei problemi, è necessario connettersi direttamente al controller di dispositivo. L'indirizzo IP fisso può essere utilizzato per accedere attiva e passivo controller nel dispositivo.

È possibile riconfigurare Controller 0 e 1 Controller tramite il portale classico Azure.

>[AZURE.NOTE] 
>
>- Per garantire il corretto funzionamento, verificare che la velocità dell'interfaccia e fronte/retro interruttore ogni interfaccia dispositivo sia connesso a. Opzione interfacce devono essere negoziare con o essere configurate per Gigabit Ethernet (1000 Mbps) e bidirezionale. Interfacce operativo alla velocità o in fronte/retro metà determinerà i problemi di prestazioni.
>
>- Per ridurre al minimo il tempo di inattività e interruzioni, è consigliabile attivare portfast su ciascuna delle porte parametro che si connettono a interfaccia di rete iSCSI del dispositivo. In questo modo che sia possibile stabilire rapidamente la connettività di rete invariato.
 
## <a name="swap-or-reassign-ips"></a>Sostituire o riassegnare IP

Attualmente, se qualsiasi interfaccia di rete sul controller viene assegnato un indirizzo VIP in uso (in base alla stesso dispositivo o un altro dispositivo nella rete), quindi il controller avrà esito negativo su. È necessario seguire la procedura appropriata se si scambio VIP per l'interfaccia di rete del dispositivo, perché è necessario creare una situazione IP duplicata.

Per sostituire o riassegnare VIP per le interfacce di rete, procedere come segue:

#### <a name="to-reassign-ips"></a>Riassegnare IP

1. Deselezionare l'indirizzo IP per entrambe le interfacce.

2. Dopo che gli indirizzi IP siano deselezionati, assegnare i nuovi indirizzi IP alle rispettive interfacce.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [configurare MPIO del dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).

- Informazioni su come [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
     
