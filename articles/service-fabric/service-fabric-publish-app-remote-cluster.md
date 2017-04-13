<properties
    pageTitle="Pubblicare un'app in un cluster remoto con Visual Studio | Microsoft Azure"
    description="Informazioni su come pubblicare un'applicazione di un cluster di tessuti servizio remoto utilizzando Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="publish-an-application-to-a-remote-cluster-by-using-visual-studio"></a>Pubblicare un'applicazione di un cluster remoto utilizzando Visual Studio

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

L'estensione di Azure servizio tessuti per Visual Studio offre un modo semplice, ripetibile e script per pubblicare un'applicazione in un cluster di servizio tessuti.

## <a name="the-artifacts-required-for-publishing"></a>Gli elementi necessari per la pubblicazione

### <a name="deploy-fabricapplicationps1"></a>Distribuire FabricApplication.ps1

Si tratta di uno script di PowerShell che utilizza il percorso del profilo pubblica come parametro per pubblicare applicazioni di servizio tessuti. Poiché questo script fa parte di un'applicazione, sono invitati a modificare in base alle esigenze dell'applicazione.

### <a name="publish-profiles"></a>I profili di pubblicazione

Una cartella nel progetto di applicazione di servizio tessuti chiamato **PublishProfiles** contiene file XML che memorizzano le informazioni essenziali per la pubblicazione di un'applicazione, ad esempio:

- Parametri di connessione cluster tessuti servizio
- Percorso di un file di parametro applicazione
- Impostazioni di aggiornamento

Per impostazione predefinita, l'applicazione conterrà due profili di pubblicazione: Local.xml e Cloud.xml. È possibile aggiungere altri profili copiando e incollando uno dei file predefinito.

### <a name="application-parameter-files"></a>File di parametro dell'applicazione

Una cartella nel progetto di applicazione di servizio tessuti chiamato **ApplicationParameters** contiene file XML per i valori dei parametri manifesto applicazione specificato dall'utente. File manifesto dell'applicazione è possono aggiungere parametri in modo che è possibile usare valori diversi per le impostazioni di distribuzione. Per ulteriori informazioni sull'applicazione di parametri l'applicazione, vedere [gestire più ambienti nel servizio tessuti](service-fabric-manage-multiple-environment-app-configuration.md).

>[AZURE.NOTE] Per i servizi dell'attore, è necessario compilare il progetto prima di tentare di modificare il file in un editor o tramite la finestra di dialogo pubblica. In questo modo parte del file manifesto verrà generato durante la compilazione.

## <a name="to-publish-an-application-by-using-the-publish-service-fabric-application-dialog-box"></a>Per pubblicare un'applicazione utilizzando la finestra di dialogo Pubblica applicazione tessuti del servizio

La procedura seguente viene illustrato come pubblicare un'applicazione utilizzando la finestra di dialogo **Pubblica applicazione di servizio tessuti** fornita da Visual Studio servizio tessuti Tools.

1. Dal menu di scelta rapida del progetto di applicazione di servizio tessuti, scegliere **pubblica...** Per visualizzare la finestra di dialogo **Pubblica applicazione di servizio tessuti** .

    ![Il * * nella finestra di dialogo Pubblica servizio tessuti applicazione * *][0]

    Il file selezionato nella casella di riepilogo a discesa **profilo di destinazione** è tutte le impostazioni, ad eccezione di **manifesto versioni**, in cui vengono salvate. È possibile riutilizzare un profilo esistente o crearne uno nuovo scegliendo **< Gestisci profili >** nella casella di riepilogo a discesa **profilo di destinazione** . Quando si sceglie un profilo di pubblicazione, il relativo contenuto vengono visualizzati i campi corrispondenti nella finestra di dialogo. Per salvare le modifiche in qualsiasi momento, fare clic su collegamento **Salva profilo** .    

2. Nella sezione **endpoint connessione** specificare endpoint pubblicazione di un locale o remoto tessuti servizio cluster. Per aggiungere o modificare l'endpoint di connessione, fare clic su elenco a discesa **Endpoint connessione** . L'elenco Mostra cluster tessuti servizio disponibili endpoint di connessione a cui è possibile pubblicare in base alle sottoscrizioni Azure. Si noti che se non ancora effettuato l'accesso a Visual Studio, viene chiesto di farlo.

    Utilizzare la finestra di dialogo Selezione cluster per scegliere l'insieme delle sottoscrizioni disponibili e cluster.

    ![Il * * nella finestra di dialogo selezionare servizio tessuti Cluster * *][1]

    >[AZURE.NOTE] Se si desidera pubblicare in un endpoint non autorizzato (ad esempio un cluster di terze parti), vedere la sezione **pubblicazione a un endpoint cluster non autorizzato** riportata di seguito.

    Una volta scelto un endpoint, Visual Studio consente di verificare la connessione al cluster tessuti servizio selezionato. Se il cluster non è protetto, Visual Studio possono connettersi immediatamente. Tuttavia, se il cluster è protetto, è necessario installare un certificato in un computer locale prima di continuare. Per ulteriori informazioni, vedere [come configurare le connessioni sicure](service-fabric-visualstudio-configure-secure-connections.md) . Al termine, scegliere il pulsante **OK** . Cluster selezionato verrà visualizzato nella finestra di dialogo **Pubblica applicazione di servizio tessuti** .

