<properties
   pageTitle="Monitoraggio delle risorse soluzione di controllo e protezione della famiglia di prodotti di gestione di operazioni | Microsoft Azure"
   description="In questo documento consente di utilizzare la protezione OMS e le funzionalità di monitoraggio delle risorse e identificare i problemi di sicurezza di controllo."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Monitoraggio delle risorse in operazioni gestione gruppo di sicurezza e soluzione di controllo

In questo documento consente di utilizzare OMS sicurezza e le funzionalità di controllo per eseguire il monitoraggio delle risorse e identificare i problemi di sicurezza.

## <a name="what-is-oms"></a>Che cos'è OMS?

Famiglia di prodotti (OMS, le operazioni di gestione Order) è soluzione di gestione IT che consente di gestire e proteggere le in locale e infrastruttura cloud basato su cloud di Microsoft. Per ulteriori informazioni sulle OMS, leggere l'articolo [Operazioni Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="monitoring-resources"></a>Monitoraggio delle risorse

Ogni volta che è possibile, è possibile evitare problemi di protezione che si verifichino in primo luogo. Tuttavia, non è possibile evitare che tutti i problemi di sicurezza. Quando si verifica un problema di sicurezza, è necessario assicurarsi che l'impatto è ridotto a icona.  Esistono tre consigli critici che possono essere usate per ridurre al minimo l'impatto dei problemi di protezione e il numero:

- Valutare periodicamente le vulnerabilità nel proprio ambiente.
- Controllare regolarmente tutti i sistemi di computer e dispositivi di rete per assicurarsi che siano tutte le patch installate.
- Controllare regolarmente tutti i registri e i meccanismi di registrazione, inclusi i registri eventi di sistema operativo, registri specifici delle applicazioni e log di sistema.

Sicurezza OMS e consente di controllo IT monitorare attivamente tutte le risorse, che consentono di ridurre al minimo l'impatto dei problemi di protezione. Protezione OMS e controllo domini di protezione che possono essere usati per il monitoraggio delle risorse. I domini di sicurezza consente di accedere rapidamente alle opzioni, per il monitoraggio della protezione, verranno contemplati i seguenti domini per altri dettagli:

- Valutazione malware
- Valutazione di aggiornamento
- Identità e accesso

> [AZURE.NOTE] per una panoramica di tutte queste opzioni, leggere [Guida introduttiva a operazioni gestione gruppo di sicurezza e soluzione di controllo](oms-security-getting-started.md).

### <a name="monitoring-system-protection"></a>La protezione del sistema di monitoraggio

In una soluzione di protezione avanzata, ogni livello di protezione è importante per lo stato di protezione generale dell'attività. Computer con minacce rilevate e computer con protezione insufficiente vengono visualizzati nel riquadro dei rischi Malware in domini di protezione. In base alle informazioni sulla valutazione Malware, è possibile identificare un piano per applicare la protezione ai server che voglio. Per accedere a questa opzione, attenersi alla procedura indicata di seguito:

