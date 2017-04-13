<properties
   pageTitle="Servizio di Centro protezione di Azure e Database di SQL Azure | Microsoft Azure"
   description="Questo articolo descrive come Security Center consente di proteggere i database nel Database di SQL Azure."
   services="sql-database"
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
   ms.date="10/18/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-and-azure-sql-database-service"></a>Servizio di Centro protezione di Azure e Database di SQL Azure

[Centro protezione di Azure](https://azure.microsoft.com/documentation/services/security-center/) consente di impedire, rilevare e rispondere a rischi. Che consente di gestire il monitoraggio e criteri di sicurezza integrata tra le proprie sottoscrizioni Azure, consente di rilevare minacce che in caso contrario non verrebbero rilevate e funziona con un insieme di soluzioni di protezione offre un ampio.

Questo articolo descrive come Security Center consente di proteggere i database nel Database di SQL Azure.

## <a name="why-use-security-center"></a>Perché usare Security Center?

Centro protezione consente di proteggere i dati nel Database di SQL fornendo la visibilità sulla sicurezza di tutti i server e database. Centro protezione, è possibile:

- Definire i criteri per la crittografia del Database di SQL e il controllo.
- Controllare la protezione delle risorse di Database SQL attraverso tutte le sottoscrizioni.
- Identificare e risolvere i problemi di sicurezza rapidamente.
- Integrare gli avvisi di [individuazione Database SQL Azure](../sql-database/sql-database-threat-detection-get-started.md).

Oltre a proteggere le risorse di Database SQL, Security Center vengono forniti anche il monitoraggio della protezione e gestione per macchine virtuali di Azure, servizi Cloud, servizi di App, reti virtuali e altro ancora. Altre informazioni sul Centro protezione [di seguito](security-center-intro.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a utilizzare Security Center, è necessario disporre di un abbonamento a Microsoft Azure. Il livello gratuito di Centro protezione è compatibile con l'abbonamento. Per ulteriori informazioni su Free e livelli Standard Security Center, vedere [Prezzi Centro protezione](https://azure.microsoft.com/pricing/details/security-center/).

Nel Centro sicurezza supporta l'accesso basato sui ruoli. Per ulteriori informazioni su controllo dell'accesso basato sui ruoli (RBAC) in Azure, vedere [Controllo dell'accesso basato sui ruoli di Azure Active Directory](../active-directory/role-based-access-control-configure.md). Domande frequenti di Centro protezione fornisce informazioni sulla [modalità di gestione delle autorizzazioni in Centro protezione](security-center-faq.md#how-are-permissions-handled-in-azure-security-center).

## <a name="access-security-center"></a>Centro protezione di Access

Dal [portale di Azure](https://azure.microsoft.com/features/azure-portal/), accedere Security Center. [Accedere al portale](https://portal.azure.com/) e selezionare l' **opzione di Centro protezione**.

![Opzione di Centro protezione][1]

Verrà visualizzata e il **Centro protezione** .
![Blade Security Center][2]

## <a name="set-security-policy"></a>Set di criteri di sicurezza

Criteri di sicurezza definisce il set di controlli consigliato per le risorse all'interno della sottoscrizione specificata o un gruppo di risorse. In Security Center, è possibile definire criteri per le sottoscrizioni o i gruppi di risorse in base alle esigenze di protezione della società e il tipo di applicazioni o riservatezza del contenuto dei dati di ogni abbonamento.

È possibile impostare un criterio per visualizzare suggerimenti per il controllo SQL e crittografia dati trasparente SQL (TDE).

- Quando si attiva **il controllo SQL**e l'individuazione, Security Center consiglia che il controllo dell'accesso al Database di Azure abilitata per la conformità, il rilevamento e per ricerche avanzate.
- Quando si attiva la **crittografia dati trasparente SQL**, Centro protezione si consiglia la crittografia inattivi sia abilitato per il Database di SQL Azure, backup associato e file di registro delle transazioni.

Per impostare un criterio di protezione, selezionare il riquadro di **criteri** in e il Centro protezione. Scegliere l'abbonamento in cui si desidera attivare i criteri di sicurezza e il **criterio di protezione** . Selezionare i **criteri di prevenzione della** e **attivare i suggerimenti di sicurezza che si desidera utilizzare questo abbonamento** .
![Criteri di sicurezza][3]

Per ulteriori informazioni, vedere [impostare criteri di sicurezza](security-center-policies.md).

## <a name="manage-security-recommendation"></a>Gestire i consigli per la protezione

Nel Centro sicurezza analizza periodicamente lo stato di protezione delle risorse di Azure. Quando nel Centro sicurezza identifica potenziali vulnerabilità, crea consigli. I suggerimenti guidano il processo di configurazione controlli necessari.

Dopo aver impostato un criterio di protezione, Security Center analizza lo stato di protezione delle risorse per identificare i potenziali vulnerabilità. I suggerimenti vengono visualizzati in un formato di tabella in cui ogni riga rappresenta un suggerimento particolare. Utilizzare la tabella seguente come riferimento identifichi i suggerimenti disponibili per il Database di SQL Azure e che cosa significa ogni Suggerimento Se si applica. Selezione di un suggerimento consente di accedere a un articolo che spiega come implementare il suggerimento nel Centro protezione.

| Suggerimento | Descrizione |
| ----- | ----- |
| [Attivare il rilevamento di controllo e rischio in SQL Server](security-center-enable-auditing-on-sql-servers.md) | Si consiglia di attivare il rilevamento della verifica e minacce per i server di Database SQL. (Solo per i servizi di Database SQL. Non sono inclusi in esecuzione in macchine virtuali di Microsoft SQL Server.) |
| [Attivare il rilevamento di controllo e rischio nel database SQL](security-center-enable-auditing-on-sql-databases.md) | Si consiglia di attivare il rilevamento della verifica e minacce per i database di Database SQL. (Solo per i servizi di Database SQL. Non sono inclusi in esecuzione in macchine virtuali di Microsoft SQL Server.) |
| [Abilitare la crittografia dati trasparente](security-center-enable-transparent-data-encryption.md) | Si consiglia di abilitare la crittografia per i database SQL. (Solo servizio Database SQL). |

Per visualizzare suggerimenti per le risorse di Azure, selezionare il riquadro **consigli** su e il Centro protezione. Scegliere un suggerimento per visualizzare i dettagli e il **consigli** . In questo esempio, se si seleziona **Attiva controllo & individuazione in SQL Server**.

![Consigli][4]

Come illustrato di seguito, Security Center Mostra SQL Server in cui non sono abilitati in controllo e rischio rilevamento. Dopo aver attivato il controllo, è possibile configurare le impostazioni di individuazione e impostazioni di posta elettronica per ricevere gli avvisi di sicurezza. Individuazione degli avvisi quando rileva le attività di database anomala che indicano i rischi di sicurezza per il database. Gli avvisi vengono visualizzati nel dashboard di Centro protezione.
![Controllo e rischio rilevamento][5]

Seguire i passaggi indicati nella [Guida introduttiva a individuazione Database SQL](../sql-database/sql-database-threat-detection-get-started.md) per attivare e configurare Individuazione e configurare l'elenco dei messaggi di posta elettronica che riceverà gli avvisi di sicurezza quando viene rilevato delle attività anomala.

Per ulteriori informazioni sui consigli, vedere [suggerimenti per la sicurezza di gestione](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Monitorare l'integrità del titolo

Dopo aver attivato [criteri di sicurezza](security-center-policies.md) per le risorse dell'abbonamento, Centro protezione verranno analizzare la protezione delle risorse per identificare i potenziali vulnerabilità.  È possibile visualizzare lo stato di protezione delle risorse nella sezione **integrità di protezione delle risorse** . Quando si fa clic su **dati** nella sezione **integrità di protezione delle risorse** , e il **Risorse dei dati** viene aperto con consigli SQL per problemi, ad esempio il controllo e trasparente crittografia di dati non viene abilitato. Include inoltre consigli per l'integrità generale del database.
![Integrità di protezione delle risorse][6]

Per ulteriori informazioni, vedere [il monitoraggio dell'integrità di sicurezza](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Gestire e risposta agli avvisi di sicurezza

Nel Centro sicurezza automaticamente raccoglie, analizza e si integra dati log di [SQL Azure individuazione](../sql-database/sql-database-threat-detection-get-started.md), nonché altre risorse Azure per rilevare minacce effettive e ridurre i falsi positivi. Un elenco di avvisi di sicurezza priorità verrà visualizzato nell'interfaccia di sicurezza insieme le informazioni che necessarie per analizzare rapidamente il problema e consigli su come eseguire un attacco.

Per visualizzare gli avvisi, selezionare il riquadro **gli avvisi di sicurezza** in e il Centro protezione. Scegliere un avviso per ulteriori informazioni su come gli eventi che lo ha attivato l'avviso e cosa, se presenti, i passaggi da eseguire per risolvere un attacco e il **avvisi di sicurezza** . In questo esempio, se si seleziona **inserimento SQL potenziali**.
![Avvisi di sicurezza][7]

Come illustrato di seguito, Security Center fornisce dettagli aggiuntivi che offrono approfondita cosa ha attivato l'avviso, la risorsa di destinazione, se applicabile l'indirizzo IP di origine e suggerimenti su come risolvere.
![Inserimento di SQL potenziale][8]

Per ulteriori informazioni, vedere [Gestione e risposta agli avvisi di sicurezza](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Passaggi successivi

- [Domande frequenti su Centro protezione](security-center-faq.md) : domande frequenti sull'utilizzo del servizio di ricerca.
- [Guida alla pianificazione e le operazioni di Centro protezione](security-center-planning-and-operations-guide.md) - segui un insieme di attività e i passaggi per ottimizzare l'uso del Centro protezione in base a requisiti di sicurezza e il modello di gestione cloud dell'organizzazione.
- [Protezione dei dati di Centro protezione](security-center-data-security.md) : informazioni su come Security Center raccoglie e consente di elaborare dati sulle risorse Azure, comprese le informazioni di configurazione, metadati, i registri eventi, arresto anomalo e altro ancora.
- [Gestione problemi di sicurezza](security-center-incident.md) - informazioni su come utilizzare la funzionalità degli avvisi di protezione in Centro protezione per facilitare la gestione problemi di protezione.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
