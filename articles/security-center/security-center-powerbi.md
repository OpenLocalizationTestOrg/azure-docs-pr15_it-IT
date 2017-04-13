<properties
   pageTitle="Ottenere informazioni approfondite dai dati del Centro protezione di Azure con Power BI | Microsoft Azure"
   description="Il language pack contenuto di Centro protezione di Azure Power BI è semplice trovare gli avvisi di sicurezza, suggerimenti, attacchi risorse e le tendenze, in base a un set di dati che è stato creato per il report."
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
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="get-insights-from-azure-security-center-data-with-power-bi"></a>Ottenere informazioni approfondite dai dati del Centro protezione di Azure con Power BI
[Dashboard di Power BI](http://aka.ms/azure-security-center-power-bi) per Azure Security Center consente di visualizzare, analizzare e filtrare consigli e gli avvisi di sicurezza da qualunque dispositivo, incluso un dispositivo mobile. Utilizzare il dashboard di Power BI per rivelare le tendenze e attacchi motivi - Visualizza gli avvisi di sicurezza delle risorse o indirizzo IP di origine e giornalismo i rischi di sicurezza delle risorse o età. 

È possibile anche eseguire il mashup consigli nel Centro sicurezza e gli avvisi di sicurezza con altri dati in modi interessanti, ad esempio utilizzando i dati di [Azure i log di controllo](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) e [Il controllo dei Database SQL di Azure](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/). Offrono Rz ed è anche possibile esportare dati in Excel per semplici report sullo stato di protezione delle risorse cloud.

##<a name="using-azure-security-center-dashboard-to-access-power-bi"></a>Utilizzo del dashboard di Azure Security Center per accedere a Power BI
È anche possibile utilizzare dashboard dell'interfaccia di sicurezza di Azure per accedere ai report di Power BI. Seguire la procedura per eseguire questa operazione: 

1. Il dashboard di **Azure Security Center** fare clic su pulsante **esplorazione in Power BI** .

    ![Connettersi al Centro protezione di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new10.png) 

2. E **l'Esplora in Power BI** apre sul lato destro, come illustrato nell'immagine seguente:

    ![Connettersi al Centro protezione di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new2.png)

3. Se si sta creando dashboard di Power BI per la prima volta, è possibile scegliere una delle opzioni seguenti e **l'Esplora in Power BI** : 

    - **Dashboard di sicurezza approfondimenti**: scegliere questa opzione se si desidera creare un dashboard che include lo stato di sicurezza, thread e rilevamenti. Questa opzione è più comuni per il ruolo attrezzi responsabile per analizzare lo stato di protezione che rilevato avvisi abbonamenti.
    - **Dashboard di gestione dei criteri**: scegliere questa opzione se si vuole esplorare i criteri di gestione e l'applicazione.  Questa opzione è più comuni per IT centrale che sono maggiormente l'attenzione sui governance. Questo dashboard consente di ottenere informazioni approfondite sulla conformità di criteri di sicurezza e la visibilità in tutta l'organizzazione.
    - Se si dispone già di un dashboard di Power BI, fare clic su **Vai al dashboard di Power BI corrente**.

4. In questo esempio, fare clic su **dashboard approfondimenti sicurezza** opzione. Se si tratta la prima volta, si sta creando un dashboard di Power BI per il Centro protezione viene richiesto di installare il language pack contenuto. Fare clic su pulsante **visualizzato** nella finestra di **pacchetti di contenuto per Power BI** come illustrato nell'immagine seguente:

    ![Dashboard di sicurezza Centro protezione approfondimenti Azure](./media/security-center-powerbi/security-center-powerbi-fig1-new3.png)

5. Visualizzata la finestra di **connettersi a informazioni dettagliate di Azure sicurezza Centro protezione** . Verificare che il metodo di **autenticazione** sia **oAuth2** come illustrato di seguito e fare clic su **Accedi** .
    
    ![Autenticazione](./media/security-center-powerbi/security-center-powerbi-fig1-new4.png)

6. Potrebbe essere necessario eseguire l'autenticazione nuovamente con le credenziali di Azure. Dopo l'autenticazione del dashboard verrà creato. Dopo aver creato il dashboard è visualizzato un rapporto con una struttura analoga, come illustrato nell'immagine seguente:

    ![Dashboard di Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new5.png)


> [AZURE.NOTE] Un aggiornamento del report è programmato su base giornaliera. Nel caso in cui si verifica un errore in questo caso di aggiornamento, leggere [Potenziali problemi durante l'aggiornamento dei Azure Security Center Power BI](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/), per ulteriori informazioni su come risolvere i problemi.

Qui è possibile visualizzare il numero di avvisi di sicurezza e suggerimenti, oltre che il numero di macchine virtuali, database SQL Azure e risorse di rete che si sta controllando dal Centro protezione di Azure.

Un collegamento a Centro protezione di Azure si viene reindirizzati al portale di Azure. I tipi di grafici rendono più semplice visualizzare informazioni sulla protezione raccomandazioni e avvisi, tra cui:

- Stato di protezione delle risorse
- Suggerimenti in sospeso
- Consigli macchine Virtuali
- Avvisi nel tempo
- Risorse attaccate
- IP di attaccato

Dietro ogni grafico sono disponibili le informazioni supplementari. Selezionare un riquadro per visualizzare altre informazioni. Ad esempio, il riquadro di **Stato di protezione delle risorse** sono indicati ulteriori dettagli in sospeso consigli dalle risorse come illustrato nell'immagine seguente:

![Consigli](./media/security-center-powerbi/security-center-powerbi-fig1-new6.png)

Se si fa clic su tutte le righe di questo grafico, gli altri intende inattiva e di focalizzare l'attenzione solo su quella selezionata. Per tornare al dashboard, fare clic su **Centro protezione di Azure** sotto l'opzione di **Dashboard** nel riquadro sinistro della pagina.

> [AZURE.NOTE] Se si preferisce personalizzare i report mediante l'aggiunta di campi aggiuntivi o modificare elementi visivi esistenti, è possibile modificare il report. Per ulteriori informazioni, vedere [interagire con un report in visualizzazione di modifica di Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) .

Quando si fa clic su ciascuna delle, i riquadri **avvisi nel tempo, attacchi risorse** e **Indirizzi IP pirata informatico** avrà l'output simile. Si verifica questo evento poiché il report aggrega informazioni relative a questi tre variabili e chiama **risorse attacco** come illustrato nell'immagine seguente:

![Attacco e le risorse](./media/security-center-powerbi/security-center-powerbi-fig1-new7.png)

A questo punto è possibile salvare una copia del report, stamparlo o pubblicarlo sul web utilizzando le opzioni disponibili nel menu **File** .

![Menu file](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## <a name="exploring-your-azure-security-center-data-with-power-bi-services"></a>Esplorare i dati di Centro protezione di Azure con servizi di Power BI

Connettersi ai [Servizi Pack contenuto di Power BI](https://msit.powerbi.com/groups/me/getdata/services) in Power BI ed eseguire la procedura seguente:

1. Nella finestra **Contenuto Pack per Power BI** vengono visualizzate due opzioni come illustrato di seguito.

    ![Pacchetto di contenuti per Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

    >[AZURE.NOTE] Già eseguito la prima parte di questo articolo verranno visualizzati solo una delle opzioni, ovvero Gestione criteri di Centro protezione di Azure.

2. In questo esempio, fare clic su **Guida** nella sezione **Gestione criteri di Centro protezione di Azure** .

3. Nella finestra di **connettersi alla gestione dei criteri di Centro protezione di Azure** , assicurarsi di selezionare **oAuth2** in **Metodo di autenticazione** a discesa come mostrato sotto e fare clic su **Accedi** .

    ![Finestra di gestione dei criteri](./media/security-center-powerbi/security-center-powerbi-fig1-new8.png)

4. Si verrà reindirizzati a una pagina di autenticazione nel punto in cui è necessario digitare le credenziali che si usa per connettersi al Centro protezione di Azure. Al termine del processo di autenticazione, verrà avviato Power BI l'importazione di dati per creare i report. Durante questo periodo è visualizzato il seguente messaggio nell'angolo destro del browser:

    ![Connettersi al Centro protezione di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

    >[AZURE.NOTE] Quando viene creato il dashboard per la prima volta può richiedere più tempo rispetto al solito, principalmente per gli scenari in cui si hanno più abbonamenti. 

5. Una volta completato il processo, il dashboard di Centro protezione di Azure Power BI verrà caricato insieme al report di **Gestione dei criteri** simile a quello illustrato di seguito:

    ![Dashboard di gestione dei criteri](./media/security-center-powerbi/security-center-powerbi-fig1-new9.png)

## <a name="see-also"></a>Vedere anche
In questo documento è stato illustrato come usare Power BI in Centro protezione di Azure. Per ulteriori informazioni sul Centro protezione di Azure, vedere gli articoli seguenti:

- [Guida e pianificazione della Centro protezione di Azure](security-center-planning-and-operations-guide.md) , informazioni su come pianificare l'adozione di Azure Security Center.
- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) , informazioni su come configurare le impostazioni di protezione in Centro protezione di Azure
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) , informazioni su come gestire e risposta agli avvisi di sicurezza
- [Domande frequenti su Centro protezione di Azure](security-center-faq.md) , ovvero trova domande frequenti sull'utilizzo del servizio
- [Blog sulla protezione di Azure](http://blogs.msdn.com/b/azuresecurity/) , trovare post di blog sulla protezione di Azure e conformità
