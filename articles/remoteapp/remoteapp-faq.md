<properties 
    pageTitle="Azure RemoteApp domande frequenti su | Microsoft Azure" 
    description="Informazioni su come ottenere il massimo domande frequenti su Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="swadhwa" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="azure-remoteapp-faq"></a>Domande frequenti su RemoteApp Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Sono state sentiti le domande seguenti sul RemoteApp di Azure. Aggiungere altri utenti? Visitare i [forum RemoteApp](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) e indicare cosa occorre sapere o elenco a discesa un commento di sotto.

## <a name="cant-find-what-youre-looking-for-have-a-question-we-didnt-answer"></a>Non è possibile trovare ciò che sta cercando? Aggiungere una domanda che non rispondere?
Se non si trovano le informazioni necessarie o si dispone di una domanda aggiuntiva è stiamo non dettagliata relativa agli qui, torna a [forum RemoteApp di Azure](http://aka.ms/araforum) e pubblicare domande sono. È sempre possibile aggiungere altre risposte qui.

## <a name="what-is-azure-remoteapp"></a>Che cos'è RemoteApp di Azure? ##


- **Che cos'è RemoteApp di Azure?** RemoteApp è che un servizio di Azure consente di fornire l'accesso remoto sicuro alle applicazioni da molti dispositivi diversi utente. Per ulteriori informazioni su [Azure RemoteApp](remoteapp-whatis.md).
- **Quali sono le opzioni di distribuzione?** Esistono due tipi di raccolte RemoteApp: cloud e ibridi. Quale è necessario dipende da diversi fattori, come la necessità di aggiunta al dominio. Soffermarsi su tutte le decisioni [qui](remoteapp-collections.md).

## <a name="quick-tips-on-using-azure-remoteapp"></a>Suggerimenti sull'uso di Azure RemoteApp ##
- **Quanto tempo occorre perché si interrompe la connessione? Per quanto tempo è possibile essere inattivo prima di eseguire me l'avvio?** 4 ore. Se si o uno degli utenti è inattivo per 4 ore, è necessario essere automaticamente eseguita la disconnessione RemoteApp di Azure. Estrarre le altre impostazioni predefinite in [abbonamento Azure e limiti del servizio, le quote e i vincoli](../azure-subscription-service-limits.md).
- **È possibile provare il servizio gratuitamente?** Sì. Per 30 giorni è disponibile una versione di valutazione gratuita. Al termine della riproduzione di prova, è possibile passare a un account a pagamento (che è possibile usare in produzione) o sospendere l'utilizzo del servizio. Avviare la versione di valutazione gratuita facendo clic su [portal.azure.com](http://portal.azure.com) , creare una nuova istanza RemoteApp. Con la versione di valutazione gratuita, è possibile creare 2 istanze di RemoteApp con 10 utenti per ogni istanza. Tenere presente che questa versione di valutazione risiede solo per 30 giorni.
## <a name="azure-remoteapp-subscription-details"></a>Dettagli dell'abbonamento RemoteApp Azure ##

- **Quali sono i limiti di servizio?** Per informazioni sulle impostazioni predefinite e limiti del servizio di Azure RemoteApp in [abbonamento Azure e limiti del servizio, le quote e i vincoli](../azure-subscription-service-limits.md). Indicare se hai altre domande.
- **Quanti utenti è necessario avere?** Esiste un minimo di 20 utenti. È possibile ripetere per super chiarezza - il valore minimo è 20. Verrà fatturato per 20. 
- **Quanto costa RemoteApp costo?** Controllare i [Dettagli prezzi RemoteApp di Azure ](https://azure.microsoft.com/pricing/details/remoteapp/).
- **Un tipo di raccolta costi maggiori rispetto a un altro?** Sì, è possibile, in base ai propri requisiti di raccolta. Un insieme di ibrido richiede una connessione da Azure RemoteApp alla rete locale. Se si usa una Route VNET/Express esistente, non esiste costi aggiuntivi. Ma se si utilizza un nuovo VNET Azure e un gateway o strada, viene addebitata per il [gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway) o [Strada](https://azure.microsoft.com/pricing/details/expressroute/). Il costo (vedere informazioni dettagliato sui collegamenti) nella parte superiore del mensile RemoteApp di Azure dei costi.

## <a name="collections---whats-supported-which-should-you-use-and-others"></a>Raccolte - che cos'è supportato, che è necessario utilizzare e gli altri utenti
- **Applicazioni personalizzate line-of-business (Line) sono supportate?** Sì. Per usare un'applicazione personalizzata in Azure RemoteApp, creare un' [immagine di modello personalizzato](remoteapp-create-custom-image.md)e quindi caricarlo alla raccolta RemoteApp.
- **Applicazione line personalizzati funzionano con Azure RemoteApp?** Il modo migliore per figura che fuori verificarne il funzionamento. Visitare il [Centro compatibilità RD](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Il metodo di distribuzione (cloud o ibrida) è più adatto per la propria organizzazione?** Gli insiemi di ibrido forniscono un'esperienza più completa se si vuole integrazione completa con single sign-on (SSO) e connettività di rete locale protetti. Raccolte cloud rappresentano un modo semplice e agile isolare la distribuzione utilizzando più metodi di autenticazione. Altre informazioni sulle [Opzioni di distribuzione](remoteapp-whatis.md).
- **Abbiamo SQL o in un altro database in locale o in Azure. Tipo di distribuzione è consigliabile utilizzare?** Dipende dove si trova il database back-end o SQL. Se il database si trova in una rete privata, utilizzare l'insieme di caratteristiche ibride. Se il database è esposto a Internet e consente di client connessioni per la connessione, è possibile utilizzare la raccolta cloud.
- **Informazioni sulle unità USB e porta seriale, condivisione degli Appunti e il reindirizzamento della stampante?** Tutte le funzionalità supportate in Azure RemoteApp. Reindirizzamento degli Appunti di condivisione e la stampante è attivato per impostazione predefinita. È possibile acquisire familiarità con il reindirizzamento [di seguito](remoteapp-redirection.md). 


## <a name="template-images"></a>Immagini dei modelli
- **È possibile utilizzare una cloud o la macchina virtuale esistente come modello per la raccolta RemoteApp?** Sì! Creare un'immagine in base a una macchina virtuale Azure, usare una delle immagini incluse con l'abbonamento o creare un'immagine personalizzata. Verificare le [Opzioni di immagine RemoteApp](remoteapp-imageoptions.md).


## <a name="network-options"></a>Opzioni di rete
- **La raccolta ibrido richiede un VNET. È possibile utilizzare il nostro VNET esistente?** È possibile se VNET esistente non è un VNET Azure. Vedere "passaggio 1: configurare la rete virtuale" nelle [istruzioni insieme ibrido](remoteapp-create-hybrid-deployment.md) per altre informazioni.
- **È possibile utilizzare una VNET con un insieme di cloud?** In effetti è possibile. [Creare un insieme di cloud](remoteapp-create-cloud-deployment.md), in particolare passaggio 1, per ulteriori informazioni, vedere.

## <a name="authentication-options"></a>Opzioni di autenticazione



- **Per quanto riguarda l'autenticazione? I metodi sono supportati?** La raccolta cloud supporta l'account di Microsoft e account Azure Active Directory, che sono anche l'account Office 365. La raccolta ibrido supporta solo gli account Azure Active Directory che sono stati sincronizzati (con uno strumento come [Sincronizzazione di Azure Active Directory](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)) da una distribuzione di Windows Server Active Directory; in particolare, o sincronizzate con l'opzione di sincronizzazione delle Password sincronizzato con la federazione di Active Directory Federation Services (ADFS) configurata. È inoltre possibile configurare [L'autenticazione a più fattori (MFA)](https://azure.microsoft.com/services/multi-factor-authentication/).

>[AZURE.NOTE]Gli utenti di Azure Active Directory devono essere compreso il tenant di cui è associato all'abbonamento. (È possibile visualizzare e modificare l'iscrizione nella scheda **Impostazioni** nel portale. Vedere [modificare il tenant di Azure Active Directory utilizzato da RemoteApp](remoteapp-changetenant.md) per ulteriori informazioni).

- **Perché non assegnare personale accesso all'account Azure Active Directory?** Gli utenti di Azure Active Directory devono essere dalla directory in cui è associato all'abbonamento. È possibile visualizzare o modificare la directory nella scheda Impostazioni nel portale. Per ulteriori informazioni, vedere [modificare il tenant di Azure Active Directory utilizzato da RemoteApp](remoteapp-changetenant.md) .

## <a name="clients---what-device-can-i-use-to-access-azure-remoteapp"></a>Client - dispositivo possibile consente di accedere RemoteApp di Azure?
È possibile trovare informazioni sulla buona client, inclusi i passaggi per installare il client diversi le [procedure di accesso le app di Azure RemoteApp](remoteapp-clients.md).

- **Quali dispositivi e sistemi operativi supportano da applicazioni client?**
Nome, il computer e Tablet: 
    - Windows 10 (versione di anteprima client)
    - Windows 8.1 e Windows 8
    - Windows 7 Service Pack 1
    - Mac OS X
    - Windows RT
    - Tablet Android
    - iPad e telefoni:
    - iPhone
    - Telefono Android
    - Windows Phone
 
    [Scaricare](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) un client RemoteApp ora.
- **Azure RemoteApp supporta Thin client?** Sì, sono supportati i client thin Windows incorporata seguenti:
    - Incorporati di Windows 7 Standard
    - Incorporati di Windows 8 Standard
    - Windows incorporati Industry 8.1 Pro
    - Windows 10 IoT Enterprise

- **La versione di Windows Server è supportata per Host sessione di Desktop remoto (RDSH)?** Windows Server 2012 R2.

## <a name="support-and-feedback"></a>Supporto e commenti


- **Che cos'è previsto il supporto di RemoteApp?** Supporto per la gestione della fatturazione e abbonamento disponibile senza costi. Supporto tecnico è disponibile tramite i [piani di servizio Azure](https://azure.microsoft.com/support/plans/). È inoltre possibile ottenere il supporto della community gratuito mediante al [forum di discussione Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp). 
- **Modalità di invio di commenti e suggerimenti** Visitare il [forum di commenti e suggerimenti](https://feedback.azure.com/forums/247748-azure-remoteapp/).
- **Chi è possibile comunicare per altre informazioni su RemoteApp di Azure** Oltre al [forum di discussione](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), che è ideale per pubblicare domande, è possibile unire le settimanale [Ask webinar esperti](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), in cui si parla tutto RemoteApp.
- **Informazioni sulla documentazione RemoteApp?** Ci fa piacere richiesto. Oltre al contenuto della Guida nel cassetto Guida portal (basta fare clic su **?** in qualsiasi pagina del portale) gli articoli seguenti sono disponibili istruzioni dettagliate su informazioni sui RemoteApp:
    - **Per iniziare:**
        - [Che cos'è RemoteApp?](remoteapp-whatis.md)
        - [Cosa contiene le immagini dei modelli RemoteApp?](remoteapp-images.md)
        - [Come funziona licenze?](remoteapp-licensing.md)
        - [Come RemoteApp e Office funzionano insieme?](remoteapp-o365.md)
        - [Reindirizzamento funzionamento in RemoteApp](remoteapp-redirection.md)?
    - **Distribuire:**
        - [Creare un'immagine di modello personalizzato](remoteapp-create-custom-image.md)
        - [Creare una raccolta ibrido](remoteapp-create-hybrid-deployment.md)
        - [Creare una raccolta di cloud](remoteapp-create-cloud-deployment.md)
        - [Configurare Azure Active Directory per RemoteApp](remoteapp-ad.md)
        - [Pubblicare un'app in RemoteApp](remoteapp-publish.md)
    - **Gestire:**
        - [Aggiunta di utenti](remoteapp-user.md)
        - [Procedure consigliate per la configurazione e uso RemoteApp](remoteapp-bestpractices.md)  

    Video! Sono inoltre una serie di video su RemoteApp. Alcuni forniscono Introduzione ([Introduzione a RemoteApp Azure](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) mentre gli altri utenti guidano l'utente nella distribuzione ([distribuzione Cloud](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) e [distribuzione ibrida](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Estrazione!

 
### <a name="help-us-help-you"></a>Aiutaci a consentono di 
Non tutti sanno che oltre alle valutazione in questo articolo e aggiunta di commenti verso il basso sotto, è possibile apportare modifiche all'articolo stesso. Qualcosa mancanti? Un problema? È stato possibile scrivere qualcosa che solo confusione? Scorrere verso l'alto e fare clic su **Modifica nella GitHub** per apportare modifiche - quelli venga recapitata a Microsoft per la revisione e quindi una volta abbiamo disconnettere su di essi, verranno visualizzate le modifiche e i miglioramenti a destra.
