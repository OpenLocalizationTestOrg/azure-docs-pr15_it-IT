<properties
    pageTitle="Sincronizzazione di Azure AD Connect: Gestione servizi di sincronizzazione UI | Microsoft Azure"
    description="Informazioni sulla scheda operazioni di Gestione servizio di sincronizzazione per Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Sincronizzazione di Azure AD Connect: Gestione servizi di sincronizzazione

[Operazioni](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Connettori](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Finestra di progettazione Metaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Ricerca nel Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

Scheda operazioni Mostra i risultati delle operazioni più recente. Questa scheda è fondamentale per comprendere e risoluzione dei problemi.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Comprendere le informazioni visibile nella scheda operazioni
Nella parte superiore mostra tutte le esecuzioni in ordine cronica. Per impostazione predefinita, il log di operazioni mantiene informazioni negli ultimi sette giorni, ma questa impostazione può essere modificata con l' [utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md). Si desidera cercare qualsiasi esecuzione che non viene visualizzato uno stato di completamento. È possibile modificare l'ordinamento facendo clic sulle intestazioni.

Nella colonna **stato** le informazioni più importanti che mostra il problema più grave per l'esecuzione. Ecco un breve riepilogo stati più comuni in ordine di priorità per esaminare (in cui * indicare più stringhe di errore possibili).

Stato | Commento
--- | ---
arrestato-* | Impossibile completare l'esecuzione. Ad esempio, se il sistema remoto è inattivo e non può essere contattato.
limite di errore arrestato | Sono presenti più di 5.000 errori. Esegui automaticamente interrotta a causa del numero elevato di errori.
completata -\*-errori | Esegui completato, ma ci sono errori (meno di 5.000) che devono essere esaminati.
completata -\*-avvisi | Esegui completato, ma alcuni dati non è stato previsto. Nel caso di errori, questo messaggio viene in genere solo il problema. Fino a quando non è stato esaminato errori, è necessario individuare non avvisi.
successo | Non sono presenti problemi.

Quando si seleziona una riga, nella parte inferiore Aggiorna per visualizzare i dettagli di quell ' esecuzione. All'estrema sinistra della parte inferiore, può essere necessario un elenco in cui viene indicato **passaggio #**. In questo elenco viene visualizzata solo se si hanno più domini della foresta in cui ogni dominio è rappresentato da un passaggio. Il nome di dominio sono disponibili sotto l'intestazione **partizione**. In **Le statistiche della sincronizzazione**, sono disponibili ulteriori informazioni sul numero di modifiche che sono stati elaborati. È possibile fare clic sui collegamenti per ottenere un elenco degli oggetti modificati. Se si dispone di oggetti con errori, gli errori visualizzata sotto **Gli errori di sincronizzazione**.

## <a name="troubleshoot-errors-in-operations-tab"></a>Risolvere gli errori di scheda operazioni
![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png)  
Quando sono presenti errori, oggetto errore e l'errore stesso sono collegamenti che vengono fornite ulteriori informazioni.

Iniziare facendo clic sulla stringa di errore (**attivate sincronizzazione-regola-errore-funzione** nell'immagine). Prima di tutto viene visualizzata con una panoramica dell'oggetto. Per visualizzare l'errore effettivo, fare clic sul pulsante **Analisi dello Stack**. La traccia fornisce informazioni sul livello di debug relativa all'errore.

**Suggerimento:** È possibile pulsante destro del mouse nella finestra di **informazioni sullo stack** , scegliere **Seleziona tutto**e **Copia**. È quindi possibile copiare stack e osservare l'errore in un editor, ad esempio Blocco note.

- Se l'errore è da **SyncRulesEngine**, le informazioni dello stack di chiamata sono prima di tutto un elenco di tutti gli attributi dell'oggetto. Scorrere verso il basso fino a quando non viene visualizzata l'intestazione **InnerException = >**.  
![Gestione di servizi di sincronizzazione](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png)  
Sulla riga successiva viene visualizzato l'errore. Nella figura riportata sopra, l'errore è da un Fabrikam di regola di sincronizzazione personalizzato creato.

Se l'errore stesso non fornisce le informazioni necessarie, è possibile esaminare i dati. È possibile fare clic sul collegamento con l'identificatore di oggetto e [seguire un oggetto e i relativi dati tramite il sistema](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
