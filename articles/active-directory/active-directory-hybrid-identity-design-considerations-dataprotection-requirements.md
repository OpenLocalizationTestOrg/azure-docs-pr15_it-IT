<properties
    pageTitle="Considerazioni di progettazione identità di distribuzione ibrida di Azure Active Directory - determinare requisiti di protezione dei dati | Microsoft Azure"
    description="Durante la pianificazione della soluzione di identità ibrida, identificare i requisiti di protezione dei dati per l'azienda e quali opzioni sono disponibili per meglio soddisfare questi requisiti."
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

#<a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>Piano per il miglioramento della protezione dei dati tramite soluzione identità sicura

Il primo passaggio per proteggere i dati è identificare chi può accedere ai dati e come parte di questa procedura che è necessario disporre di un'identità che si integra con il sistema per fornire funzionalità di autenticazione e l'autorizzazione. Autenticazione e l'autorizzazione spesso vengono confuse tra loro e i ruoli componenti. In realtà sono diversi, come illustrato nella figura seguente:

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)
 
**Fasi del ciclo di vita gestione di dispositivi mobili**

Quando si pianifica la soluzione di identità ibrida è necessario conoscere i requisiti di protezione dei dati per l'azienda e quali opzioni sono disponibili per meglio soddisfare questi requisiti.
 
>[AZURE.NOTE]
Dopo aver completato la pianificazione per la protezione dei dati, esaminare [l'autenticazione a più fattori requisiti](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) per garantire che le opzioni desiderate relative ai requisiti di autenticazione a più fattori non sono interessate da scelte effettuate in questa sezione.

## <a name="determine-data-protection-requirements"></a>Determinare i requisiti di protezione dei dati
Nell'età della mobilità, la maggior parte delle società hanno un obiettivo comune: consentire agli utenti di essere produttivi anche in dispositivi mobili durante locale o remoto da qualsiasi posizione per migliorare la produttività. Mentre può trattarsi di un obiettivo comune, società che hanno requisito sarà anche preoccupazioni circa l'importo delle minacce che deve essere attenuati per la protezione dei dati della società e gestire la privacy dell'utente. Ogni azienda potrebbe avere requisiti differenti riguardo; le regole di conformità diversi che variano in base alle quali industry opera la società porterà a decisioni diverso. 

Tuttavia, esistono alcuni aspetti di sicurezza che devono essere esaminate e convalidati, indipendentemente dal settore, sono stati spiegati nella sezione successiva.

## <a name="data-protection-paths"></a>Percorsi di protezione dei dati

![](./media/hybrid-id-design-considerations/data-protection-paths.png)
 
**Percorsi di protezione dei dati**

Nella figura precedente, il componente di identità sarà il primo da verificare prima che si accede ai dati. Tuttavia, questi dati possono essere stati diversi durante l'ora che dell'accesso. Ogni numero in questo diagramma rappresenta un percorso in cui dati possono trovarsi in un determinato momento nel tempo. Questi numeri vengono illustrati di seguito:

1. Protezione dei dati a livello dispositivo.
2. Protezione dei dati in transito.
3. Protezione dei dati in locale resto.
4. Protezione dei dati nella parte restante nel cloud.

Sebbene tecnici controlli che i reparti IT possono proteggere i dati su ciascuno di essi di tali fasi non sono direttamente disponibili in soluzione identità ibrida, è necessario che la soluzione di identità ibrida è in grado di sfruttare locali e cloud risorse di gestione delle identità per identificare l'utente prima di concedere l'accesso ai dati. Pianificazione della soluzione di identità ibrido quando assicurarsi che le seguenti domande in base alle esigenze dell'organizzazione:

## <a name="data-protection-at-rest"></a>Protezione dei dati inattivi
Indipendentemente da dove sono i dati inattivi (dispositivi, cloud o locale), è importante eseguire una valutazione per comprendere le esigenze dell'organizzazione a questo proposito. Per questa area assicurarsi che vengono richiesto gli aspetti seguenti:

- La propria azienda è necessario per proteggere i dati resto?
 - In caso affermativo, è possibile integrare con l'infrastruttura locale corrente la soluzione di identità ibrida?
 - In caso affermativo, è la soluzione di identità ibrido grado di integrare con i carichi di lavoro nel cloud?
- La gestione delle identità cloud è in grado di proteggere le credenziali dell'utente e altri dati archiviati nel cloud?

## <a name="data-protection-in-transit"></a>Protezione dei dati in transito
Dati in transito tra il dispositivo e la data center o tra il dispositivo e nel cloud devono essere protetti. Tuttavia, viene in transito non necessariamente un processo di comunicazioni con un componente all'esterno del servizio cloud. entra internamente, inoltre, ad esempio tra due reti virtuale. Per questa area assicurarsi che vengono richiesto gli aspetti seguenti:

- La propria azienda è necessario per proteggere i dati durante il transito?
 - In caso affermativo, è possibile integrare con i controlli sicuri, ad esempio SSL/TLS la soluzione di identità ibrida?
- La gestione delle identità cloud mantiene il traffico da e all'interno dell'archivio di directory (all'interno e tra i Data Center) effettuato l'accesso?


## <a name="compliance"></a>Conformità
Requisiti di conformità alle normative, leggi e norme varia in base industry appartenente all'azienda. Società nel settore regolamentati elevati necessario risolvere i problemi di gestione delle identità correlate ai problemi di conformità. Norme, ad esempio Sarbanes-Oxley (SOX), portabilità assicurazione e responsabilità Act (HIPAA), Gramm-Leach-Bliley Act (GLBA) e il pagamento Card Industry Data Security Standard (PCI DSS) sono molto restrittive relative alle identità e accessi. La soluzione di identità ibrido che la propria azienda adotterà deve avere le funzionalità di base che verranno soddisfare i requisiti di uno o più delle seguenti norme. Per questa area assicurarsi che vengono richiesto gli aspetti seguenti:

- È la soluzione di identità ibrido conforme ai requisiti legali per l'azienda?
- La soluzione di identità ibrido incorpora una funzionalità che consentono la propria azienda essere conforme ai requisiti normativi? 
 
>[AZURE.NOTE]
Assicurarsi di prendere appunti di ogni risposta e si conoscono i concetti alla base la risposta. [Definire strategia di protezione dei dati](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) verranno esaminate le opzioni disponibili e vantaggi/svantaggi delle singole opzioni.  Da con domande tali che verranno selezionati opzione migliore si adatta l'azienda deve.

## <a name="next-steps"></a>Passaggi successivi
 [Determinare i requisiti di gestione dei contenuti](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)


## <a name="see-also"></a>Vedere anche
[Cenni preliminari sulla considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)
