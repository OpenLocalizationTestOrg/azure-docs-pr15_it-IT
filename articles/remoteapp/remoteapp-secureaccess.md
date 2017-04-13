
<properties 
    pageTitle="Protezione dell'accesso a RemoteApp di Azure e oltre | Microsoft Azure"
    description="Informazioni su come proteggere l'accesso al Azure RemoteApp tramite accesso condizionato di Azure Active Directory"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="securing-access-to-azure-remoteapp-and-beyond"></a>Protezione dell'accesso a RemoteApp di Azure e oltre

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

In questo articolo si otterranno una panoramica di come un amministratore può configurare un canale l'accesso sicuro partire dall'utente, mediante RemoteApp Azure e termina con una risorsa protetta, ad esempio un database SQL o un'altra applicazione back-end. L'obiettivo è per assicurarsi che soddisfano le condizioni desiderate solo gli utenti autorizzati possano accedere applicazioni remote e che back-end protetto è accessibile solo dall'ambiente di Azure RemoteApp controllato e non da altre posizioni.

Sono disponibili 3 aree principali, che l'amministratore deve esaminare:

![Considerazioni di accesso condizionato RemoteApp Azure](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

Lettura per informazioni e le risposte a queste domande.

## <a name="who-can-access-the-collection"></a>Chi può accedere alla raccolta?
L'amministratore sceglie gli utenti che possono accedere applicazioni remote nella raccolta. È possibile utilizzare Azure Active Directory (Azure Active Directory) account aziendali o dell'istituto di istruzione (precedentemente denominati, "account aziendale") o account Microsoft (ad esempio @outlook.com). La maggior parte dei scenari aziendali usare gli account Azure Active Directory; consentono di utilizzare access condizionale funzionalità illustrate in un secondo momento e sono anche la scelta sola per gli insiemi di dominio. Il resto dell'articolo si presuppone che si utilizza account Azure Active Directory con RemoteApp di Azure.

**Cosa è stata effettuata:**

Utilizzando account Azure Active Directory per controllare l'accesso a RemoteApp di Azure offre due fattori:

1.  È possibile sapere sempre chi può accedere le applicazioni è stata pubblicata e accedere a qualsiasi back-end tali applicazioni connettono.
2.  È possibile controllare sottostante Azure AD per consentire è possibile creare ed eliminare gli account utente, impostare i criteri di password, utilizzano l'autenticazione a più fattori e così via. 

## <a name="how-is-the-collection-accessed-from-where"></a>Come si accede alla raccolta? Da dove?
In genere amministratori è utile definire i criteri per l'accesso a un ambiente pubblico esposto a Internet, ad esempio RemoteApp di Azure. Ad esempio, si desidera assicurarsi che gli utenti che accedono ambiente da all'esterno della rete aziendale sono necessario utilizzare autenticazione a più fattori (MFA) per accedere; o forse deve bloccati completamente.

Gli amministratori di RemoteApp Azure possono utilizzare le funzionalità disponibili tramite Azure Active Directory Premium per impostare i criteri di accesso condizionato per il proprio ambiente RemoteApp di Azure. Sono inoltre possibile utilizzare RTF report e gli avvisi di caratteristiche per controllare come si accede l'ambiente.

### <a name="how-to-set-up-conditional-access-for-azure-remoteapp"></a>Come configurare l'accesso condizionato per RemoteApp di Azure
Verranno scorrere uno scenario di esempio: RemoteApp Azure amministratore desidera bloccare l'accesso all'ambiente quando gli utenti sono all'esterno della rete aziendale.

>[AZURE.NOTE] Si presuppone è stato eseguito l'aggiornamento di Azure Active Directory al livello Premium e che sia stato creato almeno una raccolta di Azure RemoteApp.

1.  Nel portale di Azure fare clic sulla scheda **Active Directory** . Fare clic su directory che si desidera configurare.

    Tenere presenti: Accesso condizionato è una proprietà di directory e non di Azure RemoteApp, in modo che tutti configurazione è stata completata livello di directory. Questo significa anche è necessario essere l'amministratore della directory per apportare le modifiche.

2.  Fare clic su **applicazioni**e quindi fare clic su **Microsoft Azure RemoteApp** per configurare l'accesso condizionato. Si noti che è possibile impostare access condizionale per ogni applicazione di "software come servizio" nella directory separatamente.
![Impostazione dell'accesso condizionale per RemoteApp di Azure](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
 

3.  Nella scheda **Configura** impostare **Abilitare le regole di accesso** su ON.
![Abilitare le regole di accesso per RemoteApp di Azure](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
 

4.  È ora possibile configurare diverse regole e scegliere la persona a cui applicare:

    1. Scegliere **bloccare l'accesso quando non al lavoro** per impedire agli utenti di accedere Azure RemoteApp di fuori dell'ambiente di rete specificato.
    2. Fare clic sull'opzione riportata di seguito per definire gli intervalli di indirizzi IP che costituiscono la rete"attendibile". Tutti gli elementi all'esterno di quelli verrà rifiutato.

5.  Verificare la configurazione avviando il client di Azure RemoteApp da un indirizzo IP di fuori dell'intervallo specificato. Al termine accedere con le credenziali di Azure Active Directory verrà visualizzato un messaggio simile al seguente:

![Accesso negato a RemoteApp di Azure](./media/remoteapp-secureaccess/ra-accessdenied.png)
 

### <a name="future-conditional-access-features"></a>Caratteristiche di accesso condizionale futuri 
Nuove funzionalità di accesso condizionato sta lavorando il team di Azure Active Directory. Gli amministratori saranno in grado di creare nuovi tipi di regole oltre rete regole in base a posizione. Breve, un'anteprima delle nuove funzionalità pubblica dovrebbe essere disponibile.

### <a name="how-to-monitor-access-to-azure-remoteapp"></a>Come controllare l'accesso a RemoteApp di Azure
Un'ottima funzionalità da usare insieme a accesso condizionato è la funzionalità di creazione di report di Azure Active Directory Premium. È possibile utilizzare i report per monitorare chi accede al proprio ambiente e rilevare eventuali attività sospetto.

Ad esempio, è possibile visualizzare i nomi degli utenti per accedere RemoteApp di Azure, quante volte si ha e quando.

1.  Nel portale di Azure, fare clic su **Active Directory**e quindi fare clic su directory.

2.  Fare clic sulla scheda **report** .

3.  Elenco di report, selezionare **l'uso dell'applicazione** in **applicazioni integrate**.

    Si noterà alcune statistiche aggregato per RemoteApp di Azure. 
![Statistiche di accesso aggregati RemoteApp di Azure](./media/remoteapp-secureaccess/ra-accessstats.png)
 
5.  Fare clic su un'applicazione di fornire informazioni esaustive sugli utenti che accedono RemoteApp di Azure.
![Statistiche di accesso utente per RemoteApp di Azure](./media/remoteapp-secureaccess/ra-userstats.png)
 
### <a name="summary"></a>Riepilogo
Con Azure Active Directory Premium è possibile impostare regole di accesso a RemoteApp di Azure (e altro software come applicazioni di servizio disponibili tramite Azure Active Directory). Le regole sono attualmente limitate ai criteri di rete in base a posizione, ma in futuro essere esteso ad altri aspetti della gestione aziendale.

Azure Active Directory Premium offre reporting e il monitoraggio delle funzionalità che estendono ulteriormente il controllo l'amministratore ha sull'ambiente RemoteApp di Azure.

## <a name="how-do-i-make-sure-my-secure-resource-is-accessible-only-from-my-azure-remoteapp-environment"></a>Come verificare che la risorsa protetta sia accessibile solo da dell'ambiente operativo RemoteApp di Azure
Nelle sezioni precedenti di questo articolo è incentrato sulla protezione dell'accesso all'ambiente di Azure RemoteApp. Abbiamo risultato che ottenuto scegliendo gli utenti sono autorizzati ad accedere e configurare le regole di accesso per controllare ulteriormente come utilizzare il servizio.

Uno scenario comune per le distribuzioni di Azure RemoteApp è che è necessario comunicare con una risorsa di back-end, ad esempio un database SQL di applicazioni remote. Questa risorsa è ospitata in locale (ad esempio in una rete aziendale) o nel cloud (ad esempio in Azure IaaS). Gli amministratori spesso desidera assicurarsi che la risorsa back-end accessibile solo dalle applicazioni distribuite tramite RemoteApp di Azure e non, ad esempio da un'applicazione direttamente nel PC dell'utente e l'accesso a Internet pubblica. Azure RemoteApp spesso è considerato come l'ambiente protetto e gestiti centralmente e quindi il percorso solo mediante il quale gli utenti devono interagire con la risorsa back-end.

La soluzione consiste nell'inserire l'ambiente di Azure RemoteApp e la risorsa sicura nella stessa Azure virtuale rete (VNET). Se la risorsa in un altro sito, è possibile stabilire una connessione VPN da sito, ad esempio per creare un VNet che si estendono per il centro dati Azure e l'ambiente locale cliente.

Azure RemoteApp supporta due tipi di distribuzioni raccolta in cui è possibile immettere il proprio VNET:

-   Non appartenenti a un dominio: le applicazioni di saranno "linea di duca" di altre risorse di VNET. Ad esempio, può essere utilizzato per connettere le applicazioni a un database SQL che utilizza l'autenticazione di SQL (applicazioni autenticano dell'utente direttamente nel database)

-   Dominio: macchine virtuali di utilizzato da Azure RemoteApp fanno parte di un controller di dominio nel VNET. In questo modo le applicazioni necessarie per l'autenticazione per un Controller di dominio di Windows per ottenere accesso a una risorsa di back-end.
![Un insieme di dominio in RemoteApp di Azure](./media/remoteapp-secureaccess/ra-domainjoined.png)
 
### <a name="how-to-create-a-secure-connection-between-azure-and-my-on-premises-environment"></a>Come creare una connessione protetta tra Azure e dell'ambiente operativo locale
Sono disponibili diverse opzioni di configurazione per la connessione ambienti Azure e locale. Di seguito, è disponibile un'ampia panoramica delle opzioni.

Con Azure RemoteApp è necessario configurare i VNet prima di tutto e quindi utilizzare durante il processo di creazione della raccolta. 

## <a name="the-complete-solution"></a>La soluzione completa
Il diagramma seguente illustra la soluzione completa in cui è abbiamo incorporata un canale l'accesso sicuro dall'utente, tramite RemoteApp Azure (ARA), la risorsa back-end.
![Proteggere Azure RemoteApp](./media/remoteapp-secureaccess/ra-secureoverview.png) nella fase 1 abbiamo selezionato gli utenti e creare regole di accesso che determinano come è possibile accedere ARA. Nell'esempio seguente è consentire solo l'accesso per gli utenti che utilizzano dalla rete aziendale. Gli utenti non compatibili non sarà possibile accedere all'ambiente di ARA tutto.
In "Fase 2" esposta la risorsa back-end solo tramite la configurazione di VNet/VPN che viene controllata. Azure RemoteApp è posizionata nella stessa VNet. Il risultato finale è che la risorsa è accessibile solo tramite l'ambiente ARA.


