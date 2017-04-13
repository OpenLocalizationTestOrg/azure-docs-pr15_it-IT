<properties
    pageTitle="Identità di ibrido Azure Active Directory considerazioni sulla progettazione - determinare attività di gestione delle identità ibrido | Microsoft Azure"
    description="Controllo di accesso condizionato, Azure Active Directory controlla le condizioni specifiche scelto per l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione. Quando sono soddisfatte le condizioni, l'utente viene autenticato e autorizzato ad accedere all'applicazione."
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

# <a name="plan-for-hybrid-identity-lifecycle"></a>Piano per ciclo di vita identità ibrido 

Identità è una delle basi di enterprise mobilità e applicazione strategia di accesso. Se è stato iscrizione al dispositivo mobile o SaaS app, l'identità è fondamentale per l'accesso a tutti gli elementi. Al livello più alto, una soluzione di gestione delle identità comprende unire provenienti e la sincronizzazione tra gli archivi identità che include automazione e centralizzare il processo di provisioning risorse. Soluzione identità deve essere un'identità centralizzata in locale e cloud e anche usare una forma di federazione delle identità per mantenere autenticazione centralizzato sicuro condividere e collaborare con utenti esterni e le aziende. Risorse compreso tra sistemi operativi e applicazioni ai partecipanti o associata a un'organizzazione. Struttura dell'organizzazione può essere modificato in base ai criteri e procedure di provisioning.

È anche importante avere una soluzione di identità utili per consentire agli utenti, offrendo esperienze self-service per mantenerle produttivi. La soluzione di identità è più efficace se Abilita single sign-on per gli utenti di tutte le risorse che hanno bisogno di accedere a tutti gli amministratori livelli possono utilizzare procedure standard per la gestione delle credenziali utente. Alcuni livelli di amministrazione possono essere ridotta o eliminati, a seconda l'ampiezza della soluzione di gestione di provisioning. Inoltre, è possibile in modo sicuro distribuire le funzionalità di amministrazione, manualmente o automaticamente, tra le varie organizzazioni. Un amministratore di dominio, ad esempio, può essere usato solo gli utenti e le risorse in tale dominio. L'utente può eseguire attività amministrative e provisioning, ma non è autorizzato a eseguire attività di configurazione, ad esempio la creazione di flussi di lavoro.


## <a name="determine-hybrid-identity-management-tasks"></a>Determinare l'attività di gestione delle identità ibrido
La distribuzione di attività amministrative all'interno dell'organizzazione consente di migliorare l'accuratezza e l'efficienza dell'amministrazione e migliorare il bilanciamento del carico di lavoro di un'organizzazione. Di seguito sono pivot che definiscono un sistema di gestione delle identità affidabili.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)


Per definire attività di gestione delle identità ibrida, è necessario conoscere alcune caratteristiche essenziali dell'organizzazione che adozione di identità ibrida. È importante conoscere archivi corrente utilizzati per le origini di identità. Se si conosce gli elementi di base, saranno disponibili i requisiti di base e in base che sarà necessario porre domande più granulare che consentirà una migliore decisione di progettazione per la soluzione di identità.  

Durante la definizione di tali requisiti, assicurarsi che almeno le seguenti domande

- Opzioni di provisioning: 
 - Soluzione di identità ibrida supporta un sistema di provisioning e di gestione degli accessi account affidabile?
 - Quali sono gli utenti, gruppi e le password in modo che sia gestito?
 - È la gestione del ciclo di vita identità risponde? 
      - Come password aggiornamenti account sospensione tempo?
      
- Gestione delle licenze: 
 - È la gestione delle licenze ibrido identità soluzione quadratini?
     - In caso affermativo, quali funzionalità sono disponibili?
- La soluzione gestione licenze basato su gruppo? 
      -Se Sì, è possibile assegnare un gruppo di sicurezza? 
       -Se Sì, verrà directory cloud automaticamente assegnare licenze a tutti i membri del gruppo? 
        -Che cosa succede se un utente viene successivamente aggiunto a o rimossi dal gruppo, verrà una licenza automaticamente assegnata o rimossi in base alle esigenze? 

- Integrazione con altri provider di identità di terze parti:
- Indica se questa soluzione ibrida può essere integrata con provider di identità di terze parti per implementare il single sign-on
- È possibile unificare tutti i provider di identità diversa in un sistema di identità coerente?
- In caso affermativo, come e quali sono hanno e quali funzionalità sono disponibili?

## <a name="synchronization-management"></a>Gestione della sincronizzazione
Uno degli obiettivi di un manager di identità, in grado di visualizzare tutti i provider di identità e mantenerle sincronizzate. Mantenere i dati sincronizzati in base a un provider di identità master rilevanti. In uno scenario di identità ibrido, con un modello di gestione sincronizzata, gestire tutte le identità utente e dispositivo di un server in locale e sincronizzare l'account e, facoltativamente, la password nel cloud. L'utente immette lo stessa password locale come ha o si occupa nel cloud e per l'accesso, la password viene verificata dalla soluzione identità. Questo modello utilizza uno strumento di sincronizzazione della directory.
 
![](./media/hybrid-id-design-considerations/Directory_synchronization.png)Per progettare in modo corretto la sincronizzazione della soluzione di identità ibrido garantire che le seguenti domande: • quali sono le soluzioni di sincronizzazione disponibili per la soluzione di identità ibrida?
• Quali sono le single sign-on funzionalità disponibili?
• Quali sono le opzioni per la federazione delle identità tra B2B e B2C?

## <a name="next-steps"></a>Passaggi successivi
[Determinare la strategia di adozione gestione delle identità ibrido](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)


## <a name="see-also"></a>Vedere anche
[Cenni preliminari sulla considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)

