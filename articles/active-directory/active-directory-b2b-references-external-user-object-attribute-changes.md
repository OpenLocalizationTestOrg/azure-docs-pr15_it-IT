<properties
   pageTitle="Utente esterno oggetto attributo viene modificato per l'anteprima di collaborazione di Azure Active Directory B2B | Microsoft Azure"
   description="Azure B2B Directory attiva supporta le relazioni tra società abilitando business partner in modo selettivo accedere alle applicazioni aziendali"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-external-user-object-attribute-changes"></a>Anteprima di collaborazione Azure Active Directory B2B: modifiche degli attributi oggetto utente esterno

Ogni utente in una directory di Azure Active Directory è rappresentato da un oggetto utente. L'oggetto utente in Active Directory Azure subisce modifiche degli attributi in varie fasi di collaborazione B2B invito-Riscatta flusso. L'utente che rappresenta l'utente partner nella directory ha attributi che cambiano in riscattare esecuzione, quando l'utente partner fa clic sul collegamento nel messaggio di posta elettronica di invito. In particolare:

- Gli attributi **SignInName** e **AltSecId** vengono inseriti
- L'attributo **DisplayName** le modifiche dal nome dell'entità utente (user_fabrikam.com#EXT#@contoso.com) il nome di accesso(user@fabrikam.com)

Verifica degli attributi seguenti in Azure Active Directory consentono di risolvere i problemi di un utente di partner è stato usato loro invito di collaborazione B2B o meno.

## <a name="related-articles"></a>Articoli correlati
Individuare gli altri articoli sulla collaborazione di Azure Active Directory B2B:

- [Che cos'è la collaborazione di Azure Active Directory B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Come funziona](active-directory-b2b-how-it-works.md)
- [Descrizione dettagliata](active-directory-b2b-detailed-walkthrough.md)
- [Informazioni sui formati file CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato di token di utenti esterni](active-directory-b2b-references-external-user-token-format.md)
- [Limitazioni relative all'anteprima corrente](active-directory-b2b-current-preview-limitations.md)
- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
