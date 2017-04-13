<properties
   pageTitle="Protezione della famiglia di prodotti di gestione di operazioni e controllo soluzione previsto | Microsoft Azure"
   description="In questo documento viene illustrato come utilizzare la protezione OMS e controllo soluzione per eseguire una valutazione di riferimento di tutti i computer monitorate a scopo di sicurezza e conformità."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/08/2016"
   ms.author="yurid"/>

# <a name="baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Valutazione di riferimento per le operazioni gestione Suite sicurezza e la soluzione di controllo

In questo documento consente di usare le funzionalità di valutazione di previsione [operazioni gestione famiglia di prodotti (OMS) sicurezza e la soluzione di controllo](operations-management-suite-overview.md) di accedere allo stato sicuro delle risorse monitorate.

## <a name="what-is-baseline-assessment"></a>Che cos'è previsto valutazione?

Microsoft, insieme alle organizzazioni di settore e government tutto il mondo, definisce una configurazione di Windows che rappresenta distribuzioni server altamente protetto. Questa configurazione è un insieme di chiavi del Registro di sistema, impostazioni e impostazioni di protezione insieme valori consigliati Microsoft per queste impostazioni. Questo set di regole è noto come base per la sicurezza. Funzionalità di valutazione previsto OMS sicurezza e di controllo è perfettamente analizzare tutti i computer per la conformità. 

Esistono tre tipi di regole:

- **Le regole del Registro di sistema**: verificare che le chiavi del Registro di sistema siano impostate correttamente.
- **Regole di controllo**: le regole per i criteri di controllo.
- **Le regole dei criteri di sicurezza**: le regole relative alle autorizzazioni degli utenti del computer.

> [AZURE.NOTE] Leggere [Usa OMS protezione per valutare la previsione di configurazione di sicurezza](https://blogs.technet.microsoft.com/msoms/2016/08/12/use-oms-security-to-assess-the-security-configuration-baseline/) per una breve panoramica di questa funzionalità.

## <a name="security-baseline-assessment"></a>Valutazione della previsione di sicurezza

È possibile esaminare la valutazione della previsione di sicurezza corrente per tutti i computer che vengono monitorate dall'OMS sicurezza e controllo tramite il dashboard.  Eseguire la procedura seguente per accedere al dashboard di valutazione di sicurezza previsto:

1. Nel dashboard principale **Famiglia di prodotti Microsoft operazioni di gestione** , fare clic su **sicurezza e controllo** riquadro.
2. Il dashboard di **sicurezza e controllo** fare clic su **Valutazione di riferimento** in **Domini di protezione**. Dashboard di **Valutazione di sicurezza previsto** viene visualizzato come illustrato nell'immagine seguente:
    
    ![Sicurezza OMS e controllo previsione valutazione](./media/oms-security-baseline/oms-security-baseline-fig1.png)

Questo dashboard è diviso in tre aree principali:

- **Computer rispetto alla linea di base**: in questa sezione fornisce un riepilogo del numero di computer in cui ha avuto accesso e la percentuale di computer che passato la valutazione. Fornisce inoltre superiore 10 computer e al risultato percentuale per la valutazione.
- **Stato regole obbligatorio**: in questa sezione è lo scopo per visualizzare la conoscenza delle regole non riuscite di gravità e non è riuscita regole in base al tipo. Controllando al primo grafico è possibile identificare rapidamente se la maggior parte delle regole non riuscite diventano critiche, o meno. Inoltre, viene fornito un elenco di regole non riuscite 10 superiore e la loro gravità. Il secondo grafico mostra il tipo di regola che non è riuscita durante la valutazione. 
- **Computer mancanti valutazione di riferimento**: in questa sezione elencare i computer sono non state è stato effettuato l'accesso a causa di problemi di compatibilità del sistema operativo o errori. 

### <a name="accessing-computers-compared-to-baseline"></a>Accesso a computer confrontate con quelle di previsione

Tutti i computer sono devono essere compatibile con la valutazione della previsione di sicurezza. Non accade tuttavia si prevede che in alcune circostanze questo. Come parte del processo di gestione della sicurezza, è importante includere rivisto il computer in cui non è possibile passare tutti i test di valutazione di sicurezza. Consente di visualizzare rapidamente che è sufficiente selezionare l'opzione **accesso al computer** si trova nella sezione **computer rispetto alla previsione** . Verrà visualizzato il risultato della ricerca log con l'elenco di computer, come illustrato nell'immagine seguente:

![Risultati di accesso al computer](./media/oms-security-baseline/oms-security-baseline-fig2.png)

Il risultato della ricerca è visualizzato in un formato di tabella, in cui la prima colonna ha il nome del computer e il secondo colore ha il numero di regole che non è riuscita. Per recuperare le informazioni sul tipo di regola che non è riuscita, fare clic su numero di quelle non riuscite oltre il nome del computer. Verrà visualizzato un risultato simile a quella mostrata nell'immagine seguente:

![Dettagli risultati computer accessibili](./media/oms-security-baseline/oms-security-baseline-fig3.png)

In questo risultato della ricerca, è necessario il totale delle regole di accesso, il numero di regole critiche che non è riuscita, le regole di avviso e le regole di informazioni non è riuscite.

### <a name="accessing-required-rules-status"></a>Accesso alle regole necessari stato

Dopo aver ottenuto le informazioni relative al numero di percentuale di computer che passato la valutazione, può essere necessario ottenere ulteriori informazioni sulle regole non riescono secondo l'importanza. Questa visualizzazione consente di definire la priorità delle quali computer devono essere risolto prima di tutto per assicurarsi che saranno compatibili con la valutazione successiva. Al passaggio del mouse sulla parte critica del grafico disponibile nella sezione **non è riuscita regole in base alla gravità** e in **stato di regole richiesto** e fare clic su esso. Verrà visualizzato un risultato simile alla schermata seguente:

![Non è riuscita regole per i dettagli di gravità](./media/oms-security-baseline/oms-security-baseline-fig4.png) 

In questo risultato log viene visualizzato il tipo di regola previsto che non è riuscita, la descrizione di questa regola e l'ID comuni configurazione enumerazione (CCE) di questa regola di sicurezza. Questi attributi dovrebbero essere sufficiente per eseguire un'azione di correzione per risolvere il problema nel computer di destinazione.

> [AZURE.NOTE] Per ulteriori informazioni sulle CCE, accedere al [Database vulnerabilità nazionali](https://nvd.nist.gov/cce/index.cfm).

### <a name="accessing-computers-missing-baseline-assessment"></a>Accesso a computer mancanti valutazione di riferimento

OMS supporta il profilo di previsione membro di dominio in Windows Server 2008 R2 fino a Windows Server 2012 R2. Previsione di Windows Server 2016 non è ancora finale e verrà aggiunti come la pubblicazione. Tutti gli altri sistemi operativi digitalizzate tramite OMS sicurezza e controllo valutazione previsto viene visualizzata sotto la sezione **computer mancanti valutazione di riferimento** .

## <a name="see-also"></a>Vedere anche

In questo documento è stato sulla sicurezza OMS e controllo valutazione previsto. Per ulteriori informazioni sulla protezione OMS, vedere gli articoli seguenti:

- [Panoramica di gestione famiglia di prodotti (OMS) operazioni](operations-management-suite-overview.md)
- [Risposta agli avvisi di sicurezza in operazioni gestione famiglia di prodotti sicurezza e la soluzione di controllo e il monitoraggio](oms-security-responding-alerts.md)
- [Monitoraggio delle risorse operazioni Management Suite sicurezza e la soluzione di controllo](oms-security-monitoring-resources.md)