3. Nella casella di riepilogo a discesa **Applicazione parametro File** , passare a un file di parametro dell'applicazione. Un file di parametro dell'applicazione contiene i valori specificati dall'utente per i parametri del file manifesto dell'applicazione. Per aggiungere o modificare un parametro, scegliere il pulsante **Modifica** . Immettere o modificare il valore del parametro nella griglia **parametri** . Al termine, scegliere il pulsante **Salva** .

    ![Il * * nella finestra di dialogo Modifica parametri * *][2]

4. Usare la casella di controllo **aggiornamento dell'applicazione** per specificare se si pubblica azione è un aggiornamento. Aggiornamento pubblicare azioni diversi da normale pubblicare azioni. Per un elenco delle differenze, vedere [Eseguire l'aggiornamento di applicazione di servizio tessuti](service-fabric-application-upgrade.md) . Per configurare le impostazioni di aggiornamento, scegliere il collegamento **Configura impostazioni di aggiornamento** . Viene visualizzato l'editor di parametro aggiornamento. Vedere [configurare l'aggiornamento di un'applicazione di servizio tessuti](service-fabric-visualstudio-configure-upgrade.md) per altre informazioni sull'aggiornamento parametri.

5. Scegliere **manifesto versioni...** pulsante per visualizzare la finestra di dialogo **Modifica versioni** . È necessario aggiornare l'applicazione e le versioni di servizio per l'aggiornamento a avranno luogo. Visualizzare l' [applicazione di servizio tessuti esercitazione di aggiornamento](service-fabric-application-upgrade-tutorial.md) per scoprire come applicazione e servizio manifesto versioni impatto un processo di aggiornamento.

    ![Il * * nella finestra di dialogo Modifica versioni * *][3]

    Se l'applicazione e le versioni di servizio utilizzano semantic controllo delle versioni, ad esempio 1.0.0 o valori numerici in formato 1.0.0.0, selezionare l'opzione di **aggiornamento automatico dell'applicazione e le versioni di servizio** . Quando si sceglie questa opzione, il servizio e numeri di versione vengono aggiornati automaticamente ogni volta che un codice, configurazione o dati pacchetto versione viene aggiornati. Se si preferisce modificare manualmente le versioni, deselezionare la casella di controllo per disabilitare questa caratteristica.

    >[AZURE.NOTE] Per tutte le voci di pacchetto per la visualizzazione in un progetto dell'attore, è innanzitutto necessario generare il progetto per generare le voci nei file manifesto servizio.

6. Dopo aver completato specificando tutte le impostazioni necessarie, fare clic sul pulsante **pubblica** per pubblicare l'applicazione in cluster tessuti servizio selezionato. Vengono applicate le impostazioni specificate per il processo di pubblicazione.

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>Pubblicare un endpoint cluster non autorizzato (inclusi cluster di terze parti)

Visual Studio pubblicazione esperienza ottimizzata per la pubblicazione su cluster remoti associato a una delle sottoscrizioni Azure. Tuttavia, è possibile pubblicare i punti finali non autorizzati (ad esempio servizio tessuti festa cluster) modificando direttamente il profilo di pubblicazione XML. Come descritto in precedenza, due pubblicare profili sono forniti dai predefinito**Local.xml** e **Cloud.xml**, ma sono invitati a creare ulteriori profili per ambienti diversi. Ad esempio, è consigliabile creare un profilo per la pubblicazione su cluster di terze parti, ad esempio denominato **Party.xml**.

Se ci si connette a un cluster non protetto, è sufficiente è l'endpoint di connessione cluster, ad esempio `partycluster1.eastus.cloudapp.azure.com:19000`. In questo caso, l'endpoint di connessione nel profilo di pubblicazione risulterebbe simile al seguente:

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Se ci si connette a un cluster protetto, sarà necessario fornire i dettagli del certificato client dall'archivio locale da utilizzare per l'autenticazione. Per ulteriori informazioni, vedere [configurazione delle connessioni sicura a un cluster di servizio tessuti](service-fabric-visualstudio-configure-secure-connections.md).

  Dopo aver configurato il profilo di pubblicazione, è possibile che riferimento nella finestra di dialogo Pubblica come illustrato di seguito.

  ![Nuovo profilo di pubblicazione in pubblica nella finestra di dialogo][4]

  Si noti che in questo caso, il nuovo profilo di pubblicazione fa riferimento a uno dei file di parametro applicazione predefinita. Questa opzione se si desidera pubblicare la stessa configurazione dell'applicazione in numerosi ambienti. Nei casi in cui si vuole configurazioni diverse per ogni ambiente che si desidera pubblicare, al contrario rende utile creare un file di parametri di applicazione corrispondente.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come automatizzare il processo di pubblicazione in un ambiente di integrazione continua, vedere [configurare tessuti servizio di integrazione continua](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png
