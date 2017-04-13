<properties
    pageTitle="Aggiungere un ritardo nelle App logica | Microsoft Azure"
    description="Panoramica di ritardo e ritardo-fino a azioni e sul loro utilizzo con un'app di Azure logica."
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Iniziare a utilizzare il ritardo e ritardo-fino a azioni

Utilizzando il ritardo e "ritardo-fino a" azioni, è possibile completare flussi di lavoro.

Ad esempio, è possibile:

- Attendere fino a un giorno della settimana per inviare un aggiornamento di stato tramite posta elettronica.
- Ritardare il flusso di lavoro fino a una chiamata HTTP è stata eseguita per completare prima ripresa e recuperare i risultati.

Per iniziare a usare l'azione di ritardo in un'app di logica, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-delay-actions"></a>Usare le azioni di ritardo

Un'azione è un'operazione che viene eseguita dal flusso di lavoro definite in un'app di logica. [Altre informazioni sulle operazioni](connectors-overview.md).

Ecco una sequenza di esempio di utilizzo di un passaggio di ritardo in un'app di logica:

1. Dopo aver aggiunto un trigger, fare clic su **Nuova azione** per aggiungere un'azione.
2. Ricerca di **ritardo** visualizzare le azioni di ritardo. In questo esempio abbiamo selezionare **ritardo**.

    ![Azioni di ritardo](./media/connectors-native-delay/using-action-1.png)

3. Completare le proprietà azioni per configurare il ritardo.

    ![Configurazione di ritardo](./media/connectors-native-delay/using-action-2.png)

4. Fare clic su **Salva** per pubblicare e attivare l'app logica.


## <a name="action-details"></a>Dettagli di azione

Trigger ricorrenza include le proprietà seguenti possono essere configurate.

### <a name="delay-action"></a>Azione di ritardo

Questa azione consente di ritardare l'esecuzione in un determinato intervallo di tempo.
Risposte * significa che si tratti di un campo obbligatorio.

|Nome visualizzato|Nome della proprietà|Descrizione|
|---|---|---|
|Conteggio *|Conta. numeri|Il numero di unità di tempo di ritardo|
|Unità *|unità|L'unità di tempo: `Second`, `Minute`, `Hour`, o`Day`|
<br>

### <a name="delay-until-action"></a>Ritardo-fino a azione

Questa azione ritardi Esegui fino a data/ora specificata.
Risposte * significa che si tratti di un campo obbligatorio.

|Nome visualizzato|Nome della proprietà|Descrizione|
|---|---|---|
|Anno *|timestamp|L'anno di ritardo fino a (GMT)|
|Mese *|timestamp|Il mese di ritardo fino a (GMT)|
|Giorno *|timestamp|Il giorno di ritardo fino a (GMT)|
<br>


## <a name="next-steps"></a>Passaggi successivi

A questo punto, provare a usare la piattaforma e [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md). È possibile esplorare altri connettori disponibili nelle App logica esaminando il nostro [elenco API](apis-list.md).
