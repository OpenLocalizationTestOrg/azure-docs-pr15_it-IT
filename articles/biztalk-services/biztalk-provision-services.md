<properties
    pageTitle="Creazione di servizi di Windows Azure BizTalk nel portale di Azure | Microsoft Azure"
    description="Informazioni su come effettuare il provisioning o creare Azure BizTalk servizi nel portale di Azure; MABS, WABS"
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>



# <a name="create-biztalk-services-using-the-azure-portal"></a>Creare servizi BizTalk tramite il portale di Azure

Creazione di servizi di Windows Azure BizTalk nel portale di Azure.

> [AZURE.TIP] Per accedere al portale di Azure, è necessario un account Azure e Azure abbonamento. Se non si dispone di un account, è possibile creare un account di valutazione gratuito nell'arco di qualche minuto. Vedere [Azure versione di valutazione gratuita](http://go.microsoft.com/fwlink/p/?LinkID=239738).

## <a name="create-a-biztalk-service"></a>Creare un servizio BizTalk
A seconda dell'edizione che scelto, non tutte le impostazioni di BizTalk Service potrebbero essere disponibili.

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel riquadro di spostamento inferiore, selezionare **Nuovo**:  
![Selezionare il pulsante nuovo][NEWButton]

3. Selezionare **servizi APP** > **servizio BIZTALK** > **Crea personalizzato**:  
![Selezionare BizTalk Service e creare personalizzati][NewBizTalkService]

4. Immettere le impostazioni di BizTalk Service:

    <table border="1">
    <tr>
    <td><strong>Nome del servizio BizTalk</strong></td>
    <td>È possibile immettere un nome di ma specifico. Seguono alcuni esempi:<br/><br/>
    <em>società</em>. biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>. biztalk.windows.net<br/>
    <em>applicazione corrente</em>. biztalk.windows.net<br/><br/>". biztalk.windows.net" vengono automaticamente aggiunti al nome immesso. Verrà creato un URL che viene utilizzato per accedere ad esempio il BizTalk Service <strong>https://<em>applicazione corrente</em>. biztalk.windows.net</strong>.
    </td>
    </tr>
    <tr>
    <td><strong>Edition</strong></td>
    <td>Se si è in fase di test/sviluppo, scegliere <strong>sviluppo</strong>. Se si è in fase di produzione, utilizzare la <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">servizi BizTalk: edizioni grafico</a> per determinare se <strong>Premium</strong>, <strong>Standard</strong>o <strong>base</strong> è la scelta corretta per lo scenario aziendale.
    </td>
    </tr>
    <tr>
    <td><strong>Area geografica</strong></td>
    <td>Selezionare l'area geografica per ospitare i BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>URL del dominio</strong></td>
    <td><strong>Facoltativo</strong>. Per impostazione predefinita, l'URL di dominio è <em>YourBizTalkServiceName</em>. biztalk.windows.net. È anche possibile immettere un dominio personalizzato. Ad esempio, se il dominio <em>contoso</em>, è possibile immettere: <br/><br/>
    <em>Società</em>. contoso.com<br/>
    <em>MyCompanyMyApplication</em>. contoso.com<br/>
    <em>Applicazione corrente</em>. contoso.com<br/>
    <em>YourBizTalkServiceName</em>. contoso.com<br/>
    </td>
    </tr>
    </table>
Selezionare la freccia avanti.

5. Immettere lo spazio di archiviazione e le impostazioni del Database:

    <table border="1">
    <tr>
    <td><strong>Account di archiviazione di monitoraggio/archiviazione</strong></td>
    <td>Selezionare un account di archiviazione esistente oppure creare un nuovo account di archiviazione. <br/><br/>Se si crea un nuovo account di archiviazione, immettere il <strong>Nome dell'Account di archiviazione</strong>.</td>
    </tr>
    <tr>
    <td><strong>Database beni mobili</strong></td>
    <td>Se si usa un Database di SQL Azure, non può essere utilizzato da un'altra BizTalk Service. È necessario il nome di accesso e password immessa quando è stato creato il Server di Database SQL Azure.<br/><br/><strong>Suggerimento</strong> Creare il database di rilevamento e l'account di archiviazione monitoraggio/archiviazione nella stessa regione BizTalk Service.</td>
    </tr>
    </table>
Selezionare la freccia avanti.

