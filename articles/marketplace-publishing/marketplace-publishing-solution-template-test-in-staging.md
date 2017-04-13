<properties
   pageTitle="Test l'offerta di modello soluzione per il Marketplace | Microsoft Azure"
   description="Imparare a test l'offerta di modello soluzione per Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/04/2015"
   ms.author="hascipio; v-divte" />

# <a name="test-your-solution-template-offer-in-staging"></a>Verificare l'offerta di modello soluzione di gestione temporanea
Gestione temporanea indica che la distribuzione l'offerta in private "sandbox" in cui è possibile testare e verificarne le funzionalità prima di pubblicarlo nell'ambiente di produzione. L'offerta verrà visualizzato nella gestione temporanea come nel caso a un cliente che ha distribuito in. L'offerta deve essere certificata il push di gestione temporanea.

Dopo l'offerta viene gestita, è possibile visualizzare e testare l'offerta nel [Portale di Azure](https://portal.azure.com/).

Eseguire la procedura seguente per inserire l'offerta di gestione temporanea ed eseguire il test nel [Portale di Azure](https://portal.azure.com/):

1.  Passare al [Portale di pubblicazione](https://publish.windowsazure.com) > scheda**Modelli di soluzioni** > l'offerta > **pubblica** > **Push di gestione temporanea**.
2.  Fornire l'elenco delle sottoscrizioni Azure che verrà utilizzato per visualizzare in anteprima e testare l'offerta.
3.  Accedere al portale di Azure anteprima utilizzando l'ID di abbonamento usato nel passaggio precedente.
4.  Eseguire almeno un ciclo di test nel portale di Azure anteprima punti riportata di seguito:
  - Assicurarsi che il contenuto di marketing viene visualizzata correttamente in Azure Marketplace.
  - Distribuzione-to-end della topologia.
  - Eseguire la verifica delle prestazioni e il test intensivo.
  - Assicurarsi che la topologia aderisce alle procedure consigliate.

## <a name="next-steps"></a>Passaggi successivi
Se si è soddisfatti del risultato, quindi è possibile procedere alla fase pubblicazione finale offerta **passaggio 4**: [distribuire l'offerta Marketplace](marketplace-publishing-push-to-production.md). In caso contrario, apportare le modifiche nell'offerta e la richiesta di certificazione nuovamente.

> [AZURE.NOTE] Modifiche contenuto marketing, certificazione non sono necessarie.

Vedere [Guida introduttiva: come pubblicare un'offerta di Azure Marketplace](marketplace-publishing-getting-started.md) per una Guida per tutte le attività di publisher.
