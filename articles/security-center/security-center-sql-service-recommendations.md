<properties
   pageTitle="Proteggere il servizio di SQL Azure in Centro protezione di Azure | Microsoft Azure"
   description="Gli indirizzi di documento consigli in Centro protezione di Azure che consentono di proteggere il servizio SQL Azure e rimangono in conformità con criteri di sicurezza."
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
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# <a name="protecting-azure-sql-service-in-azure-security-center"></a>Proteggere il servizio di SQL Azure in Centro protezione di Azure

Centro protezione di Azure analizza lo stato di protezione delle risorse di Azure. Quando nel Centro sicurezza identifica potenziali vulnerabilità, crea consigli che guidano il processo di configurazione controlli necessari.  Procedure consigliate si applicano ai tipi di risorse Azure: macchine (), rete, SQL e le applicazioni.

Questo articolo riguarda consigli che si applicano al servizio di SQL Azure.  Centro consigli del servizio SQL Azure intorno abilitare il controllo di Azure SQL Server e database e per consentire la crittografia per i database SQL.  Utilizzare la tabella seguente come riferimento identifichi i suggerimenti di servizio SQL disponibili e ciascuno di essi verrà cosa se si applica.

## <a name="available-sql-service-recommendations"></a>Consigli su servizi SQL disponibili

|Suggerimento|Descrizione|
|-----|-----|
|[Abilitare il controllo di SQL server](security-center-enable-auditing-on-sql-servers.md)|Si consiglia l'attivazione del controllo per i server di SQL Azure (servizio di SQL Azure solo; non include SQL in esecuzione in macchine virtuali).|
|[Abilitare il database SQL di controllo](security-center-enable-auditing-on-sql-databases.md)|Si consiglia l'attivazione del controllo per i database di SQL Azure (servizio di SQL Azure solo; non include SQL in esecuzione in macchine virtuali).|
|[Abilitare trasparente la crittografia dei dati nel database SQL](security-center-enable-transparent-data-encryption.md)|Si consiglia di abilitare la crittografia per i database SQL (solo servizio di SQL Azure).|

## <a name="see-also"></a>Vedere anche

Per ulteriori informazioni sui consigli che si applicano ad altri tipi di risorse Azure, vedere gli articoli seguenti:

- [Protezione macchine virtuali nel Centro protezione di Azure](security-center-virtual-machine-recommendations.md)
- [Proteggere le applicazioni nel Centro protezione di Azure](security-center-application-recommendations.md)
- [Protezione alla rete aziendale in Centro protezione di Azure](security-center-network-recommendations.md)

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
