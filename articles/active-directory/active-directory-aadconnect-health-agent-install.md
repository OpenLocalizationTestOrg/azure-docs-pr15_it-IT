<properties
    pageTitle="Installazione agente integrità connettersi AD Azure | Microsoft Azure"
    description="Questa è la pagina di Azure Active Directory connettersi integrità che descrive l'installazione dell'agente per ADFS e sincronizzazione."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect dello stato dell'installazione dell'agente

In questo documento viene descritto come installare e configurare gli agenti Azure dell'integrità della connessione di Active Directory. È possibile scaricare gli agenti da [qui](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent).

##  <a name="requirements"></a>Requisiti
Tabella riportata di seguito è riportato un elenco dei requisiti per l'uso di Azure Active Directory connettersi integrità.

| Requisito | Descrizione|
| ----------- | ---------- |
|Azure Active Directory Premium| Azure Active Directory connettersi salute è una caratteristica di Azure Active Directory Premium e richiede Azure Active Directory Premium. </br></br>Per ulteriori informazioni, vedere [Guida introduttiva di Azure Active Directory Premium](active-directory-get-started-premium.md) </br>Per avviare una versione di valutazione gratuita di 30 giorni, vedere [avviare una versione di valutazione.](https://azure.microsoft.com/trial/get-started-active-directory/)|
|È necessario essere un amministratore globale del Azure Active Directory per iniziare con Azure Active Directory connettersi integrità|Per impostazione predefinita, solo gli amministratori globali possono installare e configurare gli agenti di integrità per iniziare, accedere al portale ed eseguire alcuna operazione all'interno di Azure Active Directory connettersi integrità. Per ulteriori informazioni, vedere [amministrazione directory Azure Active Directory](active-directory-administer.md). <br><br> Utilizzo di controllo dell'accesso basato su ruoli possibile consentito l'accesso a Azure Active Directory connettersi integrità ad altri utenti dell'organizzazione. Per ulteriori informazioni, vedere [controllo dell'accesso basato su ruoli per la salute la connessione di Azure Active Directory.](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) </br></br>**Importanti:** L'account utilizzato durante l'installazione di agenti deve essere un account aziendale o dell'istituto di istruzione. Non può essere un account Microsoft. Per ulteriori informazioni, vedere [effettuare l'iscrizione per Azure come un'organizzazione](sign-up-organization.md)
|Integrità agente connettersi Azure Active Directory è installato in ogni server target| Azure Active Directory connettersi integrità richiede un'installazione agente nel server di destinazione, per fornire i dati visualizzati nel portale. </br></br>Ad esempio, per recuperare dati da infrastruttura ADFS locale, è necessario installare l'agente nei server ADFS, Proxy ADFS e Proxy di applicazione Web. Accesso ai dati del locale allo stesso modo, infrastruttura di Active Directory, l'agente deve essere installato nei controller di dominio. </br></br>**Importanti:** L'account utilizzato durante l'installazione di agenti deve essere un account aziendale o dell'istituto di istruzione. Non può essere un account Microsoft. Per ulteriori informazioni, vedere [effettuare l'iscrizione per Azure come un'organizzazione](sign-up-organization.md)|
|Connettività in uscita per gli endpoint del servizio di Azure|Durante l'installazione e runtime, l'agente richiede connettività a endpoint del servizio di Azure Active Directory connettersi integrità. Se è bloccata connettività in uscita, assicurarsi che gli endpoint seguenti vengono aggiunti all'elenco consentito: </br></br><li>& #42;. BLOB.Core.Windows.NET </li><li>& #42;. Queue.Core.Windows.NET</li><li>adhsprodwus.ServiceBus.Windows.NET - porta: 5671 </li><li>https://Management.Azure.com </li><li>https://s1.adhybridhealth.Azure.com/</li><li>https://policykeyservice.DC.ad.msft.NET/</li><li>https://Login.Windows.NET</li><li>https://Login.microsoftonline.com</li><li>https://Secure.aadcdn.microsoftonline-p.com</li> |
|Porte del firewall del server che esegue l'agente.| L'agente richiede le seguenti porte del firewall per essere aperto affinché l'agente di comunicare con gli endpoint del servizio di integrità di Azure Active Directory.</br></br><li>Porte TCP/UDP 443</li><li>Porte TCP/UDP 5671</li>
|Se è abilitata la protezione avanzata di Internet Explorer, prevedere i seguenti siti Web| Se è abilitata la protezione avanzata di Internet Explorer, nel server che verrà installato l'agente è necessario consentire i seguenti siti Web.</br></br><li>https://Login.microsoftonline.com</li><li>https://Secure.aadcdn.microsoftonline-p.com</li><li>https://Login.Windows.NET</li><li>Server federativo per l'organizzazione considerato attendibile dai Azure Active Directory. Ad esempio: https://sts.contoso.com</li>




## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>L'installazione di Azure AD connettersi Agente integrità per AD FS
Per avviare l'installazione dell'agente, fare doppio clic sul file .exe che è stato scaricato. Nella prima schermata, fare clic su Installa.

![Verificare Azure AD Connect integrità](./media/active-directory-aadconnect-health-requirements/install1.png)

Al termine dell'installazione, fare clic su Configura ora.

![Verificare Azure AD Connect integrità](./media/active-directory-aadconnect-health-requirements/install2.png)

Viene avviato un prompt dei comandi, seguito da alcune PowerShell che esegue registro AzureADConnectHealthADFSAgent. Quando viene richiesto di accedere a Azure, procedere e accesso.

![Verificare Azure AD Connect integrità](./media/active-directory-aadconnect-health-requirements/install3.png)


Dopo l'accesso, PowerShell continuerà. Dopo essere stato completato, è possibile chiudere PowerShell e la configurazione è stata completata.

A questo punto, i servizi devono essere avviati automaticamente se si consente l'agente di monitoraggio e la raccolta di dati. Se non sono stati soddisfatti tutti i prerequisiti descritti nelle sezioni precedenti, gli avvisi vengono visualizzati nella finestra di PowerShell. Assicurarsi di completare i [requisiti](active-directory-aadconnect-health-agent-install.md#requirements) prima di installare l'agente. La figura seguente è illustrato un esempio di questi errori.

![Verificare Azure AD Connect integrità](./media/active-directory-aadconnect-health-requirements/install4.png)

Per verificare che l'agente è stato installato, cercare i seguenti servizi nel server. Se è stata completata la configurazione, dovrebbero già essere eseguiti. In caso contrario, essi vengono interrotti fino a ottenere la configurazione.

- Azure AD Connect integrità AD FS diagnostica servizio
- Azure AD Connect integrità ADFS informazioni dettagliate sui servizi
- Azure AD Connect integrità AD FS monitoraggio del servizio

![Verificare Azure AD Connect integrità](./media/active-directory-aadconnect-health-requirements/install5.png)


### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Installazione dell'agente di Windows Server 2008 R2 Server

Procedura per i server di Windows Server 2008 R2:

1. Assicurarsi che il server è in esecuzione in Service Pack 1.
1. Disattivare l'opzione avanzata di Internet Explorer per l'installazione dell'agente:
1. Installare Windows PowerShell 4.0 in ciascuno dei server prima di installare l'agente di integrità di Active Directory. Per installare Windows PowerShell 4.0:
 - Installare [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) tramite il collegamento seguente per scaricare il programma di installazione non in linea.
 - Installare PowerShell ISE (delle funzionalità di Windows)
 - Installare il [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Installare Internet Explorer 10 o versione successiva nel server. (Obbligatorio dal servizio integrità per eseguire l'autenticazione, usando le credenziali di amministratore di Azure.)
1. Per ulteriori informazioni sull'installazione di Windows PowerShell 4.0 in Windows Server 2008 R2, vedere l'articolo wiki [qui](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>Attivare il controllo per ADFS

> [AZURE.NOTE] In questa sezione si applica solo ai server federativi ADFS.

La funzionalità Analitica l'uso di raccogliere e analizzare i dati, l'agente di Azure Active Directory connettersi integrità deve le informazioni nei registri di controllo AD FS. I registri non sono abilitati per impostazione predefinita. Usare le procedure seguenti per abilitare il controllo di ADFS e per individuare i registri di controllo ADFS nel server ADFS.

#### <a name="to-enable-auditing-for-ad-fs-20"></a>Per abilitare il controllo di ADFS 2.0

1. Fare clic su **Start**, scegliere **programmi**, **Strumenti**di amministrazione e quindi fare clic su **Criteri di sicurezza locali**.
2. Passare alla cartella **Sicurezza protezione\Criteri utente Rights Management** e quindi fare doppio clic su genera controlli di protezione.
3. Nella scheda **Impostazioni di protezione locale** , verificare che sia presente l'account di servizio 2.0 ADFS. Se non è presenta, fare clic su **Aggiungi utente o gruppo** e aggiungerlo all'elenco e quindi fare clic su **OK**.
4. Per attivare il controllo, aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente:<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Chiudere Criteri di sicurezza locale e quindi aprire il componente aggiuntivo di gestione. Per aprire lo snap-in Gestione, fare clic su **Start**, scegliere **programmi**, **Strumenti**di amministrazione e quindi fare clic su Active Directory ADFS 2.0 gestione.
6. Nel riquadro azioni, fare clic su Modifica proprietà servizio federazione.
7. Nella finestra di dialogo **Proprietà di federazione del servizio** , fare clic sulla scheda **eventi** .
8. Selezionare le caselle di controllo **operazioni riuscite** e **controlli non riusciti** .
9. Fare clic su **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Per abilitare il controllo per AD FS in Windows Server 2012 R2

1. Aprire **Criteri di sicurezza locali** aprendo **Server Manager** nella schermata Start, o Server sulla barra delle applicazioni sul desktop e quindi fare clic su **Criteri di sicurezza strumenti/locale**.
2. Passare alla cartella **Assegnazione diritti utente protezione\Criteri di protezione** e quindi fare doppio clic su **dati**.
3. Nella scheda **Impostazioni di protezione locale** , verificare che sia presente l'account di servizio ADFS. Se non è presenta, fare clic su **Aggiungi utente o gruppo** e aggiungerlo all'elenco e quindi fare clic su **OK**.
4. Per attivare il controllo, aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente:<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Chiudere **Criteri di sicurezza locale**e quindi aprire il componente aggiuntivo **AD FS gestione** (Server Manager, fare clic su strumenti e quindi selezionare AD FS Management).
6. Nel riquadro azioni, fare clic su **Modifica proprietà servizio federazione**.
7. Nella finestra di dialogo proprietà di federazione del servizio, fare clic sulla scheda **eventi** .
8. Selezionare le caselle di controllo **operazioni riuscite e controlli non riusciti** e quindi fare clic su **OK**.






#### <a name="to-locate-the-ad-fs-audit-logs"></a>Per individuare il controllo di ADFS registri


1. Aprire **il Visualizzatore eventi**.
2. Passare a registri di Windows e scegliere **protezione**.
3. A destra, fare clic su **Registri corrente di filtro**.
4. In origine eventi, selezionare **Il controllo di AD FS**.

![Log di controllo di ADFS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] Se è presente un criterio di gruppo che disattivando il controllo di ADFS, integrità agente connettersi Azure Active Directory in grado di raccogliere informazioni. Assicurarsi che non si dispone di criteri di gruppo che possono essere la disabilitazione del controllo.

[//]: # (Start of Agent Proxy Configuration Section)

## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Installazione dell'agente di Azure Active Directory connettersi integrità per la sincronizzazione
L'agente di Azure Active Directory connettersi integrità per la sincronizzazione viene installato automaticamente nella build più recente di Azure AD Connect. Per utilizzare Azure AD Connect per la sincronizzazione, è necessario scaricare la versione più recente di Azure AD Connect e installarlo. È possibile scaricare la versione più recente [di seguito](http://www.microsoft.com/download/details.aspx?id=47594).

Per verificare che l'agente è stato installato, cercare i seguenti servizi nel server. Se è stata completata la configurazione, dovrebbero già essere eseguiti. In caso contrario, essi vengono interrotti fino a ottenere la configurazione.

- Azure AD Connect dell'integrità della sincronizzazione informazioni dettagliate sui servizi
- Azure AD Connect sincronizzazione integrità servizio di monitoraggio

![Verificare Azure AD Connect integrità per la sincronizzazione](./media/active-directory-aadconnect-health-sync/services.png)

> [AZURE.NOTE] Ricordare che con Azure Active Directory connettersi integrità richiede Azure Active Directory Premium. Se non si dispone di Azure Active Directory Premium, non si riesce a completare la configurazione nel portale di Azure. Per ulteriori informazioni, vedere la [pagina requisiti](active-directory-aadconnect-health-agent-install.md#requirements).


## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Integrità connettersi manuale Azure Active Directory per la registrazione di sincronizzazione
Se lo stato di connettersi AD Azure per la registrazione agente di sincronizzazione non funziona dopo l'installazione Azure AD Connect, è possibile utilizzare il comando PowerShell seguente per registrare manualmente l'agente.

>[AZURE.IMPORTANT] Questo comando di PowerShell è necessaria se la registrazione agente avrà esito negativo dopo l'installazione di Azure AD Connect.

PowerShell seguente comando è necessario solo quando l'integrità agente registrazione avrà esito negativo anche dopo una corretta installazione e configurazione di Azure AD Connect. I servizi di Azure Active Directory connettersi integrità verranno avviato dopo l'agente è stata registrata.

È possibile registrare manualmente l'agente di Azure Active Directory connettersi integrità per la sincronizzazione con il comando di PowerShell seguente:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Il comando accetta i parametri in seguito:

- AttributeFiltering: $true (impostazione predefinita) - se Azure AD Connect non è possibile sincronizzare l'attributo predefinito impostato ed è stato personalizzato per l'utilizzo di un set di attributo filtrato. $false in caso contrario.
- StagingMode: $false (impostazione predefinita) - se il server Azure AD Connect non è in modalità di $true di gestione temporanea se il server è configurato per essere in modalità di gestione temporanea.

Quando richiesto per l'autenticazione è necessario utilizzare lo stesso account di amministratore globale (ad esempio admin@domain.onmicrosoft.com) utilizzato per la configurazione di Azure AD Connect.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>L'installazione di Azure AD connettersi agente di integrità di Active Directory
Per avviare l'installazione dell'agente, fare doppio clic sul file .exe che è stato scaricato. Nella prima schermata, fare clic su Installa.

![Verificare Azure AD Connect integrità](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Al termine dell'installazione, fare clic su Configura ora.

![Verificare Azure AD Connect integrità](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Viene avviato un prompt dei comandi, seguito da alcune PowerShell che esegue registro AzureADConnectHealthADDSAgent. Quando viene richiesto di accedere a Azure, procedere e accesso.

![Verificare Azure AD Connect integrità](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

Dopo l'accesso, PowerShell continuerà. Dopo essere stato completato, è possibile chiudere PowerShell e la configurazione è stata completata.

A questo punto, i servizi devono essere avviati automaticamente se si consente l'agente di monitoraggio e la raccolta di dati. Se non sono stati soddisfatti tutti i prerequisiti descritti nelle sezioni precedenti, gli avvisi vengono visualizzati nella finestra di PowerShell. Assicurarsi di completare i [requisiti](active-directory-aadconnect-health-agent-install.md#requirements) prima di installare l'agente. La figura seguente è illustrato un esempio di questi errori.

![Verificare Azure AD Connect integrità per Active Directory](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Per verificare che l'agente è stato installato, cercare i seguenti servizi sul controller di dominio.

- Azure AD Connect dell'integrità di dominio Active Directory informazioni dettagliate sui servizi
- Azure AD Connect integrità AD DS monitoraggio del servizio

Se è stata completata la configurazione, questi servizi dovrebbero già essere in esecuzione. In caso contrario, essi vengono interrotti fino a ottenere la configurazione.

![Verificare Azure AD Connect integrità](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds-on-server-core"></a>L'installazione di Azure AD connettersi agente di integrità di Active Directory su Server Core.
Dopo aver installato il file .exe, è possibile completare il processo di registrazione utilizzando il comando PowerShell seguente:

`Register-AzureADConnectHealthADDSAgent -Credential $cred`

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Configurare Azure Active Directory connettersi agenti integrità per utilizzare HTTP Proxy
È possibile configurare Azure Active Directory connettersi agenti integrità per l'uso con un HTTP Proxy.

>[AZURE.NOTE]
- Utilizzo "Netsh WinHttp impostazione ProxyServerAddress" non è supportato come l'agente utilizza System.Net per creare richieste web invece di Microsoft Windows HTTP Services.
- L'indirizzo Http Proxy configurato viene utilizzato per i messaggi Https crittografati pass-through.
- Proxy autenticato (con HTTPBasic) non sono supportate.

### <a name="change-health-agent-proxy-configuration"></a>Modifica dello stato dell'agente Proxy configurazione
Sono disponibili le opzioni seguenti per configurare Azure Active Directory connettersi Agente integrità per utilizzare un HTTP Proxy.

>[AZURE.NOTE]Tutti i servizi Agente integrità di connettersi Azure Active Directory, è necessario riavviare affinché le impostazioni del proxy da aggiornare. Eseguire il comando seguente:<br>
Restart-Service AdHealth *

#### <a name="import-existing-proxy-settings"></a>Importare le impostazioni di proxy esistente

##### <a name="import-from-internet-explorer"></a>Importazione da Internet Explorer
È possibile importare le impostazioni proxy HTTP di Internet Explorer, per essere utilizzato dagli integrità agenti connettersi Azure Active Directory. In ciascuno dei server che esegue l'Agente integrità, eseguire il comando PowerShell seguente:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Importare da WinHTTP
È possibile importare le impostazioni del proxy WinHTTP, per essere utilizzato dagli integrità agenti connettersi Azure Active Directory. In ciascuno dei server che esegue l'Agente integrità, eseguire il comando PowerShell seguente:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Specificare gli indirizzi Proxy manualmente
È possibile specificare un server proxy manualmente su ciascuno dei server che esegue l'Agente integrità eseguendo il seguente comando di PowerShell:

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Esempio: *myproxyserver AzureAdConnectHealthProxySettings Set - HttpsProxyAddress: 443*

- "indirizzo" può essere un nome di essere risolto server DNS o un indirizzo IPv4
- è possibile omettere "porta". Se omesso 443 viene scelto come porta predefinita.

#### <a name="clear-existing-proxy-configuration"></a>Cancella configurazione proxy esistente
È possibile annullare la configurazione del proxy esistente eseguendo il comando seguente:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Leggere le impostazioni del proxy corrente
Per leggere le impostazioni del proxy attualmente configurato, è possibile eseguire il comando seguente:

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Verificare la connessione di Azure Active Directory connettersi integrità dei servizi
È possibile che potrebbero verificarsi problemi che causano l'agente di Azure Active Directory connettersi integrità a perde la connettività con il servizio di Azure Active Directory connettersi integrità. Sono incluse problemi di rete, problemi di autorizzazione o diversi altri motivi.

Se l'agente è Impossibile inviare dati al servizio di Azure Active Directory connettersi integrità per più di due ore, viene indicato con l'avviso seguente nel portale: "integrità dei servizi di dati non sono aggiornati." È possibile verificare se interessato Azure AD Connect Agente integrità è in grado di caricare i dati al servizio di Azure Active Directory connettersi integrità eseguendo il seguente comando di PowerShell:

    Test-AzureADConnectHealthConnectivity -Role ADFS

Il parametro ruolo attualmente accetta i valori seguenti:

- ADFS
- Sincronizzazione
- AGGIUNGE

È possibile utilizzare il contrassegno - ShowResults nel comando per visualizzare registri dettagliati. Utilizzare l'esempio seguente:

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

>[AZURE.NOTE]Per usare lo strumento di integrazione applicativa, eseguire la registrazione agente. Se non si è possibile completare la registrazione agente, assicurarsi che siano stati soddisfatti tutti i [requisiti](active-directory-aadconnect-health-agent-install.md#requirements) per la salute la connessione di Azure Active Directory. Per impostazione predefinita verrà eseguito il test di connettività durante la registrazione agente.



## <a name="related-links"></a>Collegamenti correlati

* [Azure AD Connect integrità](active-directory-aadconnect-health.md)
* [Azure AD Connect operazioni integrità](active-directory-aadconnect-health-operations.md)
* [Collegare mediante Azure Active Directory integrità con ADFS](active-directory-aadconnect-health-adfs.md)
* [Utilizzo di Azure Active Directory connettersi integrità per la sincronizzazione](active-directory-aadconnect-health-sync.md)
* [Collegare mediante Azure Active Directory integrità con Active Directory](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect integrità domande frequenti](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect dell'integrità della cronologia delle versioni](active-directory-aadconnect-health-version-history.md)
