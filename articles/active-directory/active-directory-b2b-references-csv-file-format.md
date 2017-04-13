<properties
   pageTitle="Formato del file CSV per l'anteprima di collaborazione di Azure Active Directory B2B | Microsoft Azure"
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
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-csv-file-format"></a>Anteprima di collaborazione Azure Active Directory B2B: formato del file CSV

La versione di anteprima di collaborazione di Azure Active Directory B2B richiede un file CSV che specifica le informazioni utente partner da caricare tramite il portale di Azure Active Directory. Il file CSV deve contenere etichette necessari riportata di seguito e campi facoltativi in base alle esigenze. Modificare il file CSV di esempio (sotto) senza modificare il controllo ortografico delle etichette nella prima riga.

>[AZURE.NOTE] È necessaria che il file CSV da analizzare correttamente la prima riga di etichette (ad esempio posta elettronica, DisplayName e così via). Il controllo ortografico dovrà corrispondere ai campi specificati di seguito. Queste etichette identificano il contenuto sotto di esse. Nei campi facoltativi che non sono necessari, le relative etichette possono essere rimossa dal file CSV. L'intera colonna può essere lasciato vuoto.

## <a name="required-fields-br"></a>Campi obbligatori: <br/>
**Messaggio di posta elettronica:** Indirizzo di posta elettronica di utenti invitati. <br/>
**DisplayName:** Nome visualizzato per utenti invitati (in genere, il nome e cognome).<br/>


## <a name="optional-fields-br"></a>Campi facoltativi: <br/>

**InvitationText:** Personalizzare testo e-mail di invito dopo la personalizzazione di app e prima del collegamento di rimborso.<br/>
**InvitedToApplications:** AppID alle applicazioni aziendali per assegnare gli utenti. AppID vengono recuperate nel PowerShell chiamando`Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/>
**InvitedToGroups:** ObjectID per i gruppi per aggiungere all'utente. ObjectID vengono recuperate nel PowerShell chiamando`Get-MsolGroup | fl DisplayName, ObjectId`<br/>
**InviteRedirectURL:** URL per indirizzare un utente invitato dopo l'accettazione di invito. Deve trattarsi di un URL specifico della società (ad esempio [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Se questo campo facoltativo non viene specificato, l'utente invitato è indirizzato al riquadro App Access dove è possibile accedere alle App aziendali scelta. L'URL di App Access pannello ha il formato `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/>
**CcEmailAddress**: copiare invito inviato indirizzo di posta elettronica. Se si utilizza il campo CcEmailAddress, l'invito non utilizzata per utente verificato di posta elettronica o la creazione dei tenant.<br/>
**Lingua:** Lingua invito posta elettronica e il rimborso per un'esperienza utente, con "en" (in inglese) come predefinito quando non viene specificato. Altri 10 supportati lingua codici sono:<br/>
1. de: tedesco<br/>
2. es: spagnolo<br/>
3. FR: francese<br/>
4. è: italiano<br/>
5. ja: giapponese<br/>
6. ko: coreano<br/>
7. pt-BR: portoghese (Brasile)<br/>
8. RU: russo<br/>
9. zh-HANS: cinese semplificato<br/>
10. zh-HANT: cinese tradizionale<br/>

## <a name="sample-csv-file"></a>File CSV di esempio
Ecco un esempio CSV è possibile modificare.

>[AZURE.NOTE] Copiare e incollare questa nel blocco note e salvarla con estensione 'csv'. Quindi modifica in Excel. Si è strutturato in una tabella con le etichette nella prima riga.

> Aggiungere altri campi facoltativi in Excel specificando l'etichetta e compilare la colonna di sotto di esso.

```
Email,DisplayName,InvitationText,InviteRedirectUrl,InvitedToApplications,InvitedToGroups,CcEmailAddress,Language
wharp@contoso.com,Walter Harp,Hi Walter! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
jsmith@contoso.com,Jeff Smith,Hi Jeff! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en
bsmith@contoso.com,Ben Smith,Hi Ben! I hope you’ve been doing well.,,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,,you@yourcompany.com,en

```

## <a name="related-articles"></a>Articoli correlati
Individuare gli altri articoli sulla collaborazione di Azure Active Directory B2B

- [Che cos'è la collaborazione di Azure Active Directory B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Come funziona](active-directory-b2b-how-it-works.md)
- [Descrizione dettagliata](active-directory-b2b-detailed-walkthrough.md)
- [Formato di token di utenti esterni](active-directory-b2b-references-external-user-token-format.md)
- [Modifiche degli attributi oggetto utente esterno](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitazioni relative all'anteprima corrente](active-directory-b2b-current-preview-limitations.md)
- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
