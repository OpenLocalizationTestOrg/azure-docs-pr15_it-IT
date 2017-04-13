<properties
   pageTitle="Verificare l'offerta di macchine Virtuali per il Marketplace | Microsoft Azure"
   description="Imparare a testare l'immagine di macchine Virtuali di Azure Marketplace."
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
   ms.date="08/01/2016"
   ms.author="hascipio" />

# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Verificare l'offerta di macchine Virtuali di Azure Marketplace di gestione temporanea

Gestione temporanea indica che la distribuzione lo SKU in private "sandbox" in cui è possibile testare e convalidare la relativa funzionalità prima di distribuire Marketplace. Lo SKU viene visualizzata in gestione temporanea come nel caso a un cliente che ha distribuito in. L'immagine di macchine Virtuali deve essere certificata il push di gestione temporanea.

## <a name="step-1-push-your-offer-to-staging"></a>Passaggio 1: Inserire l'offerta di gestione temporanea

1. Nella scheda **pubblica** fare clic su **Push di gestione temporanea**.

    ![disegno](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Se il portale di pubblicazione che informa degli errori, correggerli.
3.  Nel **chi può accedere l'offerta a fasi?** finestra di dialogo immettere l'elenco delle sottoscrizioni Azure che verrà utilizzato per visualizzare in anteprima l'offerta nel [portale di Azure anteprima](https://portal.azure.com).

    >[AZURE.NOTE] In caso di macchine virtuali e soluzione modelli, effettuare le sottoscrizioni di proprietà consentite **non** di tipo CSP, DreamSpark o Azure in aperto.


    > In caso di macchine virtuali, quando si fa clic sul pulsante **PUSH di gestione temporanea**, la procedura seguente vengono eseguita dietro la scena. Sarà possibile visualizzare lo stato di avanzamento di ogni passaggio della scheda pubblica in pubblicazione portal. È necessario controllare questa pagina intervalli regolari (fino a quando non viene visualizzato lo stato a fasi) per tutte le informazioni di errore che richiedono correzione dalla fine.

    > - Prima della richiesta di gestione temporanea passa al team di certificazione che convalidare il disco rigido virtuale. Tuttavia, se la richiesta dispone solo di marketing, modifica, il passaggio di certificazione viene ignorato.
    > - Al termine della certificazione di replica dell'offerta iniziare tra i Data Center Azure. In genere, accetta 24 48hours per il completamento della replica ma può richiedere fino a una settimana prima che a seconda delle dimensioni del file. Tuttavia, se la richiesta dispone solo di marketing, modifica, la replica è più veloce.
    > - Una volta completata la replica, l'offerta sarà disponibile nel [portale di Azure](http:/portal.azure.com). In tale periodo lo stato diventano INSERITE nella pubblicazione portal. Un'offerta a fasi è visibile nel [portale di Azure](http:/portal.azure.com) solo con gli ID di posta elettronica associati all'abbonamento a cui viene gestita l'offerta.

4. Accedere per visualizzare in [Anteprima di Azure portale](https://portal.azure.com) tramite una delle sottoscrizioni di Azure elencate nel passaggio precedente.
5. Individuare l'offerta e convalidare i punti di immagine macchine Virtuali:
  - Assicurarsi che contenuti di marketing viene visualizzata correttamente nel mercato.
  - Distribuzione di fine-fine dell'immagine macchine Virtuali.

      ![portale di mappa IMG](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [AZURE.IMPORTANT] L'offerta rimarrà in fino a quando non una notifica Microsoft tramite il portale di pubblicazione di gestione temporanea [scheda**pubblica** > fare clic sul pulsante **"richiedere l'approvazione per Push di produzione"**] che si è pronti a produzione. Si tratta di un'ora lo strumento ideale per tutti i membri del team assegno su tutti gli elementi in preparazione per l'offerta passando nell'elenco.

> La piattaforma di gestione temporanea è progettata per la verifica dell'offerta in modalità anteprima dal server di pubblicazione. Se ne sconsiglia fortemente utilizzando questo platofrm ai fini della commerciale.

## <a name="next-steps"></a>Passaggi successivi
Ora che l'offerta è "preconfigurato" e il test di funzionalità e contenuti di marketing, è possibile procedere alla fase di pubblicazione finale, **il passaggio 4**: [distribuire l'offerta Marketplace](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Vedere anche
- [Guida introduttiva: come pubblicare un'offerta di Azure Marketplace](marketplace-publishing-getting-started.md)
