<properties
   pageTitle="Prerequisiti di catalogo dati Azure | Microsoft Azure"
   description="Azure prerequisiti catalogo dati, è necessario per iniziare a utilizzare il catalogo dati di Azure."
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

# <a name="azure-data-catalog-prerequisites"></a>Prerequisiti di catalogo dati Azure

## <a name="what-do-i-need-to-get-started-with-azure-data-catalog"></a>Informazioni necessarie iniziare a utilizzare il catalogo dati di Azure?

Esistono alcuni aspetti che è necessario a prendersi cura della prima che è possibile impostare **Il catalogo dati di Azure**. Niente paura: non richiedono tempo!

## <a name="azure-subscription"></a>Abbonamento Azure
Per impostare il catalogo dati di Azure, è necessario essere il proprietario o Comproprietario di un abbonamento Azure.

Sottoscrizioni di Azure consentono di organizzare accesso alle risorse di servizio cloud come catalogo dati di Azure. Sono inoltre consentono di gestire la modalità di segnalazione Uso risorse, fatturazione e pagato. Ciascuna sottoscrizione può avere un'installazione di fatturazione e pagamenti diversa, pertanto è possibile utilizzare diversi abbonamenti e diversi piani di reparto, project, filiale e così via. Ogni servizio cloud appartiene a un abbonamento ed è necessario avere un abbonamento prima che la configurazione del catalogo dati di Azure. Per ulteriori informazioni, vedere [gestire abbonamenti dei ruoli amministrativi](../active-directory/active-directory-assign-admin-roles.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Per impostare il catalogo dati di Azure, è necessario accedere con un account utente di Azure Active Directory.

Azure Active Directory (Azure Active Directory) offre un modo semplice per l'azienda gestire l'accesso, sia nel cloud e in locale e identità. Gli utenti possono utilizzare un singolo account aziendale o dell'istituto di istruzione per single sign-on per qualsiasi cloud e applicazione web locale. Catalogo dati di Azure utilizza Azure Active Directory per autenticare sign-on. Per ulteriori informazioni, vedere [Novità](../active-directory/active-directory-whatis.md)di Azure Active Directory.

> [AZURE.NOTE] Il [portale di Azure](http://portal.azure.com/) consente agli utenti di accedere con un Account Microsoft personale o un ufficio di Azure Active Directory o dell'istituto di istruzione account. Per configurare catalogo dati di Azure tramite il portale di Azure o tramite il [portale di catalogo dati](http://www.azuredatacatalog.com) è necessario accedere tramite un account Azure Active Directory, non un account personale.

## <a name="active-directory-policy-configuration"></a>Configurazione di criteri di Active Directory

In alcune situazioni, gli utenti possono verificarsi situazioni in cui è possibile accedere al portale di catalogo dati di Azure, ma quando tentano di accedere allo strumento di registrazione di origine dati che possono verificarsi un messaggio di errore che impedisce che l'accesso. Questo problema può verificarsi solo quando l'utente nella rete della società, o potrebbe verificarsi solo se l'utente si connette dall'esterno della rete aziendale.

Lo strumento di registrazione di origine dati utilizza l'autenticazione basata su moduli per convalidare gli accessi utente in Active Directory. Per l'accesso, l'autenticazione basata su deve essere abilitato in Criteri di autenticazione globali da un amministratore di Active Directory.

Il criterio di autenticazione globale consente metodi di autenticazione deve essere abilitata separatamente per intranet e le connessioni extranet, come illustrato di seguito. Se l'autenticazione basata su non abilitato per la rete da cui l'utente si connette, possono verificarsi errori di accesso.

 ![Criteri di autenticazione globali di Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Per ulteriori informazioni, vedere [Configurazione dei criteri di autenticazione](https://technet.microsoft.com/library/dn486781.aspx).
