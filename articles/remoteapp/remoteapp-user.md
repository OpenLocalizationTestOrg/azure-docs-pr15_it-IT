<properties
    pageTitle="Aggiungere un utente per la raccolta di Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come aggiungere utenti alla raccolta RemoteApp di Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="how-to-add-a-user-to-your-azure-remoteapp-collection"></a>Come aggiungere un utente alla raccolta RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Prima che gli utenti possono visualizzare e usare le App in RemoteApp di Azure, è necessario concedere loro l'accesso alla raccolta. La parte facile: scheda **L'accesso degli utenti** immettere le informazioni sull'account per l'utente e quindi fare clic sul segno di spunta.

Informazioni sull'account è necessario? Che cambiano in base al tipo di raccolta creato (cloud o ibrida) e se si usa Office 365 ProPlus in tale raccolta.

## <a name="supported-user-identities"></a>Identità utente supportati

I tipi di raccolte diverse (cloud e ibrida) supportano l'utilizzo di diverse identità utente per l'accesso alle applicazioni.  

Per una raccolta di ibrido di RemoteApp, è necessario configurare un'infrastruttura di dominio Active Directory in locale e un tenant di Azure Active Directory con integrazione della Directory (e facoltativamente servizio single sign-on). Inoltre, è necessario creare alcuni oggetti Active Directory nella directory locale.  

Per una raccolta di cloud di RemoteApp, è possibile qualsiasi utente con conflitto Azure Active Directory supporta identità concedere l'accesso degli utenti a RemoteApp per includere Accounts Microsoft.  Vedere la tabella seguente.

Utenti di Office 365 sono gli utenti di Azure Active Directory. Se hanno ibrido di Azure Active Directory, gli account, la sincronizzazione Directory sia possibile concedere l'accesso degli utenti in una distribuzione ibrida RemoteApp.   

È possibile utilizzare questa tabella come riferimento rapido per il quale identità è supportato l'insieme e quali sono i requisiti di Active Directory.

|Account utente |Cloud   |Ibrido|
|--------------|--------|------|
|Account Microsoft|     Sì|    No|
|Azure Active Directory (AD Azure)| | |
|Solo Azure cloud di Active Directory    |Sì    |No |
|Sincronizzazione Active Directory con la sincronizzazione delle password  |Sì    |Sì    |
|Sincronizzazione Active Directory senza la sincronizzazione delle password|  Sì |No |
|Sincronizzazione Active Directory con ADFS  |Sì    |Sì    |
|[Azure 3o festa supportati provider di identità](https://msdn.microsoft.com/library/azure/jj679342.aspx)  (ad esempio Ping) |Sì    |Sì|
|Autenticazione a più fattori    |Sì    |Sì    |

Verificare le [informazioni](remoteapp-ad.md) sulla configurazione di Active Directory per RemoteApp.


> [AZURE.NOTE] Gli utenti di Azure Active Directory devono essere compreso il tenant di cui è associato all'abbonamento. (È possibile visualizzare e modificare l'iscrizione nella scheda **Impostazioni** nel portale. Vedere [modificare il tenant di Azure Active Directory utilizzato da RemoteApp](remoteapp-changetenant.md) per ulteriori informazioni).

## <a name="office-365-proplus-user-account-information"></a>Informazioni sull'account di Office 365 ProPlus utente
Se si utilizza l'immagine del modello di Office 365 ProPlus nella propria raccolta *o* se è stata creata un'immagine personalizzata che utilizza Office 365, è consentito solo per aggiungere gli utenti di Azure Active Directory che hanno abbonamenti a Office 365 per il dominio predefinito del proprio abbonamento. Per ulteriori informazioni, vedere [uso di Office 365 con RemoteApp di Azure](remoteapp-o365.md) .
