
<properties
    pageTitle="Considerazioni di progettazione identità di distribuzione ibrida di Azure Active Directory - rResponse incidente requisiti | Requisiti di Microsoft Azure "
    description="Funzionalità di monitoraggio e creazione di report per la soluzione di identità ibrido che possono essere utilizzati per determinare IT per eseguire azioni per identificare e ridurre i rischi di potenziali"
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

# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Requisiti di risposta per la soluzione di identità ibrido

Organizzazioni di grandi dimensioni o medie è probabile che saranno necessario un [intervento di protezione](https://technet.microsoft.com/library/cc700825.aspx) per facilitare la IT agire di conseguenza a livello di evento imprevisto. Il sistema di gestione delle identità è un componente importante del processo di risposta perché può essere utilizzato per identificare chi ha eseguito un'azione specifica rispetto al target. Soluzione di identità ibrida deve avere la possibilità fornire funzionalità di monitoraggio e creazione di report che possono essere utilizzate per IT per eseguire azioni per identificare e ridurre un potenziale rischio. In un piano di risposta tipico saranno disponibili le seguenti fasi come parte del piano:

1.  Valutazione iniziale.
2.  Operazioni non consentite comunicazioni.
3.  Il controllo dei danni e riduzione dei rischi.
4.  Identificazione delle operazioni che è stato compromesso e la gravità.
5.  Conservazione delle prove.
6.  Notifica nelle parti appropriate.
7.  Ripristino di sistema.
8.  Documentazione.
9.  Valutazione danni e il costo.
10. Processo e pianificare la revisione.

Durante l'identificazione di quello a cui è stato compromesso e gravità fase, sarà necessario identificare i sistemi che sono stati compromessi, i file che sono stati eseguiti e determinano la riservatezza del contenuto di tali file. Il sistema di identità ibrido dovrebbe essere possibile soddisfi questi requisiti che faciliteranno che identifica l'utente che sono stati resi tali modifiche. 

## <a name="monitoring-and-reporting"></a>Monitoraggio e report
Il sistema di identità di tutte le volte consente inoltre in fase di valutazione iniziale soprattutto se il sistema è compilato il controllo e funzionalità. Durante la valutazione iniziale, amministratore IT deve essere in grado identificare un'attività sospetta oppure il sistema dovrebbe essere possibile attivare che automaticamente in base a un'attività preconfigurata. Molte attività potrebbero far attacco, ma in altri casi, un sistema configurato in modo errato può generare a un numero di falsi in un sistema di rilevamento di intrusione. 

Il sistema di gestione delle identità deve aiutare gli amministratori IT per identificare e generare report di tali attività sospetto. In genere tali requisiti tecnici possono essere soddisfatti tutti i sistemi di monitoraggio e lasciando una funzionalità di creazione di report che è possibile evidenziare rischi. Utilizzare le domande seguenti che consentono di progettare una soluzione di identità ibrido tenendo in requisiti intervento considerazione:

- La società ha un intervento di protezione in posizione?
 - In caso affermativo, è viene utilizzato il sistema di gestione delle identità corrente come parte del processo di?
- La propria azienda necessario identificare sospetti tentativi accesso degli utenti tra diversi dispositivi?
- La propria azienda necessario rileva potenziali compromesso le credenziali dell'utente?
- La propria azienda necessario controllare l'accesso e azione dell'utente?
- La propria azienda utili quando si reimposta la password?

## <a name="policy-enforcement"></a>Applicazione dei criteri

Durante il controllo dei danni e fase di riduzione dei rischi, è importante rapidamente ridurre gli effetti effettivi e potenziali di attacco. L'azione che verrà eseguita a questo punto è possibile apportare la differenza tra uno principale e una secondaria. Risposta esatta dipende dall'organizzazione e il tipo di attacco riscontrati. Se la valutazione iniziale concluso che un account è stata compromessa, è necessario applicare i criteri per bloccare questo account. Questo è solo un esempio in cui verrà utilizzato il sistema di gestione delle identità. Utilizzare le domande seguenti che consentono di progettare una soluzione di identità ibrido tenendo in considerazione come verranno applicati criteri per rispondere a un problema in corso:

- La propria azienda dispone criteri in posizione per impedire agli utenti dall'accesso alla rete se necessario?
 - In caso affermativo, la soluzione corrente integra con il sistema di gestione delle identità ibrido che si intende adottare?
- La propria azienda necessario attivare l'accesso condizionale per gli utenti che sono in quarantena? 
 
>[AZURE.NOTE]
Assicurarsi di prendere appunti di ogni risposta e si conoscono i concetti alla base la risposta. [Strategia di protezione dei dati Definisci](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) verranno esaminate le opzioni disponibili e vantaggi/svantaggi delle singole opzioni.  Da con domande tali che verranno selezionati opzione migliore si adatta l'azienda deve.

## <a name="next-steps"></a>Passaggi successivi
[Definire una strategia di protezione dei dati](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Vedere anche
[Cenni preliminari sulla considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)
