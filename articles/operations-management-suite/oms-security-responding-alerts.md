<properties
   pageTitle="Risposta agli avvisi di sicurezza in protezione della famiglia di prodotti di gestione di operazioni e soluzione di controllo e il monitoraggio | Microsoft Azure"
   description="In questo documento consente di usare l'opzione di business intelligence rischio disponibile in protezione OMS e al controllo monitoraggio e la risposta agli avvisi di sicurezza."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Risposta agli avvisi di sicurezza in protezione della famiglia di prodotti di gestione di operazioni e soluzione di controllo e il monitoraggio

In questo documento consente di utilizzare il rischio intelligence è disponibile un'opzione in sicurezza OMS e controllo monitoraggio e la risposta agli avvisi di sicurezza.

## <a name="what-is-oms"></a>Che cos'è OMS?

Microsoft operazioni di gestione famiglia di prodotti (OMS) è soluzione di gestione IT che consente di gestire e proteggere le in locale e infrastruttura cloud basato su cloud di Microsoft. Per ulteriori informazioni sulle OMS, leggere l'articolo [Operazioni Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="threat-intelligence"></a>Business intelligence di rischio

In un ambiente aziendale in cui gli utenti hanno un ampio accesso alla rete e usare una varietà di dispositivi per connettersi ai dati aziendali, è essenziale che è possibile monitorare attivamente le risorse e rispondere rapidamente ai problemi di protezione. Questo è particolarmente importante dal punto di vista di ciclo di vita di protezione in quanto alcune alla sicurezza informatica minacce potrebbero non generare avvisi o sospette attività identificato dal controlli tecnici protezione tradizionale. 

Utilizzando l'opzione di **Rischio Intelligence** disponibile in protezione OMS e al controllo, gli amministratori IT possono identificare i rischi di sicurezza dell'ambiente, ad esempio, identificare se un determinato computer fa parte di un [indesiderati](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection). Computer possono diventare nodi in un indesiderati quando gli utenti malintenzionati installare illecitamente malware genere il computer viene connesso al comando e al comando. Inoltre possibile identificare i rischi provenienti da canali di comunicazione in, ad esempio [darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents). 

Per creare l'intelligente rischio, sicurezza OMS e controllo utilizzare dati provenienti da più origini all'interno di Microsoft. Protezione OMS e al controllo verranno utilizzati questi dati per individuare eventuali rischi per l'ambiente.

Il riquadro di Business Intelligence di rischio è costituito da tre opzioni principali:
- Server con il traffico dannoso in uscita
- Tipi di minacce rilevati
- Mappa di business intelligence di rischio

> [AZURE.NOTE] per una panoramica di tutte queste opzioni, leggere [Guida introduttiva a operazioni gestione gruppo di sicurezza e soluzione di controllo](oms-security-getting-started.md).

### <a name="responding-to-security-alerts"></a>Risposta agli avvisi di sicurezza

Uno dei passaggi del processo di [intervento di protezione](https://technet.microsoft.com/library/cc512623.aspx) è identificare la gravità del sistema compromesso. In questa fase eseguire le operazioni seguenti:

- Determinare la natura dell'attacco
- Determinare il punto di attacco di origine
- Determinare lo scopo dell'attacco. È stato casuale o è stato attacco specificamente mirato all'organizzazione per acquisire informazioni specifiche?
- Identificare i sistemi che sono stati danneggiati
- Identificare i file che sono stati eseguiti e determinano la riservatezza dei file

È possibile sfruttare le informazioni di **Business Intelligence di rischio** in sicurezza OMS e soluzione di controllo per facilitare queste attività. Seguire la procedura seguente per accedere alle opzioni di **Business Intelligence di rischio** :

1. Nella **Famiglia di prodotti Microsoft operazioni di gestione dei** dashboard principale fare clic su **sicurezza e controllo** riquadro.

    ![Sicurezza e controllo](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. Il dashboard di **sicurezza e controllo** vengono visualizzate le opzioni **Intelligence rischio** nell'angolo destro, come illustrato di seguito:

    ![Rischio intel](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

Questi tre riquadri per ottenere una panoramica delle minacce corrente. In **Server con il traffico dannoso in uscita** che sarà possibile identificare se esiste un qualsiasi computer in cui si sta controllando (all'interno o all'esterno di rete) che invia il traffico dannoso a Internet. 

Il riquadro di **tipi di minacce rilevate** contiene un riepilogo dei rischi che siano aggiornati "in azione", se si fa clic sul riquadro si vedrà ulteriori informazioni su queste minacce come mostrato di seguito:

![Tipi di minacce rilevati](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

È possibile estrarre ulteriori informazioni su ogni rischio facendo clic su di esso. Nell'esempio seguente viene maggiori dettagli sull'indesiderati:

![ulteriori informazioni su un rischio](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

Come descritto all'inizio di questa sezione, queste informazioni possono essere molto utile durante un caso di risposta. Può essere anche importante durante indagini legali, in cui è necessario trovare l'origine di attacco, il sistema è stata compromessa e la sequenza temporale. In questo report è possibile identificare facilmente alcuni dettagli chiave sull'attacco, ad esempio: l'origine di attacco, IP locale che è stata compromessa e lo stato della sessione corrente della connessione. 

**Mappa di business intelligence rischio** consentono per indicare la posizione corrente in tutto il mondo che hanno il traffico dannoso. Esistono arancione (posta in arrivo) e (in uscita) rosse in questa mappa che identificano la direzione del traffico, se si fa clic in una di queste frecce, verrà visualizzato il tipo di rischio e la direzione del traffico, come illustrato di seguito:

![mappa di rischio intel](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [AZURE.NOTE] È possibile visualizzare una dimostrazione sull'uso di questa funzionalità durante un incidente processo di risposta controllando la presentazione [attenuare i rischi di sicurezza Data Center con indagini interattiva utilizzando operazioni Management Suite](https://myignite.microsoft.com/videos/5000) recapitata Ignite Microsoft.

## <a name="see-also"></a>Vedere anche

In questo documento è stato illustrato come utilizzare l'opzione di **Business Intelligence di rischio** in sicurezza OMS e soluzione di controllo di risposta agli avvisi di sicurezza. Per ulteriori informazioni sulla protezione OMS, vedere gli articoli seguenti:

- [Panoramica di gestione famiglia di prodotti (OMS) operazioni](operations-management-suite-overview.md)
- [Introduzione alle operazioni di gestione famiglia di prodotti sicurezza e soluzione di controllo](oms-security-getting-started.md)
- [Monitoraggio delle risorse operazioni gestione famiglia di prodotti sicurezza e la soluzione di controllo](oms-security-monitoring-resources.md)