1. Nella **Famiglia di prodotti Microsoft operazioni di gestione dei** dashboard principale fare clic su **sicurezza e controllo** riquadro.

    ![Sicurezza e controllo](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. Il dashboard di **sicurezza e controllo** fare clic su **Rischi Antimalware** in **Domini di protezione**. Dashboard di **Valutazione Antimalware** viene visualizzato come illustrato di seguito:

![Valutazione malware](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

È possibile utilizzare il dashboard di **Valutazione di software dannoso** per identificare i problemi di sicurezza seguenti:

- **Rischi attivi**: computer in cui sono stati compromessi e hanno minacce attive nel sistema.
- **Remediated minacce**: computer in cui sono stati compromessi ma i rischi sono stati applicati i rimedi.
- **Firma è aggiornato**: computer in cui è attivata la protezione da malware, ma la firma non è aggiornato.
- **Nessuna protezione in tempo reale**: computer in cui non è installato antimalware.

### <a name="monitoring-updates"></a>Aggiornamenti di monitoraggio 

Applicare gli aggiornamenti più recenti è una procedura consigliata da incorporare in strategia di gestione dell'aggiornamento. Servizio Microsoft Agent monitoraggio (HealthService.exe) legge Aggiorna le informazioni dal computer monitorate e quindi invia tali informazioni aggiornate al servizio Outlook Mobile nel cloud per elaborazione. Il servizio Microsoft Agent monitoraggio sia configurato come un servizio automatico e deve essere eseguita sempre nel computer di destinazione.

![aggiornamenti di monitoraggio](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

Viene applicata la logica per l'aggiornamento dei dati e il servizio cloud registra i dati. Se sono disponibili aggiornamenti mancanti, vengono visualizzate nel dashboard di **aggiornamenti** . È possibile utilizzare il dashboard di **aggiornamenti** per lavorare con gli aggiornamenti mancanti e sviluppare un piano per applicarle ai server che servono. Seguire la procedura seguente per accedere al dashboard di **aggiornamenti** :

1. Nella **Famiglia di prodotti Microsoft operazioni di gestione dei** dashboard principale fare clic su **sicurezza e controllo** riquadro.
2. Il dashboard di **sicurezza e controllo** fare clic su **Aggiorna valutazione** in **Domini di protezione**. Dashboard di aggiornamento verrà visualizzato come illustrato di seguito:

![valutazione di aggiornamento](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

In questo dashboard è possibile eseguire una valutazione di aggiornamento per comprendere lo stato corrente del computer e minacce più importanti. Utilizzando il riquadro **critica o aggiornamenti della sicurezza** , gli amministratori IT saranno in grado di accedere a informazioni dettagliate sugli aggiornamenti che mancano come illustrato di seguito:

![risultato della ricerca](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

Il report include informazioni importanti che possono essere usate per identificare il tipo di rischio questo sistema è esposto a, che include gli articoli di Microsoft KB associati aggiornamento per la protezione e il Bulletin MS contenente ulteriori dettagli sulla vulnerabilità.

### <a name="monitoring-identity-and-access"></a>Monitoraggio delle identità e accesso

Con utenti che utilizzano da qualsiasi luogo, uso di diversi dispositivi e l'accesso a una grande quantità di App cloud e in locale, è essenziale che le credenziali sono protetti. Credenziali furto attacchi sono quelli in cui un inizialmente hacker accesso alle credenziali degli utenti per accedere a un sistema all'interno della rete. In molti casi, questo attacco iniziale è solo un modo per accedere alla rete, l'obiettivo è individuare account privilegi. 

Gli utenti malintenzionati rimarranno nella rete, utilizzando disponibile gratuitamente utensili per estrarre le credenziali durante le sessioni di altri account connesso. A seconda della configurazione di sistema, è possono estrarre le credenziali sotto forma di hash, biglietti o crittografate pari.  

> [AZURE.NOTE] computer esposti direttamente a Internet vedranno numero eccessivo di tentativi che tentano di accedere con tutti i tipi di nomi utente noti (ad esempio amministratore). Nella maggior parte dei casi è OK se non vengono utilizzati i nomi utente conosciuti e la password è sufficientemente complessa.

È possibile identificare questi intrusi prima che compromettere un account con privilegi. È possibile sfruttare le **OMS sicurezza e la soluzione di controllo** per eseguire il monitoraggio delle identità e accessi. Seguire la procedura seguente per accedere al dashboard di **identità e accessi** :

1. Nel dashboard principale **Famiglia di prodotti Microsoft operazioni di gestione** fare clic su sicurezza e controllo riquadro.
2. Il dashboard di **sicurezza e controllo** fare clic su **identità e accessi** in **Domini di protezione**. Dashboard di **identità e Access** viene visualizzato come illustrato di seguito:

![identità e accesso](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

Come parte di una strategia di monitoraggio regolare, è necessario includere il monitoraggio di identità. Amministratore IT dovrebbe essere se esiste un nome utente valido specifico con numero di tentativi. Questo può indicare uno dei due pirata informatico che acquisito username reale e provare a forzare o uno strumento automatico che utilizza hardcoded password scaduto.

Abilita il dashboard IT per identificare rapidamente i rischi relativi all'identità e all'accesso alle risorse della società. È determinato importante anche identificare le tendenze potenziali, ad esempio nella riquadro accessi nel tempo, è possibile visualizzare in periodo di tempo quante volte un tentativo di accesso non è stato eseguito. In questo caso il computer **FileServer** ricevuto 35 tentativi di accesso. È possibile esplorare ulteriori informazioni su questo computer, fare clic su di esso. 

![Per ulteriori informazioni](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

Il report generato per questo computer porta efficace informazioni dettagliate su questo modello. Notare che la colonna **ACCOUNT** offre l'account utente utilizzato per provare ad accedere al sistema, la colonna **TIMEGENERATED** offre l'intervallo di tempo in cui è stato eseguito il tentativo di e la colonna **LOGONTYPENAME** viene indicato il percorso in cui è stato eseguito il tentativo. Se questi tentativi venga eseguiti in locale nel sistema da un programma, nella colonna nome **processo** sarebbe con il nome del processo. In scenari in cui sarà disponibile a breve il tentativo di accesso da un programma, si dispone già il nome del processo disponibile e a questo punto è possibile eseguire ulteriori indagini nel sistema di destinazione.

## <a name="see-also"></a>Vedere anche

In questo documento è stato illustrato come utilizzare la protezione OMS e controllo soluzione per monitorare le risorse. Per ulteriori informazioni sulla protezione OMS, vedere gli articoli seguenti:

- [Panoramica di gestione famiglia di prodotti (OMS) operazioni](operations-management-suite-overview.md)
- [Guida introduttiva di protezione della famiglia di prodotti di gestione di operazioni e soluzione di controllo](oms-security-getting-started.md)
- [Risposta agli avvisi di sicurezza in operazioni gestione famiglia di prodotti sicurezza e la soluzione di controllo e il monitoraggio](oms-security-responding-alerts.md)