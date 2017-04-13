<properties
    pageTitle="Impostare avvisi per le sottoscrizioni di Microsoft Azure di fatturazione | Microsoft Azure"
    description="Descrive come è possibile impostare avvisi nella fattura Azure in modo che è possibile evitare sorprese fatturazione."
    services=""
    documentationCenter=""
    authors="vikdesai"
    manager="mbaldwin"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/18/2016"
    ms.author="vikdesai"/>

# <a name="set-up-billing-alerts-for-your-microsoft-azure-subscriptions"></a>Impostare gli avvisi di fatturazione per le sottoscrizioni di Microsoft Azure

Si teme quantità sta spese mensili per l'abbonamento Azure? Se si è l'amministratore dell'account a un abbonamento di Azure, è possibile utilizzare il servizio di avviso Azure fatturazione per creare un personalizzato fatturazione avvisi che consentono di controllare e gestire attività fatturazione per gli account Azure.

Questo servizio è un servizio di anteprima, in modo che la prima cosa che è necessario effettuare è accesso per renderla. Visitare [la pagina caratteristiche Preview](https://account.windowsazure.com/PreviewFeatures) nel portale di gestione di account Azure per abilitare questa funzionalità.

## <a name="set-the-alert-threshold-and-email-recipients"></a>Impostare i destinatari degli avvisi di soglia e la posta elettronica

Dopo aver ricevuto la conferma di posta elettronica che è attivato il servizio di fatturazione per l'abbonamento, visitare [la pagina abbonamenti](https://account.windowsazure.com/Subscriptions) nel portale di account. Fare clic sull'abbonamento che si desidera eseguire il monitoraggio e quindi fare clic su **avvisi**.

![][Image1]

Fare quindi clic su **Aggiungi avviso** per creare il primo, è possibile impostare un totale di cinque avvisi fatturazione per l'abbonamento, con un'altra soglia e fino a due destinatari di posta elettronica per ogni avviso.

![][Image2]

Quando si aggiunge un avviso, si assegnare un nome univoco, sceglie un limite di spesa e scegliere gli indirizzi di posta elettronica in cui verranno inviati gli avvisi. Quando si imposta la soglia, è possibile scegliere dall'elenco **Degli avvisi per** un **Totale di fatturazione** o una **Carta di credito monetari** . Per un totale di fatturazione, viene inviato un avviso quando abbonamento spese supera la soglia. Per una carta di credito monetario, viene inviato un avviso quando crediti monetari rilascia sotto il limite. Crediti monetari in genere applicano gratuite e sottoscrizioni associate agli account MSDN.

![][Image3]

Azure supporta qualsiasi indirizzo di posta elettronica ma non consente di verificare che lavori di indirizzo di posta elettronica, in modo verificare l'ortografia.

## <a name="check-on-your-alerts"></a>Verificare se gli avvisi

Dopo aver configurato gli avvisi, l'interfaccia di Account elencati e illustra il numero è più possibile configurare. Per ogni avviso, vedere la data e ora in cui che è stato inviato, se si tratta di un avviso per la fatturazione totale o monetari carta di credito e il limite di che configurazione. Il formato di data e ora è 24 ore ora UTC (Coordinate) e la data è il formato gg / mm-aaaa. Fare clic sul segno più per ricevere un avviso nell'elenco per modificarlo o fare clic su Cestino per eliminarlo.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png