6. Immettere le impostazioni del Database:

    <table border="1">
    <tr>
    <td><strong>Nome</strong></td>
    <td>Disponibile quando si <strong>Crea una nuova istanza di Database SQL</strong> sia selezionata nella schermata precedente.
    <br/><br/>
   Immettere un nome di Database SQL che verrà utilizzato dal BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>Server</strong></td>
    <td>Disponibile quando si <strong>Crea una nuova istanza di Database SQL</strong> sia selezionata nella schermata precedente.
    <br/><br/>
   Selezionare un Server di Database SQL esistente o creare un nuovo Database SQL server.</td>
    </tr>
    <tr>
    <td><strong>Nome di accesso server</strong></td>
    <td>Immettere il nome utente di accesso.</td>
    </tr>
    <tr>
    <td><strong>Password di accesso al server</strong></td>
    <td>Immettere la password di accesso.</td>
    </tr>
    <tr>
    <td><strong>Area geografica</strong></td>
    <td>Disponibile quando si <strong>Crea una nuova istanza di Database SQL</strong> sia selezionata. Selezionare l'area geografica per ospitare i Database di SQL.</td>
    </tr>
    </table>

Selezionare il segno di spunta per completare la procedura guidata. Viene visualizzata l'icona di stato di avanzamento:  
![Icona di stato di avanzamento viene visualizzata quando completa][ProgressComplete]

Al termine, il servizio di BizTalk Azure vengono create e sono pronti per le applicazioni. Le impostazioni predefinite sono sufficienti. Se si desidera modificare le impostazioni predefinite, selezionare **Servizi BIZTALK** nel riquadro di spostamento a sinistra e quindi selezionare il BizTalk Service. Impostazioni aggiuntive vengono visualizzate le [schede del Dashboard, Monitor e scala](biztalk-dashboard-monitor-scale-tabs.md) nella parte superiore.

In base allo stato di BizTalk Service, esistono alcune operazioni che non può essere completate. Per un elenco di queste operazioni, passare a [BizTalk Services stato grafico](biztalk-service-state-chart.md).


## <a name="post-provisioning-steps"></a>Passaggi post-implementazione

