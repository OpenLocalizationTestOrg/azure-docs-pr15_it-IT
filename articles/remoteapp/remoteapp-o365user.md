
<properties 
    pageTitle="Come utilizzare RemoteApp di Azure con gli account utente di Office 365 | Microsoft Azure"
    description="Informazioni su come utilizzare RemoteApp di Azure con gli account utente di Office 365"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-azure-remoteapp-with-office-365-user-accounts"></a>Come utilizzare RemoteApp di Azure con gli account utente di Office 365

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Se si ha un abbonamento a Office 365 è necessario un Azure Active Directory in cui sono memorizzati i nomi utente e password utilizzati per accedere ai servizi di Office 365. Ad esempio, quando gli utenti attivare Office 365 ProPlus vengano autenticare Azure Active Directory per controllare le licenze. La maggior parte dei clienti che vogliono usare la stessa directory con RemoteApp di Azure.

Se si distribuisce Azure RemoteApp è probabile che si utilizza una sottoscrizione Azure associata a un annuncio di Azure diverso. Per utilizzare la directory di Office 365, sarà necessario spostare la sottoscrizione Azure nella directory.

Per informazioni su come distribuire le applicazioni client di Office 365, vedere [come utilizzare l'abbonamento a Office 365 con RemoteApp di Azure](remoteapp-officesubscription.md).
 
## <a name="phase-1-register-your-free-office-365-azure-active-directory-subscription"></a>Fase 1: Registrare l'abbonamento di Office 365 Azure Active Directory gratuito
Se si utilizza il portale classico Azure, usare i passaggi descritti [registrare il proprio abbonamento gratuito di Azure Active Directory](https://technet.microsoft.com/library/dn832618.aspx) per ottenere accesso amministrativo a un annuncio Azure tramite il portale di gestione di Azure. Come risultato di questa procedura dovrebbe essere possibile accedere al portale Azure e vedere directory sono – a questo punto non risulta molto più poiché l'abbonamento completo Azure che si usa con Azure RemoteApp in un'altra directory.

Tenere presente il nome e la password dell'account administrator creata in questo passaggio, in cui sarà necessario nella fase 2.

Se si utilizza il portale di Azure, verificare le [istruzioni per eseguire la registrazione e attivare un gratuito Azure Active Directory nel portale di Office 365](http://azureblogger.com/2016/01/how-to-register-and-activate-a-free-azure-active-directory-using-office-365-portal/).

## <a name="phase-2-change-the-azure-ad-associated-with-your-azure-subscription"></a>Fase 2: Modificare Azure AD associato all'abbonamento Azure.
Occorre modificare l'abbonamento Azure dalla directory corrente nella directory di Office 365 che è lavorati in fase 1.

Seguire le istruzioni descritte in [modificare il tenant di Azure Active Directory in Azure RemoteApp](remoteapp-changetenant.md). Prestare particolare attenzione per la procedura seguente:

- Passaggio #1: Se è stata distribuita Azure RemoteApp (ARA) nella sottoscrizione, assicurarsi che per rimuovere tutti gli account utente di Azure Active Directory raccolte ARA prima di tutto, prima di qualsiasi altro elemento. In alternativa, è possibile eliminare le raccolte esistenti.
- Passaggio #2: Si tratta di un elemento cruciale. È necessario usare un account Microsoft (ad esempio @outlook.com) come amministratore di servizio dell'abbonamento; in questo modo Impossibile abbiamo tutti gli account utente da esistente Azure AD collegato alla sottoscrizione, in questo caso, è non sarà possibile spostarlo in un annuncio di Azure diverso.
- Passaggio 4 #: Quando si aggiunge una directory esistente, il sistema verrà chiesto di accedere con l'account dell'amministratore per tale directory. Assicurarsi di usare l'account di amministratore dalla fase 1.
- Passaggio #5: Modificare la directory padre della sottoscrizione nella directory di Office 365. Il risultato finale dovrebbe essere che in Impostazioni -> sottoscrizioni l'abbonamento sono elencati nella directory di Office 365. 
![Modificare la directory padre della sottoscrizione](./media/remoteapp-o365user/settings.png)
 

A questo punto l'abbonamento di Azure RemoteApp è associato a di Office 365 Azure Active Directory; è possibile utilizzare gli account utente di Office 365 esistenti con Azure RemoteApp!




