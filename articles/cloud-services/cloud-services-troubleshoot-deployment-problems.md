<properties
 pageTitle="Risolvere i problemi di distribuzione del servizio cloud | Microsoft Azure"
 description="Esistono alcuni problemi comuni che possono verificarsi durante la distribuzione di un servizio cloud in Azure. Questo articolo fornisce le soluzioni ad alcune di esse."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="troubleshoot-cloud-service-deployment-problems"></a>Risolvere i problemi di distribuzione del servizio cloud

Quando si distribuisce un pacchetto dell'applicazione di servizio cloud di Azure, è possibile ottenere informazioni sulla distribuzione dal riquadro delle **proprietà** nel portale di Azure. È possibile utilizzare i dettagli in questo riquadro per la risoluzione dei problemi con il servizio cloud ed è possibile fornire queste informazioni per il supporto di Azure all'apertura di una nuova richiesta di assistenza.

È possibile trovare il riquadro delle **proprietà** come indicato di seguito:

* Nel portale di Azure, fare clic su distribuzione del servizio cloud, fare clic su **tutte le impostazioni**e quindi fare clic su **proprietà**.
* Nel portale di classica Azure, fare clic sulla distribuzione del servizio cloud, fare clic su **DASHBOARD**, nell'angolo inferiore destro della pagina (sotto **occhio**). Tenere presente che non esiste nessuna etichetta "Proprietà" del riquadro.

> [AZURE.NOTE] È possibile copiare il contenuto del riquadro delle **proprietà** negli Appunti facendo clic sull'icona nell'angolo superiore destro del riquadro.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problema: non è possibile accedere il proprio sito Web, ma la distribuzione viene avviata e tutte le istanze di ruolo si sono pronte

Il collegamento al sito Web URL visualizzato nel portale non include la porta. La porta predefinita per i siti Web è 80. Se l'applicazione è configurato per l'esecuzione in una porta diversa, è necessario aggiungere il numero di porta corretto per l'URL per l'accesso al sito Web.

1. Nel portale di Azure, fare clic sulla distribuzione del servizio cloud.
2. Nel riquadro delle **proprietà** del portale di Azure, selezionare le porte per le istanze di ruolo ricoperto dai partecipanti (in **Input endpoint**).
3. Se la porta non è 80, aggiungere il valore di porta corretta all'URL quando si accede all'applicazione. Per specificare una porta non predefinito, digitare l'URL, seguito da due punti (:), seguito dal numero di porta, senza spazi.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problema: Le istanze del ruolo riciclate senza me eseguire operazioni

Servizio correzione verifica automaticamente quando Azure rileva nodi di un problema e pertanto si sposta istanze del ruolo nuovi nodi. In questo caso, è possibile visualizzare le istanze del ruolo riciclaggio automaticamente. Per sapere se servizio correzione si è verificato:

1. Nel portale di Azure, fare clic sulla distribuzione del servizio cloud.
2. Nel riquadro delle **proprietà** del portale di Azure, esaminare le informazioni e determinare se servizio correzione durante il periodo di tempo osservato di ruoli riciclaggio.

Ruoli verranno anche Cestino circa una volta al mese nel corso del sistema operativo host e aggiornamenti del sistema operativo guest.  
Per ulteriori informazioni, vedere il post del blog [Ruolo istanza riavvia scadenza agli aggiornamenti del sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problema: Impossibile eseguire scambio VIP e ricevere un messaggio di errore

Scambio VIP non consentito se è in corso un aggiornamento di distribuzione. La distribuzione degli aggiornamenti può avvenire automaticamente quando:

* È disponibile un nuovo sistema operativo guest e sia configurata per gli aggiornamenti automatici.
* Correzione Service si verifica.

Per sapere se un aggiornamento automatico che impedisce eseguendo scambio VIP:

1. Nel portale di Azure, fare clic sulla distribuzione del servizio cloud.
2. Nel riquadro delle **proprietà** del portale di Azure, osservare il valore di **stato**. Se è **pronti**, quindi selezionare l' **ultima operazione** per controllare se una recente verificato un problema che potrebbero impedire Scambia VIP.
3. Ripetere i passaggi 1 e 2 per la distribuzione di produzione.
4. Se un aggiornamento automatico è in corso, attendere che finisca, prima di provare a eseguire lo scambio di VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problema: Un'istanza del ruolo è ciclo continuo tra attività iniziale, inizializzazione, occupato e arrestato

Questa condizione sia verificato un problema con i file di codice, pacchetto o configurazione dell'applicazione. In questo caso, dovrebbe essere possibile visualizzare lo stato modifica alcuni minuti e il portale di Azure è possibile che un elemento come **riciclo**, **occupato**o **inizializzazione in corso**. Indica che ci sono verificati problemi con l'applicazione che mantiene l'istanza di ruoli esecuzione.

Per ulteriori informazioni su come risolvere il problema, vedere il blog pubblicare [Dati di diagnostica calcolare PaaS Azure](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) e [problemi che causano ruoli al Cestino](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problema: L'applicazione ha smesso di funzionare

1. Nel portale di Azure, fare clic su istanza del ruolo.
2. Nel riquadro delle **proprietà** del portale di Azure, considerare le condizioni seguenti per risolvere il problema:
   * Se l'istanza di ruoli ha smesso di recente (è possibile controllare il valore di **Conteggio interruzioni**), aggiornamento di distribuzione. Attendere per vedere se l'istanza di ruoli riprende il funzionamento autonomamente.
   * Se l'istanza di ruolo è **occupato**, controllare il codice di applicazione per verificare se l'evento [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) viene gestito. Potrebbe essere necessario aggiungere o risolvere il codice che gestisce l'evento.
   * Passare tra i dati di diagnostica e risoluzione dei problemi scenari post di [Dati di Azure PaaS calcolare diagnostica](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

>[AZURE.WARNING] Se si riavviare il servizio cloud, è reimpostare le proprietà per la distribuzione, in modo efficace cancellare le informazioni per il problema originale.

## <a name="next-steps"></a>Passaggi successivi

Visualizzare altri [articoli di risoluzione dei problemi](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) per i servizi cloud.

Per informazioni su come risolvere i problemi relativi al ruolo ricoperto dai partecipanti servizio cloud utilizzando i dati di diagnostica di Azure PaaS computer, vedere [serie di blog di Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