-  [Installare il certificato in un computer locale](#InstallCert)
-  [Aggiungere un certificato di produzione](#AddCert)
-  [Ottenere lo spazio dei nomi di controllo di accesso](#ACS)

#### <a name="InstallCert"></a>Installare il certificato in un computer locale
Come parte di BizTalk Service il provisioning di un certificato autofirmato viene creato e associato all'abbonamento BizTalk Service. È necessario scaricare il certificato e installarlo nel computer in cui si distribuiscono applicazioni BizTalk Service o inviare messaggi a un endpoint BizTalk Service.

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selezionare **Servizi BIZTALK** nel riquadro di spostamento a sinistra e quindi selezionare l'abbonamento BizTalk Service.
3. Selezionare la scheda **Dashboard** .
4. Fare clic **su Download certificato SSL**:  
![Modificare il certificato SSL][QuickGlance]
5. Fare doppio clic sul certificato ed eseguire la procedura guidata per installare il certificato. Assicurarsi di installare il certificato nell'archivio **Autorità di certificazione attendibile** .

#### <a name="AddCert"></a>Aggiungere un certificato di produzione
Il certificato autofirmato creato automaticamente durante la creazione di servizi di BizTalk deve essere utilizzato in ambienti di sviluppo solo. Per gli scenari di produzione, sostituirlo con un certificato di produzione.

1. Nella scheda **Dashboard** , selezionare **Il certificato SSL di aggiornamento**.
2. Selezionare il certificato SSL privato (*CertificateName*PFX) che include il nome BizTalk Service, immettere la password e quindi fare clic sul segno di spunta.

#### <a name="ACS"></a>Ottenere lo spazio dei nomi di controllo di accesso

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selezionare **Servizi BIZTALK** nel riquadro di spostamento a sinistra e quindi selezionare il BizTalk Service.
3. Nella barra delle applicazioni, selezionare **Le informazioni di connessione**:  
![Selezionare le informazioni di connessione][ACSConnectInfo]

4. Copiare i valori di controllo di accesso.

Quando si distribuisce un progetto BizTalk Service da Visual Studio, immettere questo spazio dei nomi di controllo di accesso. Lo spazio dei nomi di controllo dell'accesso viene creata automaticamente per il BizTalk Service.

I valori di controllo dell'accesso utilizzabili con qualsiasi applicazione. Dopo avere creato i servizi BizTalk Azure, questo spazio dei nomi di controllo dell'accesso controlla l'autenticazione con la distribuzione di BizTalk Service. Se si desidera modificare la sottoscrizione o gestire lo spazio dei nomi, selezionare **ACTIVE DIRECTORY** nel riquadro di spostamento a sinistra e quindi selezionare lo spazio dei nomi. La barra attività Elenca le opzioni disponibili.

Facendo clic su **Gestisci** apre il portale di Gestione controllo di accesso. Nel portale di Gestione controllo di accesso, BizTalk Service utilizza **identità di servizio**:  
![Portale di gestione controllare l'identità di servizio ACS in Access][ACSServiceIdentities]

L'identità di servizio controllo di accesso è un insieme di credenziali che consentono di applicazioni o i client per eseguire l'autenticazione direttamente con il controllo di accesso e la ricezione di un token.

> [AZURE.IMPORTANT]BizTalk Service utilizza **proprietario** per l'identità del servizio predefinito e il valore di **Password** . Se si utilizza il valore di chiave simmetrico anziché il valore di Password, può verificarsi l'errore seguente.<br/><br/>*Impossibile connettersi all'account del servizio di Gestione controllo di accesso con le credenziali specificate*

[Gestione di ACS Namespace](https://msdn.microsoft.com/library/azure/hh674478.aspx) sono elencati alcuni suggerimenti e linee guida.

## <a name="requirements-explained"></a>Requisiti spiegati

Questi requisiti si applicano all'edizione gratuito.
<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>È necessario</strong></td>
        <td><strong>A cosa serve</strong></td>
</tr>
<tr>
<td>Abbonamento Azure</td>
<td>L'abbonamento determina chi può accedere al portale di Azure. Il titolare di Account creato l'abbonamento <a HREF="https://account.windowsazure.com/Subscriptions">Azure</a>sottoscrizioni.
<br/><br/>
L'account Azure possa hanno più abbonamenti e può essere gestito da chiunque sia consentito. Ad esempio, il titolare di account Azure crea una sottoscrizione denominata <em>BizTalkServiceSubscription</em> e consente agli amministratori BizTalk all'interno dell'azienda (ad esempio ContosoBTSAdmins@live.com) l'accesso all'abbonamento. In questo scenario, gli amministratori di BizTalk accedere al portale di Azure e dispone dei diritti di amministratore completi per tutti i servizi ospitati in abbonamento, quali servizi BizTalk di Windows Azure. Gli amministratori di BizTalk non sono contenitori account Azure e pertanto non hanno accesso a tutte le informazioni di fatturazione.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Gestire le sottoscrizioni e gli account di archiviazione nel portale di Azure</a> fornisce altre informazioni.
</td>
</tr>
<tr>
<td>Database SQL Azure</td>
<td>Archivia le tabelle, visualizzazioni e stored procedure utilizzate dal BizTalk Service, inclusi i dati di verifica.
<br/><br/>
Quando si crea un BizTalk Service, puoi utilizzare un Server SQL Azure esistente, il Database di SQL Azure o creare automaticamente un nuovo Server o Database.
<br/><br/>
La scala di Database SQL viene configurata automaticamente. In genere, la scala predefinita è sufficiente per BizTalk Service. Modificare la scala influisce su prezzi. Vedere <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">gli account e fatturazione in Database SQL Azure</a>
<br/><br/>
<strong>Note</strong>
<br/>
<ul>
<li> Quando si crea un nuovo Azure SQL Server e Database, servizi di Windows Azure viene attivato automaticamente. BizTalk Service richiede servizi di Windows Azure sia abilitato.</li>
<li>Se si crea un nuovo Database SQL Azure in un Server SQL Azure esistente, le regole del firewall del Server non vengono modificate. Di conseguenza, è possibile che altri servizi di Windows Azure non consentiti l'accesso al database del Server.</li>
</ul>
</td>
</tr>
<tr>
<td>Spazio dei nomi del controllo dell'accesso Azure</td>
<td>Autentica con i servizi BizTalk Azure. Quando si distribuisce un progetto BizTalk Service da Visual Studio, immettere questo spazio dei nomi di controllo di accesso. Quando si crea un BizTalk Service, viene creata automaticamente lo spazio dei nomi di controllo di accesso.</td>
</tr>

<tr>
<td>Account di archiviazione Azure</td>
<td>Fornisce l'accesso alle tabelle, BLOB e code utilizzate dal BizTalk Service per salvare le operazioni seguenti:

<ul>
<li>File di log monitorare BizTalk Service. L'output monitoraggio viene inoltre visualizzato nella scheda **monitoraggio** nel portale di Azure.</li>
<li>Quando si crea un X12 o AS2 concordanza tra partner, è possibile abilitare la funzionalità di archiviazione archiviare le proprietà del messaggio. Questi dati vengono salvati in account di archiviazione.</li>
</ul>
<br/>
Quando si crea un BizTalk Service, si utilizza un account di archiviazione esistente o creare automaticamente un nuovo account di archiviazione.
<br/><br/>
Le impostazioni di archiviazione predefinito sono sufficienti per BizTalk Service.
<br/><br/>
Quando si crea un account di archiviazione, vengono creati automaticamente una chiave primaria e chiave secondaria. Questi tasti controllano l'accesso al proprio account di archiviazione. BizTalk Service usa automaticamente la chiave primaria.
<br/><br/>
Per ulteriori informazioni, vedere <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">lo spazio di archiviazione</a> .
</td>
</tr>

<tr>
<td>Certificato SSL privato</td>
<td>
Quando viene creato un servizio di BizTalk Azure, viene creato anche un URL HTTPS che include il nome BizTalk Service. Questo URL viene configurato automaticamente per l'utilizzo di un certificato autofirmato solo sviluppo. Per produzione, è necessario un certificato SSL privato.
<br/><br/>
<strong>Informazioni sul certificato SSL importanti</strong>

<ul>
<li>La data di scadenza certificato deve essere inferiore a 5 anni.</li>
<li>Tutti i certificati privati richiedono una password. Conoscere la password e come ottimale, condividere questa password con gli amministratori.</li>
<li>Certificati autofirmati vengono utilizzati in un ambiente di sviluppo di test /. Quando si utilizzano certificati autofirmati, importare il certificato nell'archivio certificati personale e l'archivio certificati Autorità di certificazione radice attendibili.</li>
</ul>
<br/>Quando si invia la richiesta di certificato di produzione autorità di certificazione, fornire le proprietà di certificato seguenti:
<br/>

<ul>
<li><strong>Utilizzo chiave avanzato</strong>: almeno i servizi di Windows Azure BizTalk richiede l'autenticazione Server.</li>
<li><strong>Nome comune</strong>: immettere il nome di dominio completo (FQDN) dell'URL del servizio BizTalk Azure. Vedere <a HREF="#BizTalk">creare un servizio BizTalk</a> in questo articolo.</li>
</ul>
<br/>
Dopo la creazione di BizTalk Service, è possibile aggiungere un certificato nuovo o diverso.
</td>
</tr>
</table>



## <a name="hybrid-connections"></a>Connessioni ibrido

Quando si crea un servizio di BizTalk Azure, è disponibile la scheda **Connessioni ibrido** :

![Scheda connessioni ibrido][HybridConnectionTab]

Ibrido connessioni vengono utilizzate per la connessione di un sito Web Azure o servizi di telefonia mobile Azure a qualsiasi risorsa locale che utilizza una porta TCP statica, ad esempio SQL Server, MySQL, API Web HTTP, servizi mobili e la maggior parte dei servizi Web personalizzati.  Connessioni ibrida e il servizio scheda BizTalk sono diversi. Il servizio scheda BizTalk viene utilizzato per connettere servizi BizTalk di Windows Azure a un sistema di linee di Business (Line) locale.

 Vedere [Connessioni ibrido](integration-hybrid-connection-overview.md) per ulteriori informazioni, incluse la creazione e gestione delle connessioni ibrida.


## <a name="next-steps"></a>Passaggi successivi

Una volta creato, BizTalk Service acquisire familiarità con i diversi [servizi BizTalk: schede del Dashboard, monitorare e scala](biztalk-dashboard-monitor-scale-tabs.md). Il BizTalk Service è pronta per le applicazioni. Per avviare la creazione di applicazioni, passare a [Servizi BizTalk di Windows Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Vedere anche
- [BizTalk servizi: Grafico edizioni](biztalk-editions-feature-chart.md)<br/>
- [BizTalk servizi: Grafico stato](biztalk-service-state-chart.md)<br/>
- [Servizi di BizTalk: Eseguire il Backup e ripristino](biztalk-backup-restore.md)<br/>
- [Servizi BizTalk: limitazione](biztalk-throttling-thresholds.md)<br/>
- [BizTalk Services: Nome autorità che emette e chiave](biztalk-issuer-name-issuer-key.md)<br/>
- [Come posso iniziare a usare Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
- [Connessioni ibrido](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
