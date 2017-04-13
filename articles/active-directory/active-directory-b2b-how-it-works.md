<properties
   pageTitle="Anteprima di collaborazione Azure Active Directory B2B: come funziona | Microsoft Azure"
   description="Viene descritto come la collaborazione di Azure Active Directory B2B supporta le relazioni tra società abilitando business partner in modo selettivo accedere alle applicazioni aziendali"
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
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-how-it-works"></a>Anteprima di collaborazione Azure Active Directory B2B: come funziona
Azure Active Directory B2B collaborazione basata su un invito e riscattare modello. Fornire gli indirizzi di posta elettronica dei partecipanti che si desidera utilizzare, insieme alle applicazioni che di utilizzare desiderato. Azure Active Directory li invia un invito di posta elettronica con un collegamento. L'utente partner segue il collegamento e viene richiesto di accedere con loro account Azure Active Directory o l'accesso per un nuovo Azure AD account.

1. L'amministratore Invita utenti partner per caricare [un file CSV strutturati](active-directory-b2b-references-csv-file-format.md) tramite il portale di Azure.
2. Il portale invia invita messaggi di posta elettronica agli utenti partner.
3. Utenti del partner fare clic sul collegamento nel messaggio di posta elettronica e vengono richiesto per accedere con le proprie credenziali di lavoro (se sono già presenti Azure Active Directory) oppure per effettuare l'iscrizione di un utente di collaborazione di Azure Active Directory B2B.
4. Gli utenti partner vengono reindirizzati all'applicazione che sono state invitate a cui hanno ora l'accesso.

## <a name="directory-operations"></a>Operazioni di directory
Gli utenti partner esistono nel Azure Active Directory come utenti esterni. Questa indica che l'amministratore può effettuare il provisioning di licenze, assegnare l'appartenenza ai gruppi e ulteriormente concedere l'accesso alle applicazioni aziendali tramite il portale di Azure o con PowerShell Azure come per gli utenti della società.

Durante un annuncio di Azure pagato sottoscrizione (Basic o Premium) non è necessario utilizzare Azure Active Directory B2B, tenant che hanno un pagamento abbonamento di Azure Active Directory (Basic o Premium) ricevere i seguenti vantaggi aggiuntivi:

 - Gli amministratori possono assegnare gruppi in App per semplificare la gestione di accesso degli utenti invitati.
 - Esperienza di rimborso, fornendo maggiore contesto per invitata utenti partner amministratore tenant personalizzazione viene utilizzata per personalizzare i messaggi di posta elettronica di invito.

## <a name="related-articles"></a>Articoli correlati
 Individuare gli altri articoli sulla collaborazione di Azure Active Directory B2B

 - [Che cos'è la collaborazione di Azure Active Directory B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [Descrizione dettagliata](active-directory-b2b-detailed-walkthrough.md)
 - [Informazioni sui formati file CSV](active-directory-b2b-references-csv-file-format.md)
 - [Formato di token di utenti esterni](active-directory-b2b-references-external-user-token-format.md)
 - [Modifiche degli attributi oggetto utente esterno](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [Limitazioni relative all'anteprima corrente](active-directory-b2b-current-preview-limitations.md)
 - [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
