<properties
    pageTitle="Considerazioni di progettazione identità di distribuzione ibrida di Azure Active Directory - determinare i requisiti di sincronizzazione della directory | Microsoft Azure"
    description="Identificare i requisiti necessari per la sincronizzazione di tutti gli utenti tra on = locali e cloud per le aziende."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-directory-synchronization-requirements"></a>Requisiti di sincronizzazione della directory
La sincronizzazione è basato sui fornire agli utenti un'identità nel cloud in base alla propria identità locale. Account sincronizzati utilizzeranno per federati o l'autenticazione, o meno gli utenti comunque necessario avere un'identità nel cloud.  Questa identità sarà necessario essere gestita e aggiornata periodicamente.  Gli aggiornamenti sono disponibili molti tipi, dalle modifiche titolo per la modifica della password.  

Avviare valutando le organizzazioni locale identità soluzione requisiti e degli utenti. Questa valutazione è importante per definire i requisiti tecnici per la modalità di creazione e gestione nel cloud identità utente.  Per la maggior parte delle organizzazioni, verrà sincronizzata da, tuttavia in alcuni casi non sarà le maiuscole/minuscole nella directory locale che gli utenti da Active Directory è locale.  

Assicurarsi che rispondere alle domande seguenti:


- Si dispone di un insieme di strutture di Active Directory, multiplo o nessuno?
 - Si quante directory di Azure Active Directory verrà sincronizzando a?
 
    1. Si sta utilizzando il filtro?
    2. Si dispone di più server Azure AD Connect pianificato?
  
- Si dispone di una sincronizzazione strumento locale?
  - In caso affermativo, non agli utenti se gli utenti hanno una virtuale/integrazione della directory di identità?
- Si dispone di qualsiasi altra directory locale che si desidera sincronizzare (ad esempio Directory LDAP, database delle risorse Umane e così via)?
  - Si sta per eseguire le eventuali GALSync?
  - Qual è lo stato corrente di UPN all'interno dell'organizzazione? 
  - Si dispone di una directory diversa per autenticare gli utenti?
  - La propria azienda utilizza Microsoft Exchange?
    - Si prevede di disporre di una distribuzione ibrida di exchange?

Dopo aver creato un'idea sui requisiti di sincronizzazione, è necessario determinare quali strumenti sono quello corretto per soddisfare tali requisiti.  Microsoft offre diversi strumenti per eseguire la sincronizzazione e integrazione della directory.  Vedere la [tabella di confronto di identità ibrido directory integrazione strumenti](active-directory-hybrid-identity-design-considerations-tools-comparison.md) per altre informazioni. 
   
Dopo aver creato ai propri requisiti di sincronizzazione e lo strumento di eseguire questa operazione per la propria azienda, è necessario valutare le applicazioni che utilizzano tali servizi directory. Questa valutazione è importante per definire i requisiti tecnici di integrare tali applicazioni nel cloud. Assicurarsi che rispondere alle domande seguenti:

- Verranno spostate queste applicazioni nel cloud e utilizzare la directory?
- Esistono attributi speciali che è necessario sincronizzare nel cloud in modo che queste applicazioni usarli correttamente?
- Queste applicazioni saranno necessario scrivere nuovamente in modo da sfruttare auth cloud?
- Queste applicazioni continueranno a live locale mentre utenti accedervi utilizzando l'identità cloud?

È anche necessario determinare la sincronizzazione delle directory vincoli e requisiti di sicurezza. Questa valutazione è importante per ottenere un elenco dei requisiti necessari per creare e gestire le identità dell'utente nel cloud. Assicurarsi che rispondere alle domande seguenti:

- Nel punto in cui sarà si trova il server di sincronizzazione?
- Si prevede dominio?
- Il server si troverà in una rete con restrizioni un firewall, ad esempio una rete Perimetrale?
  - Sarà possibile aprire le porte del firewall necessari per supportare la sincronizzazione?
- Si dispone di un piano di recupero per il server di sincronizzazione?
- Si dispone di un account con autorizzazioni corrette per tutti gli insiemi di strutture desiderato per la sincronizzazione con?
 - Se la propria azienda non sa la risposta alla domanda, consultare la sezione "Autorizzazioni per la sincronizzazione delle password" nell'articolo [installare il servizio di sincronizzazione Azure Active Directory](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) e determinare se si dispone già di un account con le autorizzazioni o se è necessario crearne uno.
- Se si dispone di sincronizzazione mutli insieme di strutture in grado di accedere a ogni foresta il server di sincronizzazione?
 
>[AZURE.NOTE]
Assicurarsi di prendere appunti di ogni risposta e si conoscono i concetti alla base la risposta. [Requisiti di risposta determinazione](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) verranno esaminate le opzioni disponibili. Da con domande tali che verranno selezionati opzione migliore si adatta l'azienda deve.

## <a name="next-steps"></a>Passaggi successivi
[Determinare i requisiti di autenticazione a più fattori](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Vedere anche
[Cenni preliminari sulla considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)
