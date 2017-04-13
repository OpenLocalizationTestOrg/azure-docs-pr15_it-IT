<properties
   pageTitle="Applicare gli aggiornamenti di sistema nel Centro protezione di Azure | Microsoft Azure"
   description="In questo documento viene illustrato come implementare i suggerimenti di Centro protezione di Azure **applicare gli aggiornamenti di sistema** e **riavviare il computer dopo l'aggiornamento del sistema**."
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

# <a name="apply-system-updates-in-azure-security-center"></a>Applicare gli aggiornamenti di sistema nel Centro protezione di Azure

Centro protezione di Azure esegue il monitoraggio giornaliero Windows e Linux macchine () per gli aggiornamenti del sistema operativo mancanti. Nel Centro sicurezza recupera un elenco di aggiornamenti critici e protezione disponibili da Windows Update o Windows Server Update Services (WSUS), a seconda di quale servizio è configurato in una macchina virtuale di Windows.  Nel Centro sicurezza anche verifica la presenza di aggiornamenti apportati alle caratteristiche di sistemi Linux. Se la macchina virtuale manca un aggiornamento di sistema, Centro protezione verranno consigliabile applicare gli aggiornamenti del sistema

> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio.  Questa non è una Guida dettagliata.

## <a name="implement-the-recommendation"></a>Implementare la recommendation

1. In e il **raccomandazioni** , selezionare **Applica gli aggiornamenti di sistema**.
![Applicare gli aggiornamenti di sistema][1]

2. Viene visualizzata e il **aggiornamenti del sistema applica** un elenco di macchine virtuali gli aggiornamenti di sistema mancanti. Selezionare una macchina virtuale.
![Selezionare una macchina virtuale][2]

3. Blade verrà visualizzata con un elenco degli aggiornamenti mancanti per tale macchina virtuale. Selezionare un aggiornamento di sistema. In questo esempio, se si seleziona KB3156016.
![Aggiornamenti della sicurezza mancanti][3]

4. Seguire i passaggi e **l'Aggiornamenti per la protezione** per applicare gli aggiornamenti mancanti.
![Aggiornamento della sicurezza][4]

## <a name="reboot-after-system-updates"></a>Riavviare il computer dopo l'aggiornamento del sistema

5. Tornare a e il **consigli** . Una nuova voce generata dopo l'applicazione di aggiornamenti di sistema, chiamati **riavvio dopo l'aggiornamento del sistema**. Questa voce indica che è necessario riavviare la macchina virtuale per completare il processo di applicazione degli aggiornamenti del sistema.
![Riavviare il computer dopo l'aggiornamento del sistema][5]

6. Selezionare **riavvio dopo l'aggiornamento del sistema**. Verrà aperta blade **che riavviare il computer è in sospeso per completare gli aggiornamenti del sistema** Visualizza un elenco di macchine virtuali che è necessario riavviare per completare il processo di aggiornamenti di sistema applica.
![Riavviare in sospeso][6]

Riavviare la macchina virtuale comuni per completare il processo.

## <a name="see-also"></a>Vedere anche

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md) - informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Il monitoraggio dell'integrità di protezione in Centro protezione di Azure](security-center-monitoring.md) informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Monitoraggio di soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) - informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
- Post di [blog di sicurezza di Windows azure](http://blogs.msdn.com/b/azuresecurity/) - Trova blog su Azure sicurezza e conformità.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]:./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png
