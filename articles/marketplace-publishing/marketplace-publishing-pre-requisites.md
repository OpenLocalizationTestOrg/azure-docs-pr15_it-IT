<properties
   pageTitle="Prerequisiti non tecniche per la creazione di un'offerta per Azure Marketplace | Microsoft Azure"
   description="Comprendere i requisiti per la creazione e la distribuzione di un'offerta di Azure Marketplace per consentire ad altri per l'acquisto."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="08/18/2016"
  ms.author="hascipio"/>

# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Prerequisiti generali per creare un'offerta per Azure Marketplace
Comprendere i prerequisiti generali business-incentrato sui processi necessari per spostarsi in avanti con il processo di creazione di offerta.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Verificare che siano registrati come venditore con Microsoft
Per istruzioni dettagliate sulla registrazione di un account del venditore con Microsoft, visitare [la registrazione e la creazione di Account](marketplace-publishing-accounts-creation-registration.md).

- **Se la propria azienda è già registrata come venditore nell'interfaccia di sviluppo e si desidera creare un'offerta di nuova,** quindi eseguire l'accesso per la pubblicazione del portale con lo stesso id di posta elettronica con il centro per sviluppatori viene eseguita la registrazione. Questo passaggio è necessario in modo che il portale di pubblicazione e Dev Center sono collegati tra loro.
- **Se la propria azienda è già registrata come venditore nell'interfaccia di sviluppo e si desidera modificare un'offerta esistente,** quindi effettuare l'accesso alla pubblicazione portale con l'account dell'amministratore o con un account viene aggiunto come un amministratore co in pubblicazione portal. Procedura per aggiungere un account di amministratore co è indicato di seguito.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>Procedura per aggiungere un amministratore di co nel portale di pubblicazione
Gli amministratori della pubblicazione portale possibile aggiungere altri membri della società, che utilizza l'applicazione, come un amministratore co in pubblicazione portal. **Presupponendo che l'amministratore** indicata di seguito sono riportati i passaggi per aggiungere un co-amministratore.

>[AZURE.NOTE] Per i nuovi utenti, prima aggiungere un amministratore di co nella pubblicazione portale, assicurarsi che sia stato creato almeno una domanda nella pubblicazione portal. Questa operazione è necessaria la scheda **EDITORI** visualizzare solo dopo la creazione di almeno un'applicazione di pubblicazione portal.

1. Assicurarsi che l'id di posta elettronica co-amministratore sia una account(MSA) Microsoft. In caso contrario, registrare come un MSA attraverso il [collegamento](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Verificare che sia presente almeno un'applicazione con l'account di amministratore prima di tentare di aggiungere un co-amministratore.
3. Dopo aver terminato la procedura riportata sopra, eseguire l'accesso per la pubblicazione del portale con l'id di posta elettronica co-amministratore e quindi disconnessione.
4. A questo punto l'accesso alla pubblicazione del portale con l'id di posta elettronica di amministrazione.
5. Passare al autori -> selezionare l'account -> amministratori -> Aggiungi l'amministratore di co (schermata indicata di seguito)

    ![disegno](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)

6. Assicurarsi che gli ID di posta elettronica forniti nelle varie fasi del processo di pubblicazione (ad esempio Centro per sviluppatori, portale di pubblicazione) vengono monitorati per le comunicazioni Microsoft.
7. Per la registrazione Centro per sviluppatori, evitare l'utilizzo di un account associato a una singola persona. Come suggerito per la rimozione di dipendenza da una singola persona.
8. Se riscontrati problemi relativi alla registrazione Centro per sviluppatori, generare un ticket attraverso il [collegamento](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>Procedura per eliminare un amministratore co in pubblicazione portale
**Presupponendo che l'amministratore** indicata di seguito sono riportati i passaggi per eliminare un co-amministratore.

1. Eseguire l'accesso per la pubblicazione del portale con l'id di posta elettronica di amministrazione.
2. Passare al **server di pubblicazione** -> selezionare l'account -> **amministratori** -> **Co-amministratori**.
3. Fare clic sul pulsante **X** accanto a co-amministrazione desiderata Elimina tot (schermata indicata di seguito).

    ![disegno](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [AZURE.IMPORTANT] Non è necessario completare informazioni sulla società imposte e banca se si prevede di pubblicare solo gratuite offerte (o visualizzare la propria licenza).

> Per iniziare, è necessario completare la registrazione della società. Tuttavia, mentre la propria azienda ha effetto su informazioni IVA e banca nella finestra account di Microsoft Developer, ovvero gli sviluppatori di possono iniziare a lavorare creazione dell'immagine macchina virtuale nel [Portale di pubblicazione](https://publish.windowsazure.com), ottenerlo certified e verifica nell'ambiente di gestione temporanea Azure. È necessario l'approvazione di account venditore completo solo per il passaggio finale della pubblicazione l'offerta di Azure Marketplace.

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Acquistare un abbonamento "in" Azure
Si tratta in abbonamento che verrà utilizzato per creare le immagini di macchine Virtuali e distribuire le immagini a [Azure Marketplace](https://azure.microsoft.com/marketplace/). Se non si dispone di una sottoscrizione esistente, quindi iscriversi al https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>"Vendere da" paesi
> [AZURE.WARNING]
Per poter vendere i servizi in Azure Marketplace, è necessario assicurarsi che l'entità registrati è stata inviata da uno dei paesi approvato "vendere-da". Questa limitazione è per motivi di pagamento e fiscali. Siamo attivamente per espandere l'elenco dei paesi nel prossimo futuro, pertanto, visita. Per l'elenco completo, vedere sezione 1b dei [criteri di partecipazione Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).

## <a name="next-steps"></a>Passaggi successivi
Una volta i prerequisiti sono soddisfatte, Avanti è l'offerta prerequisiti tecnici specifici. Fare clic sul collegamento all'articolo per il tipo di offerta rispettivi che si desidera creare per Azure Marketplace.

- [Prerequisiti tecnici macchine Virtuali](marketplace-publishing-vm-image-creation-prerequisites.md)
- [Soluzione modello tecnici prerequisiti](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Vedere anche
- [Guida introduttiva: come pubblicare un'offerta di Azure Marketplace](marketplace-publishing-getting-started.md)
