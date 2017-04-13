<properties
   pageTitle="Note sulla versione di Azure catalogo dati | Microsoft Azure"
   description="Note sulla versione per il catalogo dati di Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-release-notes"></a>Note sulla versione di Azure catalogo dati

## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Note per 20 novembre 2015 rilasciare del catalogo dati di Azure

### <a name="opening-data-sources-in-power-bi-desktop"></a>Apertura di origini dati in Power BI Desktop

Quando si usa l'opzione "Apri in Power BI Desktop" dal portale di **Catalogo dati di Azure** , gli utenti possono verificarsi uno dei due problemi nell'applicazione di Power BI Desktop:

- Viene visualizzata una finestra di dialogo con il titolo "Non è possibile al documento aperto"
- Apertura dell'applicazione di Power BI Desktop, ma il file potrebbe essere vuoto

Per ogni caso, è possibile risolto il problema, scaricare e installare la versione più recente di Power BI Desktop da [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Note per 13 novembre 2015 rilasciare del catalogo dati di Azure

### <a name="registering-and-connecting-to-teradata"></a>La registrazione e la connessione a Teradata

Quando ci si connette a origini dati Teradata utenti sia installato il driver ODBC Teradata corretto che corrispondono al numero di bit (32 bit o 64 bit) del software in uso.

In questa data di rilascio ADC, il più recente [driver ODBC Teradata per windows (versione 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) è compatibile con Office 2013, ma non con Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Note per 13 luglio 2015 rilasciare del catalogo dati di Azure

### <a name="registering-and-connecting-to-oracle-database"></a>La registrazione e la connessione al Database Oracle

Quando ci si connette a origini dati Database Oracle utenti sia installato il driver Oracle che corrispondono al numero di bit (32 bit o 64 bit) del software in uso.

-   Durante la registrazione delle origini dati Oracle in un computer che esegue Windows a 32 bit, verrà utilizzato il driver Oracle a 32 bit
-   Durante la registrazione delle origini dati Oracle in un computer che esegue Windows a 64 bit, verrà utilizzato il driver Oracle a 64 bit
-   Durante la connessione a origini dati Oracle tramite Excel in un computer che esegue la versione a 32 bit di Microsoft Office, è incluso in Windows a 64 bit, verrà utilizzato il driver Oracle a 32 bit
-   Durante la connessione a origini dati Oracle tramite Excel in un computer che esegue la versione a 64 bit di Microsoft Office, verrà utilizzato il driver Oracle a 64 bit

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>La registrazione e la connessione a SQL Server Reporting Services

Supporto per le origini dati di SQL Server Reporting Services (SSRS) è attualmente limitato solo ai server modalità nativa. Supporto per SSRS in modalità di SharePoint verrà aggiunti in una versione successiva.

### <a name="opening-data-assets-in-excel"></a>Risorse di dati di apertura in Excel

Quando si apre risorse di dati in Microsoft Excel dal portale di **Catalogo dati di Azure** , gli utenti potrebbero essere richiesto la finestra di dialogo **Avviso di protezione di Microsoft Excel** . Si tratta di standard, il comportamento previsto e gli utenti possono selezionare **Abilita** per continuare.

Per ulteriori informazioni, vedere [abilitare o disabilitare gli avvisi di sicurezza relativi ai collegamenti e file scaricati da siti Web sospetti](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Proxy e criteri di registrazione di origine dati e la configurazione

Gli utenti che si verifichino situazioni in cui è possibile accedere al portale di catalogo dati di Azure, ma quando tentano di accedere allo strumento di registrazione di origine dati che possono verificarsi un messaggio di errore che impedisce che l'accesso.

Esistono due possibili cause per questo comportamento di problema:

**Causa 1: configurazione di Active Directory Federation Services** Lo strumento di registrazione di origine dati utilizza l'autenticazione basata su moduli per convalidare gli accessi utente in Active Directory. Per l'accesso, l'autenticazione basata su deve essere abilitato in Criteri di autenticazione globali da un amministratore di Active Directory.

In alcuni casi, questo errore può verificarsi solo quando l'utente si trova nella rete della società o solo quando l'utente si connette dall'esterno della rete aziendale. Il criterio di autenticazione globale consente metodi di autenticazione deve essere abilitata separatamente per intranet e le connessioni extranet. Se l'autenticazione basata su non abilitato per la rete da cui l'utente si connette, possono verificarsi errori di accesso.

Per ulteriori informazioni, vedere [Configurazione dei criteri di autenticazione](https://technet.microsoft.com/library/dn486781.aspx).

**Causa 2: configurazione del proxy di rete** Se la rete aziendale utilizza un server proxy, lo strumento di registrazione potrebbe non riuscire a cui connettersi Azure Active Directory mediante il proxy. Gli utenti possono assicurarsi che lo strumento di registrazione per la modifica di file di configurazione dello strumento, aggiunta in questa sezione per il file:


      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Per individuare il file RegistrationTool.exe.config, avviare lo strumento di registrazione e quindi aprire l'utilità di Gestione attività Windows. Nella scheda dettagli in Gestione attività, fare clic su RegistrationTool.exe e scegliere Apri percorso file dal menu a comparsa.
