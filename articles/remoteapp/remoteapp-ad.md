
<properties 
    pageTitle="Azure Active Directory + requisiti di Active Directory per Azure RemoteApp | Microsoft Azure" 
    description="Informazioni su come configurare Active Directory per l'uso con RemoteApp di Azure." 
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



# <a name="azure-ad--active-directory-requirements-for-azure-remoteapp"></a>Azure Active Directory + requisiti di Active Directory per RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.


Per la raccolta di ibrido RemoteApp di Azure o per una raccolta di cloud che si desidera impostare la federazione con AD Connect, è necessario eseguire le operazioni seguenti.

### <a name="connect-azure-ad-and-active-directory"></a>Connettere Azure Active Directory e Active Directory

Se si desidera collegare al tenant di Azure Active Directory e gli ambienti di Active Directory locale, utilizzare AD Connect. Richiederà è solo [4 clic](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) per connettere due directory.

Nota: la sincronizzazione della Directory è necessario per gli insiemi di distribuzione ibrida.

### <a name="make-sure-your-domaincom-match"></a>Verificare che il "@domain.com" corrispondono
Prima di iniziare, verificare che l'UPN per l'insieme di strutture locale corrisponda suffisso del dominio di Azure Active Directory. 

Dopo aver configurato il suffisso UPN del dominio in Azure Active Directory, tutti gli utenti accedendo Azure RemoteApp accederà come “user@ <the suffix you set up>". Assicurarsi che gli utenti possono accedere anche con lo stesso user@suffix nel dominio locale. In alcuni casi è possibile impostare un nome di dominio in Azure Active Directory durante la specifica un suffisso di dominio diverso per l'utente in Importo Amm.to In questo caso, gli utenti non potranno connettersi a qualsiasi dominio computer o risorse mediante RemoteApp di Azure.

Se, ad esempio, impostare il suffisso dominio UPN AAD come contoso.com, ma alcuni utenti su locale/annuncio sono configurati per accedere con @contoso.uk, tali utenti non saranno in grado di accedere correttamente a raccolta ARA. Gli utenti UPN in AAD e Active Directory devono corrispondere per l'accesso come possibili"

### <a name="create-objects-for-azure-remoteapp"></a>Creare oggetti per RemoteApp di Azure
Inoltre, è necessario creare i seguente locale degli oggetti Active Directory:

- Un account del servizio per consentire l'accesso alle risorse del dominio per i programmi RemoteApp associando punti finali RDSH il dominio locale.
- Un'unità Organizzativa per contenere oggetti computer RemoteApp. Utilizzo dell'unità Organizzativa è consigliabile (ma non è necessario) per isolare gli account e i criteri che verrà utilizzata con RemoteApp.

È necessario entrambi gli oggetti quando si crea la raccolta di RemoteApp, sarà necessario eseguire le operazioni seguenti prima di tutto.