<properties
   pageTitle="Gestione di unità amministrative di Azure Active Directory"
   description="Utilizzo di unità amministrative per la delega più granulare di autorizzazioni di Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Gestione di unità amministrative in Azure Active Directory - Public Preview

In questo articolo vengono illustrati unità amministrative – un nuovo contenitore di Azure Active Directory delle risorse che possono essere usate per la delega di autorizzazioni amministrative su sottoinsiemi di utenti e applicazione di criteri a un sottoinsieme degli utenti. Azure Active Directory, unità amministrative consentono agli amministratori centrali per delegare le autorizzazioni per gli amministratori delle aree o per impostare i criteri un livello di dettaglio.

Questo è utile nelle organizzazioni con reparti indipendenti, ad esempio, un grande university è costituito da molte scuole autonome (dell'istituto di istruzione di Business, dell'istituto di istruzione di progettazione e così via) indipendenti tra loro. Ad esempio divisioni avere propri amministratori IT che è possibile controllare l'accesso, gestire gli utenti e impostare i criteri specificamente per la divisione. Amministratori centrale vogliono poter concedere questi reparto autorizzazioni agli amministratori sopra gli utenti delle proprie divisioni. In particolare, utilizzando questo esempio, un amministratore centrale può, ad esempio, creare un'unità amministrativa per un determinato istituto (scuola Business) e inserirvi solo gli utenti aziendali dell'istituto di istruzione. Solo tramite l'unità amministrativa dell'istituto di istruzione di Business in altre parole, un amministratore centrale può aggiungere il personale IT dell'istituto di istruzione Business a un ruolo dell'ambito concedere il personale IT di autorizzazioni amministrative dell'istituto di istruzione Business.

> [AZURE.IMPORTANT]
> È possibile creare e utilizzare unità amministrative solo se si abilita Azure Active Directory Premium. Per ulteriori informazioni, vedere [Guida introduttiva di Azure Active Directory Premium](active-directory-get-started-premium.md).

Dal punto di vista dell'amministratore centrale, un'unità amministrativa è un oggetto directory che può essere creato e popolato con le risorse. **In questa versione, le risorse possono essere solo gli utenti.** Una volta creato e popolato, l'unità amministrativa utilizzabile come ambito per limitare le autorizzazioni concesse solo sulle risorse contenute nell'unità di amministrazione.

## <a name="managing-administrative-units"></a>Gestione delle unità amministrative

In questa versione di anteprima, è possibile creare e gestire le unità amministrative utilizzando i cmdlet modulo Azure Active Directory per Windows PowerShell.

Per ulteriori informazioni sui requisiti software e installare il modulo di Azure Active Directory e per informazioni sui cmdlet di modulo di Azure AD per la gestione di unità amministrative, tra cui sintassi, le descrizioni del parametro ed esempi, vedere [gestire Azure Active Directory mediante Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).


## <a name="next-steps"></a>Passaggi successivi
[Edizioni di Azure Active Directory](active-directory-editions.md)
