<properties
   pageTitle="Abilitare il controllo su SQL Server in Centro protezione di Azure | Microsoft Azure"
   description="In questo documento viene illustrato come implementare recommendation Azure Security Center **abilitare il controllo su SQL Server**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="enable-auditing-on-sql-servers-in-azure-security-center"></a>Abilitare il controllo su SQL Server in Centro protezione di Azure

Centro protezione di Azure indicazioni che l'attivazione del controllo per tutti i database sui server SQL Azure se il controllo non è già attivato. Il controllo consente di mantenere la conformità alle normative, comprendere l'attività di database e approfondire differenze e alterazioni possono indicare problematiche aziendali o sospetti violazioni della protezione.

Dopo aver attivato il controllo è possibile configurare le impostazioni di individuazione e messaggi di posta elettronica per ricevere gli avvisi di sicurezza. Individuazione rileva le attività di database anomala che indica rischi di sicurezza per il database. In questo modo è possibile rilevare e rispondere ai rischi quando si verificano.

Si applica questo suggerimento per il servizio di SQL Azure. non include SQL Server in macchine virtuali in servizi di infrastruttura di Windows Azure (IaaS Azure).

> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio.  Questa non è una Guida dettagliata.

## <a name="implement-the-recommendation"></a>Implementare la recommendation

1. Nella finestra e **raccomandazioni** , selezionare **Attiva controllo in SQL Server**.  Verrà aperta e **l'Abilitare il controllo su SQL Server** .
![Abilitare il controllo su SQL Server][1]

2. Selezionare un server SQL per abilitare il controllo su. Verrà aperta e **l'Impostazioni di controllo** .
![Impostazioni di controllo][2]
3. Scegliere **Sì** in **controllo**e **l'Impostazioni di controllo** .
![Attivare le impostazioni di controllo][3]

4. Seguire i passaggi indicati nella [Guida introduttiva con il controllo dei database SQL](../sql-database/sql-database-auditing-get-started.md) per configurare l'archiviazione in cui verrà archiviato il log di controllo. Account di archiviazione dell'abbonamento per la raccolta di dati è l'account di archiviazione predefinito.

5. Seguire i passaggi indicati nella [Guida introduttiva a individuazione Database SQL](../sql-database/sql-database-threat-detection-get-started.md) per attivare e configurare Individuazione e configurare l'elenco dei messaggi di posta elettronica che riceverà gli avvisi di sicurezza quando viene rilevato delle attività anomala.

## <a name="see-also"></a>Vedere anche

In questo articolo viene illustrato come implementare recommendation Security Center "Abilitare il controllo su SQL Server". Per ulteriori informazioni sulla protezione del database SQL, vedere gli articoli seguenti:

- [La protezione del Database SQL](../sql-database/sql-database-security.md)

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md) - informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Il monitoraggio dell'integrità di protezione in Centro protezione di Azure](security-center-monitoring.md) informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Monitoraggio di soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) - informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
- [Blog di sicurezza di Windows azure](http://blogs.msdn.com/b/azuresecurity/) - ottenere le ultime notizie di sicurezza di Azure e informazioni.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]:./media/security-center-enable-auditing-on-sql-server/enable-auditing.png
[3]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
