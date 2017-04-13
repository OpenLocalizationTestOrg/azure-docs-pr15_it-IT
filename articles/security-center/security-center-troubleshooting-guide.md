<properties
   pageTitle="Guida alla risoluzione dei Azure Security Center | Microsoft Azure"
   description="In questo documento consente di risolvere i problemi in Centro protezione di Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di Centro protezione di Azure
Questa guida è rivolta professionisti IT (IT), gli analisti della sicurezza di informazioni e amministratori di cloud cui organizzazioni usano Azure Security Center ed è necessario per la risoluzione dei che problemi relativi all'interfaccia di sicurezza.

## <a name="troubleshooting-guide"></a>Guida alla risoluzione dei problemi
Questa guida viene spiegato come risolvere i problemi di Centro protezione. La maggior parte della risoluzione dei problemi eseguite in Centro protezione avranno luogo esaminando prima i record di [Log di controllo](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) per il componente non riuscito. Tramite i log di controllo, è possibile determinare:

- Sono state eseguite le operazioni
- Chi ha iniziato l'operazione
- Quando si è verificato l'operazione
- Lo stato dell'operazione
- L'operazione di ricerca di valori di altre proprietà che possono risultare utili

Il log di controllo contiene tutte le operazioni di scrittura (inserire, POST, DELETE) svolto delle risorse, ma non include operazioni di lettura (GET).

## <a name="troubleshooting-monitoring-agent-installation-in-windows"></a>Risoluzione dei problemi di installazione dell'agente di monitoraggio di Windows

Centro protezione agente di monitoraggio viene utilizzato per eseguire la raccolta di dati. Dopo la raccolta di dati è abilitata e l'agente correttamente è installato nel computer di destinazione, questi processi devono essere in esecuzione:

- ASMAgentLauncher.exe - Azure agente di monitoraggio 
- ASMMonitoringAgent.exe - estensione il monitoraggio della protezione Azure
- ASMSoftwareScanner.exe – analisi Azure Manager

L'estensione di monitoraggio di sicurezza di Azure analizza per diversi protezione pertinenti e consente di raccogliere i registri di protezione dalla macchina virtuale. Gestione di analisi da utilizzare come uno scanner patch.

Se l'installazione viene eseguita correttamente è necessario verificare una voce simile a quello sotto i log di controllo per la destinazione macchine Virtuali:

![Log di controllo](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

È inoltre possibile ottenere altre informazioni sul processo di installazione, leggere i registri agente che si trova in *%systemdrive%\windowsazure\logs* (esempio: C:\WindowsAzure\Logs).

> [AZURE.NOTE] Se l'agente di Centro protezione di Azure funzionamento anomalo, sarà necessario riavviare la destinazione macchine Virtuali poiché non esiste alcun comando per arrestare e riavviare l'agente.

## <a name="troubleshooting-monitoring-agent-installation-in-linux"></a>Risoluzione dei problemi di installazione dell'agente di monitoraggio in Linux
Quando la risoluzione dei problemi di installazione dell'agente di macchine Virtuali in un sistema Linux è necessario assicurarsi che sia stato scaricato l'estensione a/var raccolta/waagent /. È possibile eseguire il comando seguente per verificare se è stato installato:

`cat /var/log/waagent.log` 

Altri file di registro che è possibile esaminare per la risoluzione dei problemi scopo sono: 

- /var/log/mdsd.Err
- var/log/azure /

In un sistema di lavoro verrà visualizzata una connessione al processo mdsd su TCP 29130. Verrà visualizzata la comunicazione con il processo di mdsd il Registro di sistema. È possibile convalidare il problema, eseguire il comando seguente:

`netstat -plantu | grep 29130`

## <a name="contacting-microsoft-support"></a>Contattare il supporto tecnico

Alcuni problemi possono essere identificati mediante le linee guida forniti in questo articolo, gli altri utenti che è inoltre possibile trovare descritte al [Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)pubblico Security Center. Tuttavia se è necessaria ulteriore risoluzione dei problemi, è possibile aprire una nuova richiesta di assistenza tramite il portale di Azure, come illustrato di seguito: 

![Supporto tecnico Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Vedere anche

In questo documento è stato descritto come configurare i criteri di sicurezza in Centro protezione di Azure. Per ulteriori informazioni sul Centro protezione di Azure, vedere gli articoli seguenti:

- [Guida e pianificazione della Centro protezione di Azure](security-center-planning-and-operations-guide.md) , informazioni su come pianificare e si conoscono le considerazioni di progettazione per l'adozione di Azure Security Center.
- [Monitoraggio di protezione in Centro protezione di Azure](security-center-monitoring.md) , informazioni su come controllare lo stato delle risorse di Azure
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) , informazioni su come gestire e risposta agli avvisi di sicurezza
- [Monitoraggio delle soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) -informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di Azure](security-center-faq.md) , ovvero trova domande frequenti sull'utilizzo del servizio
- [Blog sulla protezione di Azure](http://blogs.msdn.com/b/azuresecurity/) , trovare post di blog sulla protezione di Azure e conformità
