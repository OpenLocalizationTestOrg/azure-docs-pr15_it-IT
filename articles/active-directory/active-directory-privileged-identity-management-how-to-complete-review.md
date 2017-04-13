<properties
   pageTitle="Come completare una revisione accesso | Microsoft Azure"
   description="Dopo una revisione di accesso è stato avviato in Azure Active Directory privilegi di gestione delle identità, informazioni su come completarla e visualizzare i risultati"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/30/2016"
   ms.author="kgremban"/>

# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Come completare una revisione di access in Azure Active Directory privilegi di gestione delle identità


Gli amministratori di ruolo privilegi è possano esaminare accesso con privilegi una volta una [revisione di sicurezza è stata avviata](active-directory-privileged-identity-management-how-to-start-security-review.md). Gestione delle identità di Azure Active Directory privilegi (PIM) invierà automaticamente un messaggio che richiede agli utenti di controllare l'accesso. Se un utente non è stato visualizzato un messaggio di posta elettronica, è possibile inviare loro le istruzioni su [come eseguire un'analisi della protezione](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Dopo il periodo di revisione di sicurezza o tutti gli utenti termine loro rivedere automatica, seguire i passaggi descritti in questo articolo per gestire la revisione e visualizzare i risultati.

## <a name="manage-security-reviews"></a>Gestire le revisioni di sicurezza

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare l'applicazione di **Azure Active Directory privilegi di gestione delle identità** nel dashboard.
2. Selezionare la sezione **vengono esaminate in Access** del dashboard.
3. Selezionare la revisione di accesso che si vuole gestire.

In blade dettaglio dell'analisi accesso sono disponibili opzioni di numerazione per la gestione di tale revisione.

![Pulsanti di revisione di access PIM - schermata][1]

### <a name="remind"></a>Promemoria

Se una revisione di accesso è configurata in modo che gli utenti verificare se stessi, il pulsante **Invia un promemoria** invia una notifica. 

### <a name="stop"></a>Interrompi

Tutte le revisioni access dispone di una data di fine, ma è possibile utilizzare il pulsante **Termina** per terminare in anticipo. Se non è stato rivisto tutti gli utenti a questo punto, non potrà a dopo si interrompe la recensione. Dopo che è stato interrotto non è possibile riavviare una revisione.

### <a name="apply"></a>Applicare

Al termine di una revisione di access, è perché raggiunto la data di fine o arrestato manualmente, il pulsante **Applica** implementata il risultato della revisione. Se l'accesso un utente negato alla revisione, questo è il passaggio che comporta la rimozione loro assegnazione di ruolo.  

### <a name="export"></a>Esportazione

Se si desidera applicare manualmente i risultati dell'analisi della protezione, è possibile esportare la recensione. Pulsante **Esporta** verrà avviato il download di un file CSV. È possibile gestire i risultati in Excel o altri programmi che aprono file CSV.

### <a name="delete"></a>Elimina

Se non si è interessati alla revisione altre, eliminarlo. Pulsante **Elimina** rimuove la revisione dall'applicazione PIM.

> [AZURE.IMPORTANT] Non si verrà visualizzato un avviso prima che venga eseguita l'eliminazione, pertanto è necessario assicurarsi che si desidera eliminare tale revisione.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
