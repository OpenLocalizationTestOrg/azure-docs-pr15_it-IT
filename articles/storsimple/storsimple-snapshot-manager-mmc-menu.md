<properties 
   pageTitle="Azioni di menu Gestione Snapshot StorSimple MMC | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare le azioni di menu standard Microsoft Management Console (MMC) in StorSimple Snapshot Manager."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/25/2016"
   ms.author="v-sharos" />

# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Usare le azioni di menu MMC in StorSimple Snapshot Manager

## <a name="overview"></a>Panoramica

In Gestione StorSimple Snapshot si vedrà le azioni seguenti elencate in tutti i menu di azione e tutte le variazioni del riquadro **Azioni** . 

- Visualizzazione
- Nuova finestra da qui 
- L'aggiornamento 
- Elenco di esportazione 
- Guida 

Queste azioni fanno parte di Microsoft Management Console (MMC) e non sono specifiche di gestione di Snapshot di StorSimple. In questa esercitazione descrive queste azioni e viene spiegato come utilizzare ciascuno di essi in StorSimple Snapshot Manager.

## <a name="view"></a>Visualizzazione

È possibile utilizzare l'opzione di **visualizzazione** per modificare la visualizzazione del riquadro **risultati** e modificare la visualizzazione della finestra console. 

#### <a name="to-change-the-results-pane-view"></a>Per modificare la visualizzazione del riquadro risultati

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro **ambito** rapida qualsiasi nodo o espandere il nodo e rapida di un elemento nel riquadro **risultati** e quindi fare clic sull'opzione di **visualizzazione** . 

3. Per aggiungere o rimuovere le colonne visualizzate nel riquadro **dei risultati** , fare clic su **Aggiungi/Rimuovi colonne**. Verrà visualizzata la finestra di dialogo **Aggiungi/Rimuovi colonne** .

    ![Aggiungere o rimuovere colonne dal riquadro risultati](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 

4. Compilare il modulo nel modo seguente:

    - Selezionare gli elementi dall'elenco colonne **disponibili** e fare clic su **Aggiungi** per aggiungerli all'elenco di **colonne visualizzate** . 

    - Fare clic su elementi presenti nell'elenco **colonne visualizzate** e fare clic su **Rimuovi** per rimuoverli dall'elenco. 

    - Selezionare un elemento nell'elenco colonne **visualizzate** e fare clic su **Sposta su** o **Sposta giù** per spostare l'elemento verso l'alto o verso il basso nell'elenco. 

    - Fare clic su **Ripristina** per tornare alla configurazione riquadro **dei risultati** predefinita. 

5. Dopo aver terminato tutte le opzioni desiderate, fare clic su **OK**. 

#### <a name="to-change-the-console-window-view"></a>Per modificare la visualizzazione della finestra console

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro **ambito** rapida qualsiasi nodo, fare clic su **Visualizza**e quindi fare clic su **Personalizza**. Verrà visualizzata la finestra di dialogo **Personalizza** .

    ![Personalizzare la finestra di console](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 

3. Selezionare o deselezionare le caselle di controllo per mostrare o nascondere gli elementi nella finestra della console. Dopo aver terminato tutte le opzioni desiderate, fare clic su **OK**.

## <a name="new-window-from-here"></a>Nuova finestra da qui

È possibile utilizzare l'opzione **Nuova finestra da qui** per aprire una finestra console.

#### <a name="to-open-a-new-console-window"></a>Per aprire una finestra console

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro **ambito** destro nodi e quindi fare clic su **Nuova finestra da qui**. 

    Una nuova finestra verrà visualizzata solo l'ambito di selezionato. Ad esempio, se si rapida nodo **Criteri di Backup** , la nuova finestra verrà visualizzati solo il nodo **Criteri di Backup** nel riquadro **dell'ambito** e un elenco di criteri di backup definiti nel riquadro **risultati** . Vedere nell'esempio seguente.

    ![Nuova finestra da qui](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 
 
## <a name="refresh"></a>L'aggiornamento

È possibile usare l'azione **Aggiorna** per aggiornare la finestra della console.

#### <a name="to-update-the-console-window"></a>Per aggiornare la finestra della console

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro **ambito** rapida qualsiasi nodo o espandere il nodo e rapida di un elemento nel riquadro **risultati** e quindi fare clic su **Aggiorna**. 

## <a name="export-list"></a>Elenco di esportazione

È possibile utilizzare l'azione **Esporta elenco** per salvare un elenco in un file con valori delimitati da virgole (CSV). Ad esempio, è possibile esportare l'elenco dei criteri di backup o il catalogo di backup. È quindi possibile importare il file CSV in un'applicazione foglio di calcolo per l'analisi.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Per salvare un elenco in un file con valori delimitati da virgole (CSV)

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple. 

2. Nel riquadro **ambito** rapida nodi o espandere il nodo e rapida di un elemento nel riquadro **risultati** e quindi fare clic su **Esporta elenco**. 

3. Verrà visualizzata la finestra di dialogo **Esporta elenco** . Compilare il modulo nel modo seguente: 

    1. Nella casella **Nome File** digitare un nome per il file CSV oppure fare clic sulla freccia per selezionare dall'elenco a discesa.

    2. Nella casella **tipo file** fare clic sulla freccia e selezionare un tipo di file dall'elenco a discesa.

    3. Per salvare solo gli elementi selezionati, selezionare le righe e quindi fare clic sulla casella di controllo **Salva solo le righe selezionate** . Per salvare gli elenchi tutti esportati, deselezionare la casella di controllo **Salva solo le righe selezionate** .

    4. Fare clic su **Salva**.

    ![Elenco di esportazione come file con valori delimitati da virgole](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 
 
## <a name="help"></a>Guida

È possibile utilizzare il menu **della Guida** per visualizzare la Guida in linea disponibile per StorSimple Snapshot Manager e MMC.

#### <a name="to-view-available-online-help"></a>Visualizzare la Guida in linea disponibile

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro di **ambito** rapida qualsiasi nodo o espandere il nodo e rapida di un elemento nel riquadro **risultati** e quindi fare clic su **Guida**. 

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull' [interfaccia utente StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
- Ulteriori informazioni [sull'utilizzo di gestione di Snapshot StorSimple per amministrare la soluzione StorSimple](storsimple-snapshot-manager-admin.md).
