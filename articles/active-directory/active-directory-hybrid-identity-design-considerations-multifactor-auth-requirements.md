<properties
    pageTitle="Considerazioni di progettazione identità di distribuzione ibrida di Azure Active Directory - determinare i requisiti di autenticazione a più fattori"
    description="Controllo di accesso condizionato, Azure Active Directory controlla le condizioni specifiche scelto per l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione. Quando sono soddisfatte le condizioni, l'utente viene autenticato e autorizzato ad accedere all'applicazione."
    documentationCenter=""
    services="active-directory"
    authors="femila"
    manager="billmath"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Requisiti di autenticazione a più fattori per la soluzione di identità ibrido

In questo mondo di mobilità con utenti che accedono a dati e le applicazioni nel cloud e da qualsiasi dispositivo, proteggere queste informazioni è diventato fondamentale.  Ogni giorno non c'è un nuovo titolo sulle violazioni della protezione.  Anche se non ci sono garanzie contro tali violazioni, l'autenticazione a più fattori, offre un livello di sicurezza per evitare che tali violazioni aggiuntivo.
Prima di tutto la valutazione dei requisiti di organizzazioni per l'autenticazione a più fattori. Vale a dire che è l'organizzazione cercando di sicura.  Questa valutazione è importante per definire i requisiti tecnici per la configurazione e consentire agli utenti di organizzazioni per l'autenticazione a più fattori.

>[AZURE.NOTE]
Se non ha familiarità con MFA e qual è, si consiglia di leggere l'articolo [che cos'è l'autenticazione a più fattori Azure?](../multi-factor-authentication/multi-factor-authentication.md) prima di continuare a leggere questa sezione.

Assicurarsi di rispondere le operazioni seguenti:

- La propria azienda tentando di proteggere le applicazioni di Microsoft? 
- Modalità di pubblicazione queste applicazioni?
- La propria azienda offre accesso remoto per consentire ai dipendenti di accedere App locale?

In caso affermativo, il tipo di accesso remoto? È anche necessario valutare nel punto in cui gli utenti che accedono a queste applicazioni sarà trovano. Questa valutazione è un altro passaggio importante per definire la strategia di autenticazione a più fattori appropriate. Assicurarsi che rispondere alle domande seguenti:

- Nel punto in cui gli utenti devono essere trova?
- È possibile si trova in un punto qualsiasi?
- La società vuole stabilire restrizioni in base alla posizione dell'utente?

Dopo aver stabilito questi requisiti, è importante valutare anche i requisiti dell'utente per l'autenticazione a più fattori. Questa valutazione è importante perché è di definire i requisiti per l'implementazione di autenticazione a più fattori. Assicurarsi che rispondere alle domande seguenti:

- Gli utenti sono familiarità con l'autenticazione a più fattori?
- Alcuni usi saranno necessari per l'autenticazione aggiuntiva?  
 - In caso affermativo, il tempo, quando proveniente da reti esterne o accesso alle applicazioni specifiche o altre condizioni?
- Gli utenti richiederà formazione su come installare e implementare l'autenticazione a più fattori?
- Quali sono gli scenari principali la società desidera abilitare l'autenticazione a più fattori per gli utenti?

Dopo la risposta alle domande precedente, sarà possibile capire se sono già implementata l'autenticazione a più fattori locale. Questa valutazione è importante per definire i requisiti tecnici per la configurazione e consentire agli utenti di organizzazioni per l'autenticazione a più fattori. Assicurarsi che rispondere alle domande seguenti:

- La propria azienda necessario proteggere account con privilegi con MFA?
- La propria azienda necessario abilitare MFA per alcune applicazione per motivi di conformità?
- La propria azienda necessario abilitare MFA per tutti gli utenti appropriati di queste applicazioni o solo gli amministratori?
- È necessario installato MFA sempre attivata o solo quando gli utenti connessi all'esterno della rete aziendale?


## <a name="next-steps"></a>Passaggi successivi
[Definire una strategia di adozione di identità ibrido](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)


## <a name="see-also"></a>Vedere anche
[Cenni preliminari sulla considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)
