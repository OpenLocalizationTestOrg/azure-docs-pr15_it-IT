<properties
   pageTitle="Test l'offerta di servizi di dati per il Marketplace | Microsoft Azure"
   description="Imparare a test l'offerta di servizi di dati di Azure Marketplace."
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
   ms.date="08/26/2016"
   ms.author="hascipio; avikova" />

# <a name="testing-your-data-service-offer-in-staging"></a>Verificare l'offerta di servizi di dati in gestione temporanea

>[AZURE.IMPORTANT] **In questo momento sono onboarding qualsiasi nuovi autori di servizio dati. Nuovo dataservices non ottenere approvato per l'elenco.** Se si dispone di un'applicazione aziendale SaaS si desidera pubblicare nella AppSource è possibile trovare altre informazioni [di seguito](https://appsource.microsoft.com/partners). Se si dispone di un'applicazione IaaS o servizio sviluppo da pubblicare in Azure Marketplace è possibile trovare ulteriori informazioni [qui](https://azure.microsoft.com/marketplace/programs/certified/).

Dopo aver completato i primi due passaggi di [creazione dell'account Microsoft Developer](marketplace-publishing-accounts-creation-registration.md) e [creare l'offerta di servizio dati nel portale pubblicazione](marketplace-publishing-data-service-creation.md) si è pronti per rendere disponibile l'offerta in Azure Marketplace. In questo argomento viene illustrato come intermedia prima di tutto, istruzione denominata "Prova"

Gestione temporanea indica che la distribuzione l'offerta in private "sandbox" in cui è possibile testare e verificarne le funzionalità prima di pubblicarlo nell'ambiente di produzione. Di gestione temporanea come nel caso a un cliente che ha distribuito verrà visualizzata l'offerta.

## <a name="step-1-pushing-your-offer-to-staging"></a>Passaggio 1. Inserendo l'offerta di gestione temporanea
Inserendo l'offerta di gestione temporanea consente di verificare l'offerta prima che diventi disponibile per gli abbonati futuri.  È possibile vedere come l'offerta la corretta visualizzazione e per quelli la sottoscrizione ai dati.  

  ![disegno](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1.  Accesso nel [portale di pubblicazione](https://publish.windowsazure.com)
2.  Selezionare **I servizi di dati** nella finestra di navigazione sinistro
3.  Selezionare l'offerta che si desidera push di gestione temporanea. Verrà visualizzata la schermata precedente.
4.  Fare clic su **Push di gestione temporanea** .  
5.  Se sono presenti problemi con l'offerta che devono essere completate prima dell'inserimento di gestione temporanea, verrà visualizzato un elenco visualizzato.  Correggere questi elementi facendo clic su ogni elemento nell'elenco. Quando tutte le correzioni apportate Fare nuovamente clic pulsante **Push di gestione temporanea** .

Se non esistono problemi con l'offerta verrà visualizzato finestra popup.  

Se sta approvata per fornire l'interfaccia utente l'offerta nel portale di Azure non pianificazione/non (attualmente limitate capacità), quindi chiudere semplicemente la finestra popup.

Per testare il servizio di dati nel portale di Azure (oltre alle portal DataMarket), è necessario un ID abbonamento Azure di test per.  Questo ID abbonamento verrà identificare l'account che sarà autorizzato a test l'offerta.  

Tagliare e incollare l'ID di abbonamento e fare clic sul segno di spunta per continuare.

  ![disegno](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [AZURE.NOTE] Sottoscrizioni Azure ID sono necessari per la verifica e di gestione temporanea nel [Portale di gestione Azure](https://manage.windowsazure.com). Non sono necessarie per verificare in Azure Marketplace.

Nella schermata successiva visualizzata indica che la pubblicazione è stata effettuata visualizzando l'icona "In corso" evidenziata giallo riportata di seguito. Inserimento di gestione temporanea accetta tra 10-15 minuti.  Se richiede più tempo, prima di tutto aggiornare il browser (premendo F5 in Internet Explorer).  In rari casi in cui l'offerta è ancora inserendo di gestione temporanea dopo un'ora, fare clic sul contatto Aiutaci a creare un collegamento a indicare che si verifica un problema.

  ![disegno](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

Quando il Push di gestione temporanea viene completata l'icona "In corso" Interrompi spostamento e lo stato verrà avranno a "A fasi".  A questo punto si è pronti testare l'offerta.  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>Passaggio 2. Testare l'offerta a fasi in DataMarket

Fare clic sul collegamento dopo il testo **"Vedere il servizio offrono a..."** Per visualizzare la schermata che il server di sottoscrizione verrà visualizzato quando l'offerta passa alla produzione, verranno visualizzate nella DataMarket.

  ![disegno](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Testare o verificare ogni 12 elementi contrassegnati sopra per assicurarsi che tutti logo, prezzi/transazioni, testo, immagini, documentazione e collegamenti siano corretti e che funzioni correttamente.  Questo è il momento migliore per assicurarsi che i valori di test che è stata immessa quando si creano l'offerta sono stati sostituiti con i valori effettivi.

1. Logo di offerta
2. Nome offerta
3. Publisher nome/collegamento al sito Web della società
4. Categorie di ricerca per l'offerta
5. Collegamento di supporto dell'offerta per agevolare la sottoscrizione
6. Descrizione di scelta rapida per l'offerta
7. Piano di offerta che illustra i dettagli di fatturazione
8. Collegamento al codice di implementazione
9. Immagini di esempio che illustrano utilizzano dei dati di offerta
10. Metadati di ingresso/uscita per ogni servizio all'interno dell'offerta
11. Condizioni dell'offerta per l'utilizzo
12. Anteprima dei dati dell'offerta


Infine, controllare che il servizio funzionerà tramite il Datamarket facendo clic sul collegamento "Esplorare il set di dati".  Verrà aperta una nuova finestra nello strumento si chiama "Servizio Esplora risorse" in modo che è possibile visualizzare in anteprima i risultati di una query in base al servizio.  In questa finestra, è possibile immettere i parametri necessari e visualizzare i risultati visualizzati da una query al servizio.   Inoltre, visualizzato è l'URL per la Query.  

> [AZURE.NOTE] Assicurarsi di esaminare la descrizione del servizio visualizzato nella parte superiore.  E se l'offerta è costituita da più di un servizio di chiamata, fare clic sulle schede nella parte inferiore per passare al servizio successivo per esaminare e testare.



## <a name="next-step"></a>Passaggio successivo
Se si riscontrano problemi e assistenza per risolvere il problema, contattare [Il supporto di Publisher Azure]( http://go.microsoft.com/fwlink/?LinkId=272975).

Se si è soddisfatti e pronti a pubblicare l'offerta leggere la documentazione per la [Richiesta di approvazione per Push di produzione](marketplace-publishing-push-to-production.md) .

## <a name="see-also"></a>Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta di Azure Marketplace](marketplace-publishing-getting-started.md)
