<properties 
    pageTitle="Procedura dettagliata: Monitorare Microsoft Dynamics CRM con informazioni dettagliate sui applicazione" 
    description="È possibile ottenere telemetria da Microsoft Dynamics CRM Online con informazioni dettagliate sui applicazione. Procedura dettagliata del programma di installazione, recupero dei dati, visualizzazione ed Esporta." 
    services="application-insights" 
    documentationCenter=""
    authors="mazharmicrosoft" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="awills"/>
 
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Procedura dettagliata: Abilitazione di telemetria per Microsoft Dynamics CRM Online tramite l'applicazione approfondimenti

In questo articolo viene illustrato come ottenere i dati di telemetria da [Microsoft Dynamics CRM Online](https://www.dynamics.com/) utilizzando [Visual Studio applicazione approfondimenti](https://azure.microsoft.com/services/application-insights/). Verrà esaminato il processo di completamento dell'aggiunta di script applicazione approfondimenti per l'applicazione, l'acquisizione di dati e la visualizzazione dei dati.

>[AZURE.NOTE] [Individuare la soluzione di esempio](https://dynamicsandappinsights.codeplex.com/).

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Aggiungere informazioni dettagliate sui applicazione nuovo o esistente istanza CRM Online 

Per controllare l'applicazione, aggiungere un SDK approfondimenti applicazione all'applicazione. SDK invia telemetria al [portale applicazione approfondimenti](https://portal.azure.com), in cui è possibile utilizzare il nostro analisi potenti e strumenti di diagnostica o esportare i dati allo spazio di archiviazione.

### <a name="create-an-application-insights-resource-in-azure"></a>Creare una risorsa applicazione approfondimenti in Azure

1. È possibile ottenere [un account di Microsoft Azure](http://azure.com/pricing). 
2. Accedere al [portale di Azure](https://portal.azure.com) e aggiungere una nuova risorsa approfondimenti applicazione. Si tratta in cui verranno elaborati e visualizzati i dati.

    ![Fare clic su +, servizi di sviluppo, approfondimenti applicazione.](./media/app-insights-sample-mscrm/01.png)

    Scegliere ASP.NET come tipo di applicazione.

3. Aprire la scheda Quick Start e lo script di codice.

    ![](./media/app-insights-sample-mscrm/03.png)

**Tenere aperta la pagina di codice** mentre si svolgono successivo passaggio in un'altra finestra del browser. È necessario il codice breve. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Creare una risorsa web JavaScript in Microsoft Dynamics CRM

1. Aprire l'istanza CRM Online e accedere con privilegi di amministratore.
2. Aprire Microsoft Dynamics CRM impostazioni, le personalizzazioni, personalizzare il sistema

    ![](./media/app-insights-sample-mscrm/04.png)
    
    ![](./media/app-insights-sample-mscrm/05.png)


    ![](./media/app-insights-sample-mscrm/06.png)

3. Creare una risorsa JavaScript.

    ![](./media/app-insights-sample-mscrm/07.png)

    Assegnare un nome, selezionare **Script (JScript)** e aprire l'editor di testo.

    ![](./media/app-insights-sample-mscrm/08.png)
    
4. Copiare il codice approfondimenti applicazione. Durante la copia assicurarsi di ignorare tag script. Fare riferimento di sotto di schermata:

    ![](./media/app-insights-sample-mscrm/09.png)

    Il codice include la chiave strumentazione che identifica la risorsa approfondimenti dell'applicazione.

5. Salvare e pubblicare.

    ![](./media/app-insights-sample-mscrm/10.png)

### <a name="instrument-forms"></a>Strumento moduli

1. In Microsoft CRM Online, aprire il modulo Account

    ![](./media/app-insights-sample-mscrm/11.png)

2. Aprire la maschera di proprietà

    ![](./media/app-insights-sample-mscrm/12.png)

3. Aggiungere la risorsa web JavaScript creato

    ![](./media/app-insights-sample-mscrm/13.png)

    ![](./media/app-insights-sample-mscrm/14.png)

4. Salvare e pubblicare le personalizzazioni modulo.


## <a name="metrics-captured"></a>Metrica acquisita

A questo punto impostati acquisizione di telemetria per la maschera. Ogni volta che viene utilizzato, i dati verranno inviati alla risorsa di informazioni approfondite dell'applicazione.

Di seguito sono esempi dei dati che verrà visualizzato.

#### <a name="application-health"></a>Dell'integrità

![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

Eccezioni browser:

![](./media/app-insights-sample-mscrm/17.png)

Fare clic sul grafico per ulteriori informazioni:

![](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Uso

![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Browser

![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Georilevazione

![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Richiesta di visualizzazione all'interno della pagina

![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Codice di esempio

[Individuare il codice di esempio](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI

È possibile eseguire un'analisi più approfondita anche se è [esportare i dati da Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Soluzione di esempio Microsoft Dynamics CRM

[Qui è la soluzione di esempio implementata in Microsoft Dynamics CRM] (https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Ulteriori informazioni

* [Che cos'è approfondimenti applicazione?](app-insights-overview.md)
* [Informazioni dettagliate sui applicazione per le pagine web](app-insights-javascript.md)
* [Ulteriori esempi e procedure dettagliate](app-insights-code-samples.md)

 
