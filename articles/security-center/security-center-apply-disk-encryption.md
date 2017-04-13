<properties
   pageTitle="Applicare la crittografia del Centro protezione di Azure | Microsoft Azure"
   description="In questo documento viene illustrato come implementare il suggerimento di Centro protezione di Azure **Applica la crittografia del disco**."
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

# <a name="apply-disk-encryption-in-azure-security-center"></a>Applicare la crittografia del Centro protezione di Azure

Centro protezione di Azure indicazioni applicare la crittografia del se si dispone di Windows o Linux VM dischi che non vengono crittografati utilizzando la crittografia del Azure. La crittografia del consente di crittografare il disco di Windows e macchine Virtuali IaaS Linux.  La crittografia è consigliata per i volumi sistema operativo e dati di una macchina virtuale.


La crittografia del utilizza la funzionalità di [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) standard di settore di Windows e la funzionalità di [Crittografia di data mining](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire del sistema operativo e crittografia dei dati per proteggere e proteggere i dati e gli impegni dell'organizzazione per la sicurezza e conformità. La crittografia del è integrata con [Azure chiave archivio](https://azure.microsoft.com/documentation/services/key-vault/) consentono di controllare e gestire le chiavi di crittografia disco e le informazioni riservate nel proprio abbonamento chiave archivio, assicurandosi che tutti i dati nei dischi macchine Virtuali vengono crittografati inattivi in [Archiviazione Azure](https://azure.microsoft.com/documentation/services/storage/).

> [AZURE.NOTE] Crittografia disco Azure è supportata nei seguenti sistemi operativi Windows server - Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. La crittografia del è supportata nei seguenti Linux server sistemi operativi - Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES).

## <a name="implement-the-recommendation"></a>Implementare la recommendation

1. Nella finestra e **raccomandazioni** , selezionare **Applica la crittografia del disco**.
2. In e **l'Applica la crittografia del disco** , si verrà visualizzato un elenco di macchine virtuali per il quale si consiglia la crittografia del disco.
3. Seguire le istruzioni per applicare la crittografia a queste macchine virtuali.

![][1]

Per crittografare macchine virtuali di Azure identificato dal Centro protezione come la necessità di crittografia, è consigliabile la procedura seguente:

- Installare e configurare Azure PowerShell. In questo modo sarà possibile eseguire i comandi di PowerShell necessari per impostare i prerequisiti necessari per crittografare macchine virtuali di Azure.
- Ottenere ed eseguire lo script di PowerShell di Azure prerequisiti di Azure disco la crittografia.
- Crittografare le macchine virtuali.

[Crittografa una macchina virtuale Azure](security-center-disk-encryption.md) consentono all'utente la procedura seguente.  Si presuppone che si usa Windows 10 come computer client da cui si configura la codifica del disco.

Sono disponibili molti approcci che possono essere usati per i prerequisiti di installazione e per configurare la crittografia per macchine virtuali di Azure. Se si ha già ben versed in Azure PowerShell o CLI Azure, si preferisce utilizzare soluzioni alternative. Per ulteriori informazioni su questi altri approcci vedere [crittografia Azure disco](../security/azure-security-disk-encryption.md).



## <a name="see-also"></a>Vedere anche

In questo documento è stato illustrato come implementare il suggerimento di Centro protezione "Applica la crittografia disco". Per ulteriori informazioni sulla crittografia disco, vedere gli articoli seguenti:

- [Crittografia e gestione delle chiavi con Azure chiave archivio](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video e 36 min 39 sec): informazioni su come utilizzare la crittografia del disco per macchine virtuali IaaS e Azure chiave archivio per proteggere e proteggere i dati.
- [Crittografare una macchina virtuale Azure](security-center-disk-encryption.md) (documento): informazioni su come crittografare macchine virtuali di Azure.
- [Crittografia del Azure](../security/azure-security-disk-encryption.md) (documento): informazioni su come abilitare la crittografia del disco per Windows e macchine virtuali Linux.

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza.
- [Il monitoraggio dell'integrità di protezione in Centro protezione di Azure](security-center-monitoring.md) informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md) - informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
- Post di [blog di sicurezza di Windows azure](http://blogs.msdn.com/b/azuresecurity/) - Trova blog su Azure sicurezza e conformità.



<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
