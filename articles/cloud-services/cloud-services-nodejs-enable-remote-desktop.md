<properties 
    pageTitle="Attivare desktop remoto per servizi cloud (Node)" 
    description="Informazioni su come abilitare l'accesso desktop remoto per le macchine virtuali che ospita l'applicazione di Azure Node." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="enabling-remote-desktop-in-azure"></a>Attivazione di Desktop remoto in Azure

Desktop remoto consente di accedere al desktop di un'istanza di ruolo in esecuzione in Azure. È possibile utilizzare una connessione desktop remoto per configurare la macchina virtuale o risolvere i problemi con l'applicazione.

> [AZURE.NOTE] Questo articolo si applica alle applicazioni Node ospitate come un servizio Cloud di Azure.


## <a name="prerequisites"></a>Prerequisiti

- Installare e configurare [Azure Powershell](../powershell-install-configure.md).
- Distribuire un'app Node a un servizio Cloud Azure. Per ulteriori informazioni, vedere [creare e distribuire un'applicazione di Node a un servizio Cloud di Azure](cloud-services-nodejs-develop-deploy-app.md).


## <a name="step-1-use-azure-powershell-to-configure-the-service-for-remote-desktop-access"></a>Passaggio 1: Usare Azure PowerShell per configurare il servizio per l'accesso Desktop remoto

Per utilizzare Desktop remoto, è necessario aggiornare la definizione di servizio Azure e la configurazione con un nome utente, la password e certificato. 

Da un computer che contiene i file di origine per l'app, procedere come segue.

1. Esegui come amministratore **di Windows PowerShell** . (Dal **Menu Start** o **Schermata Start**, eseguire una ricerca per **Windows PowerShell**.)

2.  Passare alla cartella che contiene il file di definizione (.csdef) e servizio configurazione (. cscfg).

3. Immettere il seguente cmdlet di PowerShell:

        Enable-AzureServiceProjectRemoteDesktop

4. Se viene richiesto, immettere un nome utente e password.

    ![Abilita azureserviceprojectremotedesktop][enable-rdp]

3.  Immettere il seguente cmdlet di PowerShell per pubblicare le modifiche:

        Publish-AzureServiceProject

    ![pubblicare azureserviceproject][publish-project]

## <a name="step-2-connect-to-the-role-instance"></a>Passaggio 2: Connettersi all'istanza di ruoli

Dopo la pubblicazione la definizione del servizio di aggiornamento, è possibile connettersi all'istanza di ruolo.

1.  Nel [portale classica Azure], selezionare **Servizi Cloud** e quindi selezionare il servizio.

    ![Portale classica Azure][cloud-services]

2.  Fare clic su **istanze**e quindi fare clic su per visualizzare le istanze del servizio di **produzione** o **gestione temporanea** . Selezionare un'istanza e quindi fare clic su **Connetti** nella parte inferiore della pagina.

    ![La pagina istanze][3]

2.  Quando si fa clic su **Connetti**, il web browser richiede di salvare un file con estensione rdp. Aprire il file. (Ad esempio, se si usa Internet Explorer, fare clic su **Apri**.)

    ![Chiedi conferma prima di aprire o salvare il file con estensione rdp][4]

3.  Quando si apre il file, viene visualizzato il seguente messaggio di sicurezza:

    ![Schermata di sicurezza di Windows][5]

4.  Fare clic su **Connetti**e verrà visualizzato un prompt di sicurezza per immettere le credenziali per accedere all'istanza. Immettere la password creata in [passaggio 1] [passaggio 1: configurare il servizio per l'accesso Desktop remoto tramite PowerShell Azure] e quindi fare clic su **OK**.

    ![nome utente e la password][6]

Quando viene effettuata la connessione, connessione Desktop remoto viene visualizzato il desktop dell'istanza di Azure. 

![Sessione desktop remoto][7]

## <a name="step-3-configure-the-service-to-disable-remote-desktop-access"></a>Passaggio 3: Configurare il servizio per disattivare l'accesso Desktop remoto 

Quando non è più necessario connessioni desktop remoto per le istanze di ruolo nel cloud, disattivare l'accesso remoto al desktop tramite [PowerShell Azure].

1.  Immettere il seguente cmdlet di PowerShell:

        Disable-AzureServiceProjectRemoteDesktop

2.  Immettere il seguente cmdlet di PowerShell per pubblicare le modifiche:

        Publish-AzureServiceProject

## <a name="additional-resources"></a>Risorse aggiuntive

- [Accesso remoto istanze del ruolo di Azure] 
- [Utilizzo di Desktop remoto con i ruoli di Azure]
- [Centro per sviluppatori Node](/develop/nodejs/)

  [PowerShell Azure]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Portale classica Azure]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
[3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
[4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
[5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
[6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
[7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
[Accesso remoto istanze del ruolo di Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
[Utilizzo di Desktop remoto con i ruoli di Azure]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx
 