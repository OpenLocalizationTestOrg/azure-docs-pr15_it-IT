<properties
   pageTitle="Distribuire l'offerta di Azure Marketplace | Microsoft Azure"
   description="Informazioni su e scorrere le istruzioni per distribuire l'offerta: immagine di macchina virtuale, servizio di sviluppo, servizio dati e così via, a Azure Marketplace."
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
   ms.date="08/02/2016"
   ms.author="hascipio" />

# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Distribuire l'offerta di Azure Marketplace
Quando si è soddisfatti l'offerta (vale a dire il test di scenari dei clienti, marketing, il contenuto e così via) e si è pronti avviare, richiedere **Push di produzione** nella scheda **pubblica** .  

1. Quattro passaggi in procedura dettagliata pagina nella pubblicazione portale deve essere completate e verde. Per le offerte macchina virtuale, assicurarsi che le linee guida seguenti sono seguite.

    ![disegno][img-pubportal-walkthru-checked]

2. Selezionare la scheda **pubblica** nell'elenco sul lato sinistro.

    ![disegno][img-pubportal-menu-publish]

3. Fare clic sul pulsante **richiedere l'approvazione da inserire nell'ambiente di produzione**. Una volta la richiesta, il team di approvazione esegue una revisione finale e quindi l'offerta sarà di nuovo disponibile in Azure Marketplace.

    ![disegno][img-pubportal-publish-pushproduction]

>[AZURE.IMPORTANT] In caso di macchine virtuali, quando si fa clic sul pulsante della richiesta di approvazione a produzione, la procedura seguente vengono eseguita dietro la scena. Sarà possibile visualizzare lo stato di avanzamento di ogni passaggio della scheda pubblica in pubblicazione portal. È necessario controllare questa pagina intervalli regolari (fino a quando non viene visualizzato lo stato "Elencati") per tutte le informazioni di errore che richiedono correzione dalla fine.

> - Prima la richiesta di produzione viene inviata al team di certificazione che convalidare il disco rigido virtuale. Tuttavia, se si sta aggiornando l'offerta già nell'elenco e la richiesta dispone solo di marketing, modifica, il passaggio di certificazione viene ignorato.
> - Al passaggio successivo, la richiesta provenire al team della convalida del contenuto che verificare i contenuti di marketing dell'offerta.
> - Se la procedura sopra indicata hanno esito positivo, l'offerta è approvata in produzione. In questa fase, lo stato "all'interno" nel portale di pubblicazione. Tuttavia, questo stato "Elencati" non implica che il processo è stato completo. I passaggi seguenti devono essere completata prima che l'offerta è disponibile in Azure Marketplace.
> - Dopo l'approvazione dell'offerta di produzione nel passaggio precedente, la replica dell'offerta avviata tra i Data Center Azure. In genere, accetta 24 48hours per il completamento della replica ma può richiedere fino a una settimana prima che a seconda delle dimensioni del file. Tuttavia, se si sta aggiornando l'offerta già elencato e ha solo di marketing, modifica, la replica è più veloce.
> - Una volta completata la replica, quindi l'offerta sarà disponibile in Azure Marketplace.

> È sempre possibile eliminare l'offerta mentre è nello stato **bozza** (ad esempio, non è mai **Push di gestione temporanea** o **Push di produzione**). Nella scheda **cronologia** , fare clic sul pulsante **Elimina bozza** nella parte inferiore della pagina per eliminare una bozza.


## <a name="production-checklist-for-all-virtual-machine-offers"></a>Elenco di controllo di produzione per tutte le offerte di macchina virtuale

- Assicurarsi di essere un partner certificati di Microsoft Azure
- Nella scheda SKU l'opzione "nascondere questa SKU di Marketplace perché deve sempre essere acquistato tramite un modello di soluzione" devono essere contrassegnati come Sì solo se lo SKU fa parte di un modello di soluzione. In tutti gli altri casi, questa opzione deve sempre essere contrassegnata come No.
- Tenere presenti: È consigliabile non modificare l'impostazione di visibilità SKU dopo lo SKU è presente nell'elenco. Questa funzionalità non è supportata.
- Assicurarsi che il logo soddisfa i criteri di logo di Azure Marketplace indicati di seguito.
- Descrizione offerta e SKU non deve essere uguali.
- Del SKU titolo e offrono lungo riepilogo non deve essere uguale a.
- Titolo SKU e offrono riepilogo non deve essere uguale a.
- I titoli SKU non devono essere identici per un'offerta con più SKU.

**Linee guida logo di Azure Marketplace**

- La struttura di Azure ha una tavolozza dei colori semplice. Mantenere il numero di principale e secondaria colori nel logo bassa.
- I colori del tema del portale di Azure sono bianchi e nero. Pertanto evitare l'utilizzo di questi colori come colore di sfondo per il logo. Usare un colore che da rendere visibili nel portale di Azure il logo. È consigliabile colori semplici. Se si usa sfondo trasparente, verificare che il logo/testo non sia di colore bianco o nero.
- Non utilizzare uno sfondo sfumato sul logo.
- Evitare l'inserimento di testo, anche una società o i fantasia, sul logo.
- L'aspetto del logo deve essere 'semplice' ed evitare sfumature.
- Il logo non possono essere ridimensionato.

**Indicazioni aggiuntive per il logo di immagine:**

- Logo eroe è facoltativo. L'autore può scegliere di non carica un logo eroe. **Tuttavia una volta caricato l'icona di immagine non può essere eliminata dalla pubblicazione portal. In quel momento, il partner deve seguire le istruzioni di Azure Marketplace per altre icone eroe che l'offerta non sono stato approvato nell'ambiente di produzione.**
- Il nome di visualizzazione dell'autore, titolo SKU e l'offerta tempo riepilogo vengono visualizzate nel colore bianco. Pertanto non è consigliabile mantenere eventuali visualizzarla in background dell'icona di immagine. Nero, bianco e trasparente lo sfondo non è consentito per visualizzare le icone eroe.
- L'autore visualizzare nome, titolo SKU, l'offerta tempo riepilogo e il pulsante Crea sono incorporati a livello di programmazione all'interno del logo eroe dopo l'offerta passa nell'elenco. Pertanto non è necessario immettere il testo eventualmente contenuto durante la progettazione del logo di immagine. Lasciare spazio vuoto a destra, in quanto il testo (ad esempio visualizzazione di Publisher nome, titolo SKU, l'offerta tempo riepilogo) sarà incluso a livello di programmazione da Microsoft su tale posizione. Lo spazio vuoto per il testo deve essere 415 x 100 sulla destra (e viene spostato di 370px da sinistra).


## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Elenco di controllo di produzione aggiuntive per macchina virtuale già elencati offre

- Verificare se è già presente un'offerta con lo stesso nome offerta della società. In caso affermativo, è necessario aggiungere una nuova versione lo SKU nell'offerta di esistente invece di creare una nuova offerta duplicato.
- Non modificare disco dati tra due versioni dello stesso SKU.
- Azure Marketplace non supporta la modifica dei prezzi dei SKU elencati come che influisce sulla fatturazione dei clienti esistenti. Assicurarsi che non si modifica il prezzo del SKU elencati nelle aree in cui è disponibile lo SKU. Tuttavia, è possibile aggiungere ai nuovi SKU o aggiungere nuove aree una SKU esistente.


## <a name="next-steps"></a>Passaggi successivi
Dopo l'offerta entra attivo, verificare gli scenari di assistenza clienti per convalidare tutti i contratti e funzionalità il corretto funzionamento nell'ambiente di produzione come testati e convalidati nell'ambiente di gestione temporanea.

## <a name="see-also"></a>Vedere anche
- [Guida introduttiva: come pubblicare un'offerta di Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
