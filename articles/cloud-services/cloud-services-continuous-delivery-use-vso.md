<properties
    pageTitle="Recapito continuo con Visual Studio Team Services in Azure | Microsoft Azure"
    description="Informazioni su come configurare i progetti di Visual Studio Team Services team per compilare e distribuire la caratteristica Web App in Azure App servizio o cloud services automaticamente."
    services="cloud-services"
    documentationCenter=".net"
    authors="mlearned"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="mlearned"/>

# <a name="continuous-delivery-to-azure-using-visual-studio-team-services"></a>Recapito continuo a Azure utilizzando Visual Studio Team Services

È possibile configurare i progetti di Visual Studio Team Services team per creare e distribuire le applicazioni web Azure o servizi cloud automaticamente.  (Per informazioni su come configurare una compilazione continua e distribuire sistema utilizzando un Team Foundation Server *in locale* , vedere [Recapito continua per i servizi Cloud in Azure](cloud-services-dotnet-continuous-delivery.md)).

In questa esercitazione si presuppone che Visual Studio 2013 e Azure SDK installato. Se si dispone già di Visual Studio 2013, scaricarlo facendo clic sul collegamento **Guida introduttiva gratuitamente** su [www.visualstudio.com](http://www.visualstudio.com). Installare Azure SDK da [qui](http://go.microsoft.com/fwlink/?LinkId=239540).

> [AZURE.NOTE]È necessario un account di Visual Studio Team Services per completare l'esercitazione: È possibile [aprire un account di Visual Studio Team Services gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Per configurare un servizio cloud per compilare e distribuire in Azure utilizzando Visual Studio Team Services automaticamente, seguire questa procedura.

## <a name="1-create-a-team-project"></a>1: creare un progetto team

Seguire le istruzioni riportate [di seguito](http://go.microsoft.com/fwlink/?LinkId=512980) per creare il team di progetto e collegarlo a Visual Studio. Si presuppone che si sta utilizzando Team Foundation versione controllo (TFVC) come la soluzione di controllo di origine. Se si desidera utilizzare fra per il controllo delle versioni, vedere [la versione di operazioni di questa procedura dettagliata](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## <a name="2-check-in-a-project-to-source-control"></a>2: archiviare un progetto al controllo origine

1. In Visual Studio, aprire la soluzione che si desidera distribuire o crearne uno nuovo.
È possibile distribuire un'app web o un servizio cloud (applicazione Azure) eseguendo la procedura descritta in questa procedura dettagliata.
Se si desidera creare una nuova soluzione, creare un nuovo progetto di servizio Cloud di Azure o un nuovo progetto MVC ASP.NET. Assicurarsi che il progetto è destinato a .NET Framework 4 o 4.5 e se si sta creando un progetto di servizio cloud, aggiungere un ruolo web ASP.NET MVC e un ruolo di lavoro e scegliere applicazione Internet per il ruolo di web. Quando richiesto, scegliere **Applicazione Internet**.
Se si desidera creare un'app web, scegliere il modello di progetto applicazione Web ASP.NET e quindi scegliere MVC. Vedere [creare un'applicazione web ASP.NET in Azure App servizio](../app-service-web/web-sites-dotnet-get-started.md).

    > [AZURE.NOTE] Visual Studio Team Services supporta solo CI distribuzioni di Visual Studio Web Applications al momento. Progetti di sito Web sono fuori ambito.

1. Aprire il menu di scelta rapida per la soluzione e scegliere **Aggiungi soluzione a controllo del codice sorgente**.

    ![][5]

1. Accettare o modificare le impostazioni predefinite e fare clic su **OK** . Una volta completato il processo, icone vengono visualizzate in **Esplora soluzioni**.

    ![][6]

1. Aprire il menu di scelta rapida per la soluzione e scegliere **Archivia**.

    ![][7]

1. Nell'area di **Modifiche in sospeso** di **Team Explorer**, digitare un commento per l'archiviazione e scegliere il pulsante **Archivia** .

    ![][8]

    Nota le opzioni per includere o escludere modifiche specifiche quando si archivia. Se vengono escluse le modifiche desiderate, fare clic su collegamento **Includere tutti** .

    ![][9]

## <a name="3-connect-the-project-to-azure"></a>3: connettere il progetto in Azure

1. Dopo aver creato un progetto di team VS Team Services con codice sorgente al suo interno, si è pronti a cui connettersi Azure progetto team.  Nel [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), selezionare l'app web o servizio cloud oppure crearne uno nuovo scegliendo il **+** icona in basso a sinistra e scegliendo **Un servizio Cloud** o **Web App** e quindi **Creare rapido**. Selezionare il collegamento di **configurare la pubblicazione con Visual Studio Team Services** .

    ![][10]

1. Nella creazione guidata digitare il nome del proprio account di Visual Studio Team Services nella casella di testo e fare clic sul collegamento **Autorizzare ora** . Potrebbe essere necessario accedere.

    ![][11]

1. Nella finestra dialogo a comparsa **Richiesta di connessione** scegliere il pulsante **accetta** per autorizzare Azure per configurare il progetto team in servizi di Team VS.

    ![][12]

1. Quando l'autorizzazione ha avuto esito positivo, viene visualizzato un elenco a discesa contenente un elenco di progetti team Visual Studio Team Services. Scegliere il nome del progetto team che è stato creato in precedenza e quindi sul pulsante della procedura guidata segno di spunta.

    ![][13]

1. Dopo il progetto è collegato, si vedrà alcune istruzioni per archiviare le modifiche apportate al progetto team Visual Studio Team Services.  Il successivo controllo aggiuntivo, Visual Studio Team Services verrà compilare e distribuire il progetto in Azure.  Provare questa ora facendo clic sul collegamento **Archivia da Visual Studio** e quindi sul collegamento di **Avviare Visual Studio** (o sul pulsante **Visual Studio** equivalente nella parte inferiore dello schermo portale).

    ![][14]

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4: impostare un trigger la ricostruzione e ridistribuire il progetto

1. In Visual Studio **Team Explorer**, selezionare il collegamento **Esplora controllo codice sorgente** .

    ![][15]

1. Passare al file di soluzione e aprirlo.

    ![][16]

1. In **Esplora soluzioni**, aprire un file e modificarlo. Ad esempio modificare il file `_Layout.cshtml` nelle viste\\cartella condivisa in un ruolo web MVC.

    ![][17]

1. Modificare il logo del sito e scegliere **Ctrl + S** per salvare il file.

    ![][18]

1. In **Esplora risorse del Team**, selezionare il collegamento **Modifiche in sospeso** .

    ![][19]

1. Immettere un commento e quindi fare clic sul pulsante **Archivia** .

    ![][20]

1. Fare clic sul pulsante **Home** per tornare alla home page del **Team Explorer** .

    ![][21]

1. Selezionare il collegamento **Compila** per visualizzare le build in corso.

    ![][22]

    **Team Explorer** mostra che una compilazione se è stata attivata per il controllo.

    ![][23]

1. Fare doppio clic sul nome della compilazione in corso per visualizzare un file di log dettagliate durante la compilazione.

    ![][24]

1. Durante la compilazione è in corso, esaminare la definizione di compilazione creato al momento collegata TFS Azure utilizzando la creazione guidata.  Aprire il menu di scelta rapida per la definizione di compilazione e scegliere **Modifica definizione di compilazione**.

    ![][25]

    Nella scheda **Trigger** , si vedrà che la definizione di compilazione sia impostata su cui basare ogni controllo per impostazione predefinita.

    ![][26]

    Nella scheda **grafici** , è possibile visualizzare che l'ambiente di distribuzione è impostato sul nome dell'app web o servizio cloud. Se si lavora con web apps, le proprietà che vengono visualizzate sarà diverse da quello illustrato di seguito.

    ![][27]

1. Specificare i valori delle proprietà se si desidera che valori diversi da quelli predefiniti. Le proprietà per la pubblicazione di Azure sono nella sezione **distribuzione** .

    Nella tabella seguente mostra le opzioni disponibili nella sezione **distribuzione** :

  	|Proprietà|Valore predefinito|
  	|---|---|
  	|Consentire certificati non attendibili|Se è false, i certificati SSL devono essere firmati da un'autorità radice.|
  	|Consentire l'aggiornamento|Consente di distribuzione aggiornare una distribuzione esistente anziché crearne uno nuovo. Consente di mantenere l'indirizzo IP.|
  	|Non eliminare|Se true, non sovrascrivere una distribuzione non correlata esistente (aggiornamento consentito).|
  	|Percorso di impostazioni di distribuzione|Il percorso al file .pubxml per un'app web, relativo alla cartella radice del repo. Ignorati per i servizi cloud.|
  	|Ambiente di distribuzione di SharePoint|Lo stesso nome del servizio.|
  	|Ambiente di distribuzione di Azure|Il nome di servizio cloud o app web.|

1. Se si usano più configurazioni di servizio (file cscfg), è possibile specificare la configurazione del servizio desiderato nell'impostazione **creano, avanzate, argomenti MSBuild** . Ad esempio, per usare ServiceConfiguration.Test.cscfg, impostare argomenti MSBuild opzione della riga `/p:TargetProfile=Test`.

    ![][38]

    A questo punto, la compilazione deve essere completata.

    ![][28]

1. Se si fa doppio clic sul nome di compilazione, Visual Studio viene visualizzato un **Riepilogo compilazione**, inclusi i risultati dei test di progetti di test di unità associato.

    ![][29]

1. Nel [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), è possibile visualizzare la distribuzione associata nella scheda **distribuzioni** quando viene selezionato l'ambiente di gestione temporanea.

    ![][30]

1.  Passare all'URL del sito. Per un'app web, scegliere il pulsante **Sfoglia** della barra dei comandi. Per un servizio cloud, selezionare l'URL nella sezione **Informazioni di riepilogo rapido** della pagina **Dashboard** che mostra l'ambiente di gestione temporanea per un servizio cloud. Distribuzioni dall'integrazione continua per i servizi cloud pubblicate per l'ambiente di gestione temporanea per impostazione predefinita. È possibile modificare questa impostando la proprietà di **Ambiente servizio Cloud alternativo** di **produzione**. Questa schermata indica la posizione dell'URL del sito nella pagina dashboard del servizio cloud.

    ![][31]

    Verrà aperta una nuova scheda del browser per visualizzare il sito in esecuzione.

    ![][32]

    Per i servizi cloud, se è apportare altre modifiche al progetto, si trigger compila più e verranno inseriti più distribuzioni. Il più recente contrassegnato come attivo.

    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5: ridistribuire una build precedente

Questo passaggio si applica ai servizi cloud ed è facoltativo. Nel portale di classica Azure, scegliere una distribuzione precedente e quindi fare clic sul pulsante **ridistribuire** per riavvolgere il sito per un controllo precedente.  Si noti che verrà avviare una nuova compilazione TFS e creare una nuova voce nella cronologia di distribuzione.

![][34]

## <a name="6-change-the-production-deployment"></a>6: modificare la distribuzione di produzione

Questo passaggio si applica solo ai servizi cloud, non web app. Quando si è pronti, è possibile convertire l'ambiente di gestione temporanea all'ambiente di produzione facendo clic sul pulsante **Scambia** nel portale di classica Azure. Ambiente di gestione temporanea appena distribuito è promossi a produzione e ambiente di produzione precedente, se presenti, diventa un ambiente di gestione temporanea. Distribuzione attiva potrebbe essere diversa per la produzione e ambienti di gestione temporanea, ma la cronologia di distribuzione di recente build corrisponde indipendentemente dall'ambiente.

![][35]

## <a name="7-run-unit-tests"></a>7: eseguire unit test

Questo passaggio si applica solo a web App, non servizi cloud. Per inserire una verifica qualità nella distribuzione, è possibile eseguire test di unità e in caso di errore, è possibile interrompere la distribuzione.

1.  In Visual Studio, aggiungere un progetto di test di unità.

    ![][39]

1.  Aggiungere riferimenti al progetto da testare.

    ![][40]

1.  Aggiungere alcuni unit test. Per iniziare, provare a un test fittizio passa sempre.

        ```
        using System;
        using Microsoft.VisualStudio.TestTools.UnitTesting;

        namespace UnitTestProject1
        {
            [TestClass]
            public class UnitTest1
            {
                [TestMethod]
                [ExpectedException(typeof(NotImplementedException))]
                public void TestMethod1()
                {
                    throw new NotImplementedException();
                }
            }
        }
        ```

1.  Modificare la definizione di compilazione, scegliere la scheda **processo** ed espandere il nodo **del Test** .

1.  Impostare l' **Errore compilazione se test** su True. Ciò significa che la distribuzione non si verifica solo se il test passare.

    ![][41]

1.  Accoda nuova compilazione.

    ![][42]

    ![][43]

1. Mentre è in corso la compilazione, verificare se lo stato di avanzamento.

    ![][44]

    ![][45]

1. Al termine della compilazione, controllare i risultati.

    ![][46]

    ![][47]

1.  Provare a creare un test avrà esito negativo. Aggiungere un nuovo test copiando il primo, rinominare e commento la riga di codice indicante che NotImplementedException è un'eccezione prevista.

        ```
        [TestMethod]
        //[ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            throw new NotImplementedException();
        }
        ```

1. Archiviare le modifiche Accoda nuova compilazione.

    ![][48]

1. Visualizzare i risultati per visualizzare i dettagli sull'errore.

    ![][49]

    ![][50]

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su unit test in Visual Studio Team Services, vedere [eseguire test di unità nella compilazione](http://go.microsoft.com/fwlink/p/?LinkId=510474). Se si usa fra, vedere [condividere il codice in fra](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e [distribuzione continua al servizio App Azure](../app-service-web/app-service-continuous-deployment.md).  Per ulteriori informazioni sui servizi di Team Visual Studio, vedere [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
