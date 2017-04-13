<properties
    pageTitle="Azure AD Connect in Germania Microsoft Cloud"
    description="Azure AD Connect verrà integrato nelle directory locali con Azure Active Directory. In questo modo è possibile fornire un'identità comune per le applicazioni di Office 365 e Azure SaaS integrate con Azure Active Directory."
    keywords="Introduzione a Azure AD Connect, informazioni generali su Azure AD Connect, che cos'è Azure AD Connect, installare active directory, Germania, nero foresta"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/08/2016"
    ms.author="billmath"/>

#<a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect in Microsoft Cloud Germania - anteprima pubblica

## <a name="introduction"></a>Introduzione
Azure AD Connect consente la sincronizzazione tra il Active Directory locale e Azure Active Directory.
Attualmente, molti degli scenari di [Microsoft Cloud Germania](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) deve essere eseguite dall'operatore. Quando si usa Microsoft Cloud Germania, è necessario essere tenere presente quanto segue:


- Su un server proxy per la sincronizzazione venga eseguita correttamente, è necessario aprire gli URL seguenti:
    - *. microsoftonline.de
    - *. windows.net
    - + Elenchi di revoche di certificati

- Quando accede a directory di Azure Active Directory, è necessario utilizzare un account del dominio onmicrosoft.de.
- Le caratteristiche seguenti non sono disponibili:
    - Azure AD Connect integrità
    - Aggiornamenti automatici
    - Writeback di password

## <a name="download"></a>Download
È possibile scaricare Azure AD Connect da e il Azure AD Connect all'interno del portale.  Seguire le istruzioni seguenti per individuare e il Azure AD Connect.

### <a name="the-azure-ad-connect-blade"></a>Azure AD Connect Blade

Dopo aver l'accesso al portale di Azure, eseguire le operazioni seguenti:

1. Passare alla visualizzazione
2.  Selezionare Azure Active Directory
3.  Quindi selezionare Azure AD Connect

Verrà visualizzato quanto segue:

![Azure AD Connect Blade](media\active-directory-aadconnect-germany\germany1.png)

 
Nella tabella seguente descrive le caratteristiche mostrate nella e.


Titolo|Descrizione|
----- | ----- |
STATO DI SINCRONIZZAZIONE|Di seguito è possibile sapere se la sincronizzazione è attivata o disabilitata.|
ULTIMA SINCRONIZZAZIONE|L'ultima volta una sincronizzazione completata completata.|
DOMINI FEDERATI|Mostra il numero di domini federati attualmente configurato.|


## <a name="installation"></a>Installazione
Per installare Azure AD Connect, è possibile utilizzare la documentazione [qui](active-directory-aadconnect.md#install-azure-ad-connect).

## <a name="advanced-features-and-additional-information"></a>Caratteristiche avanzate e altre informazioni
Per ulteriori informazioni e indicazioni sulla impostazioni personalizzate o configurazioni avanzate, iniziare con [integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).  Questa pagina contiene informazioni e collegamenti a ulteriori istruzioni.
