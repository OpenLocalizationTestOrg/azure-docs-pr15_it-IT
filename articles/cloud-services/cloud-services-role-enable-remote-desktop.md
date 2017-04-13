<properties 
pageTitle="Abilitare connessione Desktop remoto per un ruolo di servizi Cloud Azure" 
description="Come configurare l'applicazione di servizio cloud azure per consentire connessioni desktop remoto" 
services="cloud-services" 
documentationCenter="" 
authors="sbtron" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="02/17/2016" 
ms.author="saurabh"/>

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Abilitare connessione Desktop remoto per un ruolo di servizi Cloud Azure

>[AZURE.SELECTOR]
- [Portale classica Azure](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


Desktop remoto consente di accedere al desktop di un ruolo in esecuzione in Azure. È possibile utilizzare una connessione Desktop remoto per risolvere i problemi e diagnosticare i problemi con l'applicazione mentre è in esecuzione. 

È possibile attivare una connessione Desktop remoto nel proprio ruolo in fase di sviluppo facilmente individuabili perché includono i moduli di Desktop remoto nella definizione del servizio o è possibile scegliere di attivare Desktop remoto tramite l'estensione Desktop remoto. Approccio consigliato consiste nell'usare l'estensione Desktop remoto come è possibile attivare Desktop remoto, anche dopo l'applicazione viene distribuito senza dover ridistribuire l'applicazione. 


## <a name="configure-remote-desktop-from-the-azure-classic-portal"></a>Configurare Desktop remoto dal portale di classica Azure
Il portale classico Azure utilizza l'approccio estensione Desktop remoto in modo che è possibile attivare Desktop remoto, anche dopo l'applicazione distribuita. Pagina **Configura** per il servizio cloud consente di attivare Desktop remoto, cambiare l'account di amministratore locale utilizzato per la connessione alle macchine virtuali, il certificato usato con l'autenticazione e imposta la data di scadenza. 


1. Fare clic su **Servizi Cloud**, fare clic sul nome del servizio cloud e quindi fare clic su **Configura**.

2. Fare clic su **remoto**.
    
    ![Servizi cloud remoto](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)
    
    > [AZURE.WARNING] Tutte le istanze di ruolo verranno riavviate quando si prima di tutto attivare Desktop remoto e fare clic su OK (segno di spunta). Per evitare che riavviare il computer, è necessario installare il certificato usato per crittografare la password sul ruolo. Per evitare che il riavvio, [caricare un certificato per il servizio cloud](cloud-services-how-to-create-deploy/#how-to-upload-a-certificate-for-a-cloud-service) e quindi tornare a questa finestra di dialogo.
    

3. **Ruoli**, selezionare il ruolo che si desidera aggiornare o selezionare **tutto** per tutti i ruoli.

4. Apportare le modifiche seguenti:
    
    - Per abilitare Desktop remoto, selezionare la casella di controllo **Attiva Desktop remoto** . Per disattivare Desktop remoto, deselezionare la casella di controllo.
    
    - Creare un account da utilizzare in connessioni Desktop remoto per le istanze di ruolo.
    
    - Aggiornare la password dell'account esistente.
    
    - Selezionare un certificato caricato da utilizzare per l'autenticazione (caricamento il certificato utilizzando **caricare** nella pagina **certificati** ) o creare un nuovo certificato. 
    
    - Modificare la data di scadenza per la configurazione Desktop remoto.

5. Dopo aver gli aggiornamenti di configurazione, fare clic su **OK** (segno di spunta).


## <a name="remote-into-role-instances"></a>Remote in istanze del ruolo
Una volta Desktop remoto è attivata per i ruoli in un'istanza di ruolo tramite vari strumenti è possibile remota.

Per connettersi a un'istanza del ruolo dal portale di classica Azure:
    
  1.   Fare clic su **istanze** per aprire la pagina **istanze** .
  2.   Selezionare un'istanza di ruolo con Desktop remoto configurato.
  3.   Fare clic su **Connetti**e seguire le istruzioni per visualizzare il desktop. 
  4.   Fare clic su **Apri** e quindi su **Connetti** per avviare la connessione Desktop remoto. 


### <a name="use-visual-studio-to-remote-into-a-role-instance"></a>Utilizzare Visual Studio per remote in un'istanza del ruolo

In Visual Studio, Esplora Server:

1. Espandere il **Azure\\servizi Cloud\\[nome servizio cloud]** nodo.
2. Espandere **gestione temporanea** o **produzione**.
3. Espandere il ruolo di singoli.
4. Rapida una delle istanze del ruolo, fare clic su **Connetti tramite Desktop remoto...**e quindi immettere il nome utente e la password. 

![Desktop remoto Esplora server](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)


### <a name="use-powershell-to-get-the-rdp-file"></a>Usare PowerShell per ottenere il file RDP
È possibile utilizzare il cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) per recuperare il file RDP. È quindi possibile utilizzare il file RDP con connessione Desktop remoto per accedere al servizio cloud.

### <a name="programmatically-download-the-rdp-file-through-the-service-management-rest-api"></a>Livello di programmazione scaricare il file RDP tramite l'API REST di gestione del servizio
È possibile utilizzare l'operazione di resto [Scaricare File RDP](https://msdn.microsoft.com/library/jj157183.aspx) di scaricare il file RDP. 



## <a name="to-configure-remote-desktop-in-the-service-definition-file"></a>Per configurare Desktop remoto in file di definizione del servizio

Questo metodo consente di attivare Desktop remoto per l'applicazione in fase di sviluppo. Questo approccio richiede la password crittografata trovarsi nella configurazione del servizio file e gli aggiornamenti per la configurazione desktop remoto richiedono ridistribuzione dell'applicazione. Se si desidera evitare questi svantaggi è necessario utilizzare l'approccio estensione desktop remoto in base a descritto sopra.  

È possibile utilizzare Visual Studio per [abilitare una connessione desktop remoto](../vs-azure-tools-remote-desktop-roles.md) utilizzando l'approccio di file di definizione di servizio.  
I passaggi seguenti descrivono le modifiche necessarie per i file di modello di servizio per attivare desktop remoto. Visual Studio verranno automaticamente apporta le modifiche durante la pubblicazione.

### <a name="set-up-the-connection-in-the-service-model"></a>Configurare la connessione nel modello di servizio 
Utilizzare l'elemento **Importa** per importare il modulo di **accesso remoto** e il modulo **RemoteForwarder** nel file [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) .

File di definizione del servizio dovrebbe essere simile al seguente con il `<Imports>` elemento aggiunto.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
Il file [ServiceConfiguration. cscfg](cloud-services-model-and-package.md#cscfg) dovrebbe essere simile al seguente, annotare il `<ConfigurationSettings>` e `<Certificates>` elementi. Il certificato specificato deve essere [caricata al servizio cloud](../cloud-services-how-to-create-deploy.md#how-to-upload-a-certificate-for-a-cloud-service).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## <a name="additional-resources"></a>Risorse aggiuntive

[Come configurare i servizi Cloud](cloud-services-how-to-configure.md)