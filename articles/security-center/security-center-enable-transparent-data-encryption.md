<properties
   pageTitle="Abilitare la crittografia dati trasparente in Centro protezione di Azure | Microsoft Azure"
   description="In questo documento viene illustrato come implementare il suggerimento di Centro protezione di Azure **Abilitare la crittografia dei dati trasparente**."
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

# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Abilitare la crittografia dati trasparente in Centro protezione di Azure

Centro protezione di Azure indicazioni abilitare la crittografia dati trasparente (TDE) in database SQL se TDE non è già stato attivato. TDE protegge i dati e consente di soddisfare i requisiti di conformità crittografando database, backup associato, file di registro delle transazioni inattivi, senza apportare modifiche all'applicazione. Per ulteriori informazioni, vedere [Trasparente la crittografia dei dati con il Database di SQL Azure](https://msdn.microsoft.com/library/dn948096).

Si applica questo suggerimento per il servizio di SQL Azure. non include SQL in esecuzione in macchine virtuali.

> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio.  Questa non è una Guida dettagliata.

## <a name="implement-the-recommendation"></a>Implementare la recommendation

1. Nella finestra e **raccomandazioni** , selezionare **Abilita la crittografia dei dati trasparente**.
![Abilitare la crittografia dati trasparente][1]

2. Verrà aperta e **l'Abilitare trasparente la crittografia dei dati nel database SQL** . Selezionare un database SQL di attivare TDE.
![Selezionare DB SQL per abilitare TDE in][2]
3. Nella e **crittografia dati trasparente** , selezionare **via** sotto la crittografia dei dati e selezionare **Salva** nella barra multifunzione superiore della stessa e.
![Attivare TDE][3]

  Una volta TDE è stato attivato il database SQL selezionato, lo **stato di crittografia** cambierà a **crittografia**.    

  ![Stato di crittografia][4]

## <a name="see-also"></a>Vedere anche

In questo articolo viene illustrato come implementare recommendation Security Center "Abilitare la crittografia dei dati trasparente". Per ulteriori informazioni su SQL TDE, vedere gli articoli seguenti:

- [Crittografia dati trasparente con Database SQL Azure](https://msdn.microsoft.com/library/dn948096)
- [Iniziare con la crittografia dati trasparente (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md) - informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Il monitoraggio dell'integrità di protezione in Centro protezione di Azure](security-center-monitoring.md) informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Monitoraggio di soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) - informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
- [Blog di sicurezza di Windows azure](http://blogs.msdn.com/b/azuresecurity/) - ottenere le ultime notizie di sicurezza di Azure e informazioni.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
