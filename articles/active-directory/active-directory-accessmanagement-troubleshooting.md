
<properties
    pageTitle="Risoluzione dei problemi dell'appartenenza dinamica per i gruppi | Microsoft Azure"
    description="Risoluzione dei problemi di suggerimenti per l'appartenenza dinamico per i gruppi di Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>


# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Risoluzione dei problemi appartenenze dinamiche per i gruppi

**È configurata una regola in un gruppo, ma nessuna appartenenza viene aggiornata nel gruppo**<br/>Verificare che l'impostazione **Attiva delegato la gestione del gruppo** è impostata su **Sì** nella scheda **Configura** . Questa impostazione verrà visualizzato solo se è stato effettuato l'accesso come utente a cui è assegnata una licenza di Azure Active Directory Premium. Verificare i valori per gli attributi utente alla regola: sono presenti utenti che soddisfano la regola?

**È configurata una regola, ma ora vengono rimossi i membri esistenti della regola**<br/>Si tratta in base al comportamento previsto. Esistenti membri del gruppo vengono rimossi quando una regola viene attivata o modificata. Gli utenti restituiti dalla valutazione della regola vengono aggiunti come membri al gruppo.     

**Non viene visualizzato immediatamente quando si aggiungono o si modifica una regola, perché non cambia l'appartenenza al?**<br/>Valutazione di appartenenza dedicato viene eseguita periodicamente in un processo asincrono in background. Il processo di tempo è il numero di utenti nella directory e le dimensioni del gruppo creato in seguito alla regola. In genere in meno di alcuni minuti directory un numero limitato di utenti verranno visualizzate le modifiche di appartenenza al gruppo. Directory con un numero elevato di utenti potrebbe richiedere 30 minuti o più per popolare.

Gli articoli seguenti forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse dei gruppi di Azure Active Directory](active-directory-manage-groups.md)
* [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
* [Che cos'è Azure Active Directory?](active-directory-whatis.md)
* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
