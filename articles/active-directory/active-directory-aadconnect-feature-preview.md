<properties
   pageTitle="Azure AD Connect: Le caratteristiche in anteprima | Microsoft Azure"
   description="In questo argomento descrive in altre caratteristiche di dettaglio che sono in anteprima di Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/27/2016"
   ms.author="billmath"/>

# <a name="more-details-about-features-in-preview"></a>Ulteriori informazioni sulle funzionalità di anteprima
In questo argomento viene descritto come utilizzare le funzionalità attualmente in anteprima.

## <a name="group-writeback"></a>Writeback di gruppo
L'opzione per il writeback di gruppo in termini di caratteristiche facoltativi consente di writeback **Gruppi di Office 365** a un insieme di strutture con Exchange installato. Si tratta di un gruppo di cui sempre acquisito nel cloud. Se si dispone di Exchange locale, quindi è possibile scrivere nuovamente questi gruppi in locale in modo gli utenti con una cassetta postale di Exchange locale di inviare e ricevere messaggi di posta elettronica da questi gruppi.

Sono disponibili ulteriori informazioni sui gruppi di Office 365 e come usarle [qui](http://aka.ms/O365g).

In questo gruppo verrà rappresentato come un gruppo di distribuzione in locale di dominio Active Directory. Il server di Exchange locale deve essere in Exchange 2013 aggiornamento cumulativo 8 (rilasciato in marzo 2015) o Exchange 2016 facilmente riconoscibile il nuovo tipo di gruppo.

**Note durante l'anteprima**

- Attributo address book attualmente non popolata nel riquadro di anteprima. Senza questo attributo, il gruppo non sarà visibile nell'elenco indirizzi globale. Il modo più semplice per popolare l'attributo consiste nell'utilizzare il cmdlet di PowerShell di Exchange `update-recipient`.
- Solo insiemi con lo schema di Exchange sono destinazioni valide per i gruppi. Se è non stato rilevato alcun Exchange, writeback di gruppo non sarà possibile attivare.
- Solo singola foresta organizzazione le distribuzioni di Exchange sono attualmente supportate. Se si dispone di più di Exchange dell'organizzazione locale, sarà necessario una soluzione di GALSync locale per questi gruppi all'interno del altri insiemi di strutture.
- La funzionalità di writeback di gruppo attualmente non gestire gruppi di sicurezza o i gruppi di distribuzione.

>[AZURE.NOTE] Una sottoscrizione a Azure Active Directory Premium è necessaria per il writeback di gruppo.

## <a name="user-writeback"></a>Writeback di utente
> [AZURE.IMPORTANT] La caratteristica Anteprima writeback di utente è stato rimosso nell'aggiornamento di agosto 2015 di Azure AD Connect. Se è abilitata, è necessario disattivare questa caratteristica.

## <a name="next-steps"></a>Passaggi successivi
Procedere con l' [installazione personalizzata di Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).

Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
