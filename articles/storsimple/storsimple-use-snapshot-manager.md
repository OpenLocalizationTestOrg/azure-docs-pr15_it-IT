<properties 
   pageTitle="Interfaccia utente di gestione di Snapshot StorSimple | Microsoft Azure"
   description="Descrive l'interfaccia utente StorSimple Snapshot Manager e viene spiegato come utilizzare per gestire processi di backup e il catalogo di backup."
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

# <a name="storsimple-snapshot-manager-user-interface"></a>Interfaccia utente StorSimple Snapshot Manager

## <a name="overview"></a>Panoramica

Il responsabile di Snapshot StorSimple ha un'interfaccia utente intuitiva che è possibile utilizzare per creare e gestire i backup. In questa esercitazione viene fornita un'introduzione all'interfaccia utente e quindi viene illustrato come utilizzare ognuno dei componenti. Per una descrizione dettagliata di gestione Snapshot StorSimple, vedere [che cos'è Gestione Snapshot StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Descrizione console

Per visualizzare l'interfaccia utente, fare clic sull'icona StorSimple Snapshot Manager sul desktop. Viene visualizzata la finestra di console, come illustrato nella figura seguente.

![Riquadri StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Nella finestra di console sono cinque elementi principali. Fare clic sul collegamento appropriato per una descrizione completa di ogni elemento.

- [Barra dei menu](#menu-bar) 
- [Barra degli strumenti](#tool-bar) 
- [Riquadro dell'ambito](#scope-pane) 
- [Riquadro risultati](#results-pane) 
- [Nel riquadro azioni](#actions-pane) 

Inoltre, il gestore di Snapshot StorSimple supporta [la navigazione da tastiera e un numero di scelta rapida](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Console accessibilità

Nell'interfaccia utente StorSimple Snapshot Manager supporta la funzionalità di accessibilità fornite dal sistema operativo Windows e Microsoft Management Console (MMC), nonché alcune scelte rapide da tastiera specifici StorSimple Snapshot Manager. 

- Per una descrizione delle caratteristiche di accessibilità di Windows, vedere [scelte rapide da tastiera per Windows](https://support.microsoft.com/kb/126449). 

- Per una descrizione delle caratteristiche di accessibilità MMC, vedere per [l'accessibilità per MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)

- Per una descrizione delle caratteristiche di accessibilità StorSimple Snapshot Manager, passare ai [tasti di scelta rapida e di spostamento](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Barra dei menu

Barra dei menu nella parte superiore della finestra della console contiene [File](#file-menu), [azione](#action-menu), [visualizzazione](#view-menu), [Preferiti](#favorites-menu), [finestra](#window-menu)e dei menu [della Guida](#help-menu) .

Fare clic su qualsiasi elemento sulla barra dei menu per visualizzare un elenco dei comandi disponibili nel menu. Nell'esempio seguente mostra il menu **visualizzazione** selezionato nella barra dei menu.

![Menu visualizzazione selezionato](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menu file

Dal menu **File** comandi standard Microsoft Management Console (MMC).

#### <a name="menu-access"></a>Accesso al menu

Per visualizzare il menu **File** , fare clic su **File** sulla barra dei menu. Viene visualizzato il menu seguente.

![Menu File StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Descrizione del menu

Nella tabella seguente vengono illustrati gli elementi visualizzati nel menu **File** .

| Voce di menu | Descrizione |
|:----------|:-------------|
| Nuovo       | Fare clic su **Nuovo** per creare una nuova console basata su StorSimple Snapshot Manager. |
| Apri      | Fare clic su **Apri** per aprire una console esistente. |
| Salva      | Fare clic su **Salva** per salvare la console corrente. |
| Salva con nome   | Fare clic su **Salva con nome** per creare una nuova istanza rinominata dalla console corrente. Utilizzare l'opzione **Salva con nome** per personalizzare una visualizzazione e salvarla per poterlo recuperare. Ad esempio, è possibile creare snap-in Gestione di Snapshot StorSimple che puntino ai server specifico. |
| Aggiungi/Rimuovi Snap-in | Fare clic su **Aggiungi/Rimuovi Snap-in** per aggiungere o rimuovere snap-in e per organizzare nodi nel riquadro di **ambito** . Per ulteriori informazioni, passare a [aggiungere, rimuovere e organizza Snap-in ed estensioni in MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Opzioni   | Fare clic su **Opzioni** per modificare l'icona di console, specificare le autorizzazioni e la modalità di accesso utente o eliminare file console per aumentare lo spazio disponibile su disco. |
| Elenco di percorsi dei file | Fare clic su un percorso nell'elenco numerato per riaprire un file aperto di recente. |
| Uscita      | Fare clic su **Esci** per chiudere il menu **File** . |
 
### <a name="action-menu"></a>Menu Azioni

Usare il menu **Azioni** selezionare azioni disponibili. Gli elementi disponibili dipendono dalla selezione effettuata nel riquadro **ambito** o nel riquadro **risultati** .

#### <a name="menu-access"></a>Accesso al menu

Per visualizzare il menu **Azioni** , eseguire una delle operazioni seguenti:

- Fare clic su un elemento nel riquadro **ambito** o nel riquadro **risultati** .

- Selezionare un elemento nel riquadro **ambito** o nel riquadro **risultati** e quindi fare clic su **azione** sulla barra dei menu. 

Ad esempio, se si seleziona il livello di nodo principale nel riquadro di **ambito** e quindi pulsante destro del mouse o fare clic su **azione** nella barra dei menu, viene visualizzato il menu seguente.
 
![Menu azione StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

Il riquadro **Azioni** (a destra della console) contiene lo stesso elenco di azioni del menu **dell'azione** . Inoltre, il riquadro **Azioni** contiene le opzioni del menu **Visualizza** , che consentono di creare una visualizzazione personalizzata del riquadro **risultati** .

![Aprire il riquadro azioni con dal menu Visualizza](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Descrizione del menu

Nella tabella seguente contiene un elenco in ordine alfabetico di azioni StorSimple Snapshot Manager. 

- La colonna **azione** Elenca le azioni che è possibile eseguire su nodi e i risultati. 

- La colonna di **spostamento** viene illustrato come visualizzare il menu **azione** appropriato, in modo che sia possibile selezionare l'azione. Alcune azioni vengono visualizzate nel menu **Azioni** multipli. Per le azioni, selezionare un'opzione di **spostamento** nell'elenco puntato. 

- Colonna **Descrizione** viene descritto come utilizzare ogni azione del menu di **azione** o nel riquadro azioni e spiega cosa.

>[AZURE.NOTE] Il riquadro **Azioni** e menu **azione** contengono ulteriori opzioni, ad esempio **visualizzazione** **Nuova finestra da qui**, **aggiornare**, **Esporta elenco**e **Guida**. Queste opzioni sono disponibili come parte di MMC e non sono specifiche di gestione di Snapshot di StorSimple. La tabella include le descrizioni delle opzioni seguenti.
 
| Azione  | Struttura di spostamento  | Descrizione  |
|:--------|:------------|:-------------|
| Eseguire l'autenticazione | Fare clic sul nodo **dispositivi** e rapida un dispositivo nel riquadro **risultati** . | Fare clic su **autentica** per immettere la password è configurato per il dispositivo. |
| Duplicato  | Espandere **Catalogo di Backup**, espandere **Cloud snapshot**, fare clic su una copia di backup specificato e quindi selezionare un volume nel riquadro dei **risultati** . | Fare clic su **duplicato** per creare una copia di uno snapshot cloud e archiviarlo in una posizione in cui è possibile designare. |
| Configurare un dispositivo | Pulsante destro del mouse del nodo di **dispositivi** . | Fare clic su **configurare un dispositivo** per configurare uno o più dispositivi per connettersi a Windows host singola. |
| Creare criteri di Backup | Eseguire una delle operazioni seguenti:<ul><li>Fare clic su **criteri di Backup**.</li><li>Fare clic su o espandere **I gruppi di Volume**e fare clic su un gruppo di volume.</li><li>Fare clic su o espandere **Catalogo di Backup**e fare clic su un gruppo di volume.</li></ul> | Fare clic su **Crea criterio di Backup** per configurare un backup pianificato per un gruppo di volume. |
| Creare il gruppo di Volume | Eseguire una delle operazioni seguenti:<ul><li>Fare clic sul nodo **volumi** e quindi rapida un volume nel riquadro **risultati** .</li><li>Pulsante destro del mouse il nodo **Gruppi Volume** .</li></ul> | Fare clic su **Crea gruppo Volume** per assegnare volumi a un gruppo di volume. |
| Elimina | Fare clic su un nodo o un risultato (questo elemento appare nella molti menu **Azioni** e riquadri **Azioni** ). | Fare clic su **Elimina** per eliminare il nodo o il risultato è stato selezionato. Quando viene visualizzata la finestra di dialogo di conferma, confermare o annullare l'eliminazione. |
| Dettagli | Fare clic sul nodo **dispositivi** e fare clic su un dispositivo nel riquadro **risultati** . | Fare clic su **Dettagli** per visualizzare i dettagli di configurazione per un dispositivo. |
| Modifica | Fare clic su **Criteri di Backup**e quindi rapida un criterio nel riquadro **risultati** . | Fare clic su **Modifica** per modificare la pianificazione di backup per un gruppo di volume. |
| Elenco di esportazione | Fare clic su qualsiasi nodo o risultato (questo elemento viene visualizzata in tutti i menu **Azioni** e riquadri **Azioni** ). | Fare clic su **Esporta elenco** per salvare un elenco in un file con valori delimitati da virgole (CSV). È quindi possibile importare il file in un'applicazione foglio di calcolo per l'analisi. |
| Guida | Fare clic su qualsiasi nodo o il risultato. (Questo elemento viene visualizzata in tutti i menu **Azioni** e riquadri **Azioni** ). | Fare clic su **Guida** per aprire la Guida in linea in una finestra separata del browser. |
| Nuova finestra da qui | Fare clic su qualsiasi nodo o risultato (questo elemento viene visualizzata in tutti i menu **Azioni** e riquadri **Azioni** ). | Fare clic su **Nuova finestra da qui** per aprire una nuova finestra StorSimple Snapshot Manager.|
| L'aggiornamento | Fare clic su qualsiasi nodo o risultato (questo elemento viene visualizzata in tutti i menu **Azioni** e riquadri **Azioni** ). | Fare clic su **Aggiorna** per aggiornare la finestra di gestione di Snapshot StorSimple attualmente visualizzata. |
| Aggiornare i dispositivi | Fare clic sul nodo **dispositivi** e rapida un dispositivo nel riquadro **risultati** . | Fare clic su **Aggiorna dispositivo** per sincronizzare un dispositivo connesso specifico con StorSimple Snapshot Manager. |
| Aggiornare i dispositivi | Pulsante destro del mouse del nodo di **dispositivi** . | Fare clic su **Aggiorna dispositivi** per sincronizzare l'elenco dei dispositivi collegati con StorSimple Snapshot Manager. |
| Ripetere l'analisi di volumi | Pulsante destro del mouse del nodo **volumi** . | Fare clic su **Ripeti analisi volumi** per aggiornare l'elenco di volumi che viene visualizzata nel riquadro **risultati** . |
| Ripristinare | Espandere **Catalogo di Backup**, espandere un gruppo di volume, espandere **Istantanee locale** o **Istantanee Cloud**e fare clic su una copia di backup. | Fare clic su **Ripristina** per sostituire i dati del gruppo volume corrente con i dati di backup selezionato. |
| Eseguire il Backup | Eseguire una delle operazioni seguenti:<ul><li>Espandere **I gruppi di Volume**e fare clic su un gruppo di volume.</li><li>Espandere **Catalogo di Backup**e fare clic su un gruppo di volume.</li></ul> | Fare clic su **Eseguire Backup** per avviare un processo di backup immediatamente. |
| Attivare/disattivare importazioni visualizzazione | Fare clic sul livello di nodo principale nel riquadro di **ambito** (il nodo **StorSimple Snapshot Manager** negli esempi). | Fare clic su **Mostra/Nascondi importazioni visualizzazione** per mostrare o nascondere i gruppi di volume e backup associato che sono stati importati nel dashboard di servizio StorSimple Manager. |

### <a name="view-menu"></a>Menu Visualizza

Utilizzare il menu **Visualizza** per creare una visualizzazione personalizzata dei contenuti del riquadro **risultati** . Dal menu **Visualizza** contiene le opzioni di **Aggiungi/Rimuovi colonne** e **personalizzazione** .

#### <a name="menu-access"></a>Accesso al menu

È possibile accedere dal menu **Visualizza** sulla barra dei menu o nel riquadro **Azioni** .

![Menu Visualizza StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Descrizione del menu

Nella tabella seguente vengono illustrati gli elementi visualizzati nel menu **Visualizza** .

| Voce di menu  | Descrizione |
|:-----------|:-------------|
| Aggiungi/Rimuovi colonne | Fare clic su **Aggiungi/Rimuovi colonne** per aggiungere o rimuovere colonne nel riquadro **risultati** . |
| Personalizza | Fare clic su **Personalizza** per mostrare o nascondere gli elementi nella finestra della console StorSimple Snapshot Manager. |

### <a name="favorites-menu"></a>Menu Preferiti

Usare il menu **Preferiti** per aggiungere, rimuovere e organizzare le visualizzazioni di pagina e le attività che si usano di frequente. 

#### <a name="menu-access"></a>Accesso al menu

È possibile accedere al menu **Preferiti** sulla barra dei menu.

![Menu StorSimple Snapshot Manager Preferiti](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Descrizione del menu

Nella tabella seguente vengono illustrati gli elementi visualizzati nel menu **Preferiti** .

| Voce di menu |  Descrizione |
|:----------|:-------------|
| Aggiungi a Preferiti | Fare clic su **Aggiungi a Preferiti** per aggiungere la visualizzazione corrente all'elenco Preferiti. |
| Organizzare i Preferiti | Fare clic su **Organizza Preferiti** per organizzare il contenuto della cartella Preferiti. |

### <a name="window-menu"></a>Menu della finestra

Per aggiungere e ridisporre windows console StorSimple Snapshot Manager, utilizzare il menu della **finestra** .

#### <a name="menu-access"></a>Accesso al menu

È possibile accedere al menu **finestra** sulla barra dei menu.

![Menu finestra StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

L'elenco numerato nella parte inferiore del menu Mostra finestre attualmente aperte. Fare clic su tutte le finestre in tale elenco per visualizzare la finestra in primo piano. 

#### <a name="menu-description"></a>Descrizione del menu

Nella tabella seguente vengono illustrati gli elementi visualizzati nel menu finestra.

| Voce di menu  | Descrizione |
|:-----------|:-------------|
| Nuova finestra | Fare clic su **Nuova finestra** per aprire una nuova finestra console (oltre alle finestra esistente). |
| Eliminazione a catena   | Fare clic su **a catena** per visualizzare le finestre aperte console di stile CSS. |
| Affiancare orizzontalmente | Fare clic su **Affiancare orizzontalmente** per visualizzare le finestre di console aperta in un formato riquadro (o griglia). |
| Disporre le icone | Se si dispone di più console di windows aprire e distribuiti in desktop, ridurre a icona loro e quindi fare clic su **Disponi icone** per disporle in una riga orizzontale nella parte inferiore dello schermo. |

### <a name="help-menu"></a>Dal menu?

Utilizzare il menu **della Guida** per visualizzare la Guida in linea disponibile per StorSimple Snapshot Manager e MMC. È anche possibile visualizzare informazioni sulle versioni software MMC e gestione di Snapshot StorSimple attualmente installati nel sistema. 

Per visualizzare il menu **della Guida** sulla barra dei menu. È anche possibile accedere gli argomenti della Guida StorSimple Snapshot Manager nel riquadro **Azioni** .

![Dal menu aiuto StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Descrizione del menu

Nella tabella seguente vengono illustrati gli elementi visualizzati dal menu?.

| Voce di menu  | Descrizione  |
|:-----------|:-------------|
| Guida relativa Snapshot StorSimple Manager | Fare clic **su StorSimple Snapshot gestione della Guida** per aprire la Guida di gestione di Snapshot StorSimple in una finestra separata. |
| Argomenti della Guida |Fare clic su **argomenti della Guida** per aprire la Guida in linea di MMC in una finestra separata. |
| Sito Web TechCenter | Fare clic su **sito Web TechCenter** per aprire la home page di Microsoft TechNet Tech Center in una finestra separata. |
| Informazioni su Microsoft Management Console | Fare clic **Su Microsoft Management Console** per visualizzare la versione di Microsoft Management Console installata nel sistema. |
| Informazioni su StorSimple Snapshot Manager | Fare clic su **Gestione Snapshot StorSimple** per visualizzare la versione di snap-in è installata nel sistema. |

## <a name="tool-bar"></a>Barra degli strumenti

Barra degli strumenti, sotto la barra dei menu, contiene le icone di spostamento e attività. Ogni icona è un collegamento a un'attività specifica.

### <a name="icon-descriptions"></a>Descrizioni delle icone

Nella tabella seguente descrive le icone visualizzate nella barra degli strumenti. 

| Icona  | Descrizione  |
|:------|:-------------| 
| ![Freccia sinistra](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) | Fare clic sull'icona freccia sinistra per tornare alla pagina precedente. |
| ![Freccia destra](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) | Fare clic sulla freccia destra per passare alla pagina successiva (se la freccia grigio, l'azione è disponibile). |
| ![Icona Sposta su](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) | Fare clic sull'icona rapidamente per passare al superiore livello nella struttura della console (riquadro **obiettivi globali** ). |
| ![Mostra/Nascondi struttura console](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) | Fai clic sull'icona Mostra/Nascondi struttura console per mostrare o nascondere il riquadro di **ambito** . |
| ![Elenco di esportazione](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) | Fare clic sull'icona di elenco Esporta per esportare un elenco in un file CSV specificato. |
| ![Icona della Guida](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png)  |Fare clic sull'icona della Guida per aprire un argomento della Guida MMC online. |
| ![Nel riquadro azioni Mostra/Nascondi](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) | Fare clic su Mostra/Nascondi icona nel riquadro **Azioni** per mostrare o nascondere il riquadro **Azioni** . 
 
## <a name="scope-pane"></a>Riquadro dell'ambito

Riquadro **dell'ambito** è il riquadro all'estrema sinistra in UI StorSimple Snapshot Manager. Contiene l'albero console (o nodo) e il meccanismo di spostamento principale per StorSimple Snapshot Manager. 
 
### <a name="scope-pane-structure"></a>Struttura del riquadro di ambito

Riquadro **dell'ambito** contiene una serie di oggetti selezionabile (nodi) organizzata in una struttura ad albero. 

![Riquadro dell'ambito](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

- Per espandere o comprimere un nodo, fare clic sull'icona freccia accanto al nome del nodo.

- Per visualizzare lo stato o il contenuto di un nodo, fare clic sul nome di nodo. Le informazioni visualizzate nel riquadro **risultati** . 

Riquadro **dell'ambito** contiene i nodi seguenti: 

- [Nodo dispositivi](#devices-node) 
- [Nodo volumi](#volumes-node) 
- [Nodo gruppi volume](#volume-groups-node) 
- [Eseguire il backup nodo Criteri](#backup-policies-node) 
- [Eseguire il backup nodo del catalogo](#backup-catalog-node) 
- [Nodo processi](#jobs-node) 

### <a name="scope-pane-tasks"></a>Attività riquadro ambito

È possibile utilizzare il riquadro **ambito** per completare un'azione su un nodo specifico. Per selezionare un'attività, eseguire una delle operazioni seguenti:

- Pulsante destro del mouse il nodo e quindi selezionare l'attività dal menu visualizzato.

- Fare clic sul nodo e quindi fare clic su **azione** sulla barra dei menu. Selezionare l'attività dal menu visualizzato.

- Fare clic sul nodo e quindi selezionare l'azione nel riquadro **Azioni** .

Quando si seleziona un nodo e utilizzare uno dei metodi seguenti per visualizzare un elenco di attività, vengono visualizzate solo le azioni che è possono eseguire su tale nodo.

### <a name="devices-node"></a>Nodo dispositivi

Il nodo **dispositivi** rappresenta il StorSimple periferiche e StorSimple virtuale che si è connessi a StorSimple Snapshot Manager. Selezionare il nodo per connettere e configurare un dispositivo e importare i volumi associati, i gruppi di volumi e copie di backup esistenti. Più dispositivi possono essere connesso a un singolo host.

- Per espandere il nodo, fare clic sull'icona della freccia accanto a **dispositivi**.

- Per visualizzare un menu delle azioni disponibili, rapida nodo **dispositivi** o rapida uno qualsiasi dei nodi visualizzati in visualizzazione espansa.

- Per visualizzare un elenco dei dispositivi configurati, fare clic su **dispositivi** nel riquadro di **ambito** . Nel riquadro **risultati** viene visualizzato l'elenco dei dispositivi, insieme a informazioni su ciascun dispositivo.

### <a name="volumes-node"></a>Nodo volumi

Il nodo **volumi** rappresenta le unità che corrispondono ai volumi installati dall'host, compresi quelli scoperti tramite iSCSI e quelli scoperti tramite un dispositivo. Utilizzare questo nodo per visualizzare l'elenco di volumi disponibili e assegnare singoli volumi ai gruppi di volume.

- Per espandere il nodo, fare clic sull'icona della freccia accanto a **volumi**.

- Per visualizzare un menu delle azioni disponibili, rapida del nodo **volumi** o rapida uno qualsiasi dei nodi visualizzati in visualizzazione espansa.

- Per visualizzare un elenco di volumi, fare clic su **volumi** nel riquadro di **ambito** . Nel riquadro **risultati** viene visualizzato l'elenco di volumi, insieme a informazioni su ogni volume.

### <a name="volume-groups-node"></a>Nodo gruppi volume

Gruppi di volume sono noto anche come gruppi coerenza. Ogni gruppo volume è un pool di volumi relativi all'applicazione che consente di garantire la coerenza applicazione durante le operazioni di backup. Utilizzare il nodo **Gruppi Volume** di configurare i gruppi e di eseguire backup interattivi o creare le pianificazioni di backup. 

- Per espandere il nodo, fare clic sull'icona freccia accanto a **Volume gruppi**.

- Per visualizzare un menu delle azioni disponibili, rapida il nodo **Gruppi Volume** o rapida uno qualsiasi dei nodi visualizzati in visualizzazione espansa.

- Per visualizzare un elenco di gruppi di volume, fare clic su **Volume gruppi** nel riquadro di **ambito** . Nel riquadro **risultati** viene visualizzato l'elenco dei gruppi di volume, insieme a informazioni su ogni gruppo volume.

### <a name="backup-policies-node"></a>Eseguire il backup nodo Criteri

Criteri di backup sono pianificazioni per locali e cloud istantanee. Utilizzare il nodo **Criteri di Backup** per specificare la frequenza di creazione di una copia di backup e il periodo di tempo una copia di backup deve essere mantenuti. 

- Per espandere il nodo, fare clic sull'icona della freccia accanto a **Criteri di Backup**.

- Per visualizzare un menu delle azioni disponibili, rapida il nodo **Criteri di Backup** oppure fare clic uno qualsiasi dei nodi visualizzati in visualizzazione espansa.

- Per visualizzare un elenco dei criteri di backup, fare clic su **Criteri di Backup** nel riquadro di **ambito** . L'elenco dei criteri di backup, insieme a informazioni sui diversi criteri, verrà visualizzata nel riquadro **risultati** .

>[AZURE.NOTE] È possibile mantenere un massimo di 64 backup.


### <a name="backup-catalog-node"></a>Eseguire il backup nodo del catalogo

Il nodo **Catalogo di Backup** contiene elenchi di backup sul posto e all'esterno di Azure StorSimple volumi. Questo nodo è raggruppato a volume e ogni contenitore gruppo volume contiene strutture separate per istantanee locale (il nodo s **Snapshot locale**) e snapshot cloud (il nodo **Snapshot Cloud** ). Quando è visualizzata, ogni contenitore gruppo volume sono elencati tutti i backup completati vengono eseguiti in modo interattivo o da un criterio configurato.

- Per espandere il nodo, fare clic sull'icona freccia accanto a **Catalogo di Backup**.

- Per visualizzare un menu delle azioni disponibili, rapida nodo **Catalogo di Backup** oppure fare clic uno qualsiasi dei nodi visualizzati in visualizzazione espansa.

- Per visualizzare un elenco di copie di backup, fare clic su **Backup catalogo** nel riquadro di **ambito** . Nel riquadro **risultati** viene visualizzato l'elenco di istantanee, insieme a informazioni su ogni snapshot.

### <a name="local-snapshots-node"></a>Nodo snapshot locale

Il nodo **Locale istantanee** Elenca snapshot locale per un gruppo di volume specifico. Il nodo si trova sotto il nodo **Backup catalogo** nel riquadro di **ambito** . Snapshot locale sono in un momento copie dei dati di volume che sono archiviate nel dispositivo StorSimple Azure. In genere, è possibile creare e ripristinato rapidamente questo tipo di backup. Come si farebbe con una copia di backup locale, è possibile usare uno snapshot locale.

- Per espandere il nodo, fare clic sull'icona freccia accanto a **Snapshot locale**.

- Per visualizzare un menu delle azioni disponibili, rapida nodo **Istantanee locale** o rapida uno qualsiasi dei nodi visualizzati in visualizzazione espansa.

- Per visualizzare un elenco di istantanee locale, fare clic su **Snapshot locale** nel riquadro di **ambito** . Nel riquadro **risultati** viene visualizzato l'elenco di istantanee, insieme a informazioni su ogni snapshot.

### <a name="cloud-snapshots-node"></a>Nodo snapshot cloud

Il nodo **Snapshot Cloud** Elenca snapshot cloud per un gruppo di volume specifico. Il nodo si trova sotto il nodo **Backup catalogo** nel riquadro di **ambito** . Snapshot cloud sono in un momento copie di dati del volume archiviati nel cloud. Uno snapshot cloud corrisponde a uno snapshot replicato in un sistema di archiviazione diverso, fuori sede. Snapshot cloud sono particolarmente utili di emergenza.

- Per espandere il nodo, fare clic sull'icona della freccia accanto a **Snapshot Cloud**.

- Per visualizzare un menu delle azioni disponibili, rapida nodo **Snapshot Cloud** oppure fare clic uno qualsiasi dei nodi visualizzati in visualizzazione espansa.

- Per visualizzare un elenco di istantanee cloud, fare clic su **Cloud snapshot** nel riquadro di **ambito** . Nel riquadro **risultati** viene visualizzato l'elenco di istantanee, insieme a informazioni su ogni snapshot.

### <a name="jobs-node"></a>Nodo processi

Il nodo **processi** contiene informazioni sui processi di backup pianificati, eseguire e completati recentemente. 

- Per espandere il nodo, fare clic sull'icona della freccia accanto a **processi**.

- Per visualizzare un menu delle azioni disponibili, il nodo **processi** rapida oppure fare clic uno qualsiasi dei nodi visualizzati in visualizzazione espansa.

- Per visualizzare un elenco dei processi pianificati, espandere il nodo **processi** e quindi fare clic su **pianificazione**. Nel riquadro **risultati** viene visualizzato l'elenco dei processi configurati in precedenza e informazioni su ogni processo. 

- Per visualizzare un elenco dei processi completati recentemente, espandere il nodo **processi** e quindi fare clic su **ultime 24 ore**. Nel riquadro **risultati** viene visualizzato un elenco di processi che sono state completate nelle ultime 24 ore. Riquadro dei **risultati** contiene anche informazioni su ogni processo completato.

- Per visualizzare un elenco dei processi attualmente in esecuzione, espandere il nodo **processi** e quindi fare clic su **esecuzione**. Nel riquadro **risultati** viene visualizzato l'elenco dei attualmente in esecuzione processi e informazioni su ogni processo.

## <a name="results-pane"></a>Riquadro risultati

Il riquadro **risultati** è nel riquadro centrale UI StorSimple Snapshot Manager. Contiene elenchi e informazioni dettagliate sullo stato del nodo selezionato nel riquadro **dell'ambito** .

### <a name="example"></a>Esempio

Nell'esempio seguente, scegliere il nodo **Volume gruppi** nel riquadro di **ambito** . Riquadro **risultati** Visualizza un elenco di gruppi di volume con informazioni dettagliate su ogni gruppo.

![Riquadro risultati](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

È possibile configurare i dettagli visualizzati nel riquadro **risultati** : un nodo nel **riquadro** destro, fare clic su **Visualizza**e quindi fare clic su **Aggiungi/Rimuovi colonne**.

## <a name="actions-pane"></a>Nel riquadro azioni

Il riquadro **Azioni** è il riquadro destro UI StorSimple Snapshot Manager. La presentazione contiene un menu delle operazioni che è possibile eseguire per il nodo, vista o dati selezionati nel riquadro **ambito** o nel riquadro **risultati** . Nel riquadro **Azioni** sono gli stessi comandi i menu di **azione** sono disponibili per gli elementi nel riquadro **ambito** e nel riquadro **risultati** . Per una descrizione di ogni azione, vedere la tabella nella sezione dal menu **Azioni** .

### <a name="examples"></a>Esempi

Per vedere nell'esempio seguente, nel riquadro di **ambito** , espandere il nodo **processi** e fare clic su **pianificato**. Nel riquadro **Azioni** consente di visualizzare le azioni disponibili per il nodo **pianificato** .

![Esempio di programmate nel riquadro azioni](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Per visualizzare altre opzioni, nel riquadro di **ambito** , espandere il nodo **processi** , fare clic su **pianificato**e quindi fare clic su un processo pianificato nel riquadro **risultati** . Nel riquadro **Azioni** consente di visualizzare le azioni disponibili per il processo pianificato, come illustrato nell'esempio seguente.

![Esempio di azioni nel riquadro azioni processo](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Tasti di scelta rapida e di spostamento

StorSimple Snapshot Manager consente la funzionalità di accessibilità del sistema operativo Windows e Microsoft Management Console (MMC). Inoltre, include alcune funzionalità di spostamento di tastiera e scelta rapida specifici per il gestore di Snapshot di StorSimple, come descritto nelle sezioni seguenti.
 
- [Tasti di spostamento](#keyboard-navigation-keys) 
- [Menu barra tasti di scelta rapida](#menu-bar-shortcut-keys) 
- [Tasti di scelta rapida del riquadro di ambito](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Tasti di spostamento

Nella tabella seguente descrive i tasti che è possibile utilizzare per esplorare l'interfaccia utente StorSimple Snapshot Manager. 

| Tasto di spostamento  | Azione  |
|:----------------|:--------| 
| Tasto freccia giù | Usare il tasto freccia giù per spostarsi in senso verticale all'elemento successivo in un menu o un riquadro. |
| Immettere | Premere il tasto INVIO per completare un'azione e quindi procedere con il passaggio successivo. Ad esempio, è possibile premere INVIO per selezionare **Avanti**, **OK**o **Crea**e quindi passare alla sezione successiva in una procedura guidata.|
| ESC | Premere ESC per chiudere un menu o per annullare e chiudere una pagina.|
| F1 | Premere F1 per visualizzare un argomento della Guida per la finestra attiva.|
| F5 | Premere F5 per aggiornare un nodo. |
| F6 | Premere F6 per passare dal riquadro **dell'ambito** nel riquadro **risultati** .|
| F10 | Premere il tasto F10 per passare alla barra dei menu. |
| Tasto freccia sinistra | Usare il tasto freccia sinistra per spostarsi in senso orizzontale da un'opzione di barra dei menu all'opzione precedente. Quando si sposta all'elemento precedente sulla barra dei menu, viene visualizzato il menu azione (o contesto) per l'elemento precedente. |
| Tasto freccia destra | Usare il tasto freccia destra per spostare orizzontalmente da una barra della voce di menu alla successiva. Quando si sposta all'elemento successivo sulla barra dei menu, viene visualizzato il menu azione (o contesto) per il nuovo elemento.
| Tasto TAB | Usare il tasto Tab per spostarsi al riquadro successivo nella console o nella casella di selezione o il testo successivo in una pagina. |
| Tasto freccia su | Utilizzare il tasto freccia su per spostare in senso verticale all'elemento precedente in un menu o un riquadro. |

### <a name="menu-bar-shortcut-keys"></a>Menu barra tasti di scelta rapida

Nella tabella seguente descrive le combinazioni di tasti di scelta rapida per la barra dei menu. Dopo che si premere i tasti di scelta rapida e viene visualizzato il menu, è possibile utilizzare i tasti di scelta rapida (tasti sottolineati nel menu). Per ulteriori informazioni sulla barra dei menu, passare alla [barra dei Menu](#menu-bar).

| Scelta rapida | Risultato                    | Tasto del menu di scelta rapida | Risultato          |
|:---------|:--------------------------|:------------------|:----------------|
| ALT + F    | Aprire il menu **File** .  | N | Verrà aperta una nuova istanza di console.   |
|          |                           | O | Apre la pagina di **Strumenti di amministrazione** . |
|          |                           | S | Salva la console StorSimple Snapshot Manager.|
|          |                           | RISPOSTE | Apre la pagina **Salva con nome** . |
|          |                           | M | Apre la pagina **Aggiungi/Rimuovi Snap-in** .|
|          |                           | P | Verrà visualizzata la pagina delle **Opzioni** . |
|          |                           | H | Verrà visualizzata la Guida in linea.|
| ALT + A    | Apre il menu **Azioni** .| Si | L'opzione di visualizzazione di importazione viene attivato e disattivato.|
|          |                           | W | Verrà aperta una nuova console StorSimple Snapshot Manager.|
|          |                           | F | Aggiorna la console StorSimple Snapshot Manager.|
|          |                           | G | Apre la pagina **Esporta elenco** . 
|          |                           | H | Verrà visualizzata la Guida in linea.|
| ALT + V    | Apre il menu **Visualizza** .  | RISPOSTE | Apre la pagina **Aggiungi/Rimuovi colonne** . |
|          |                           | U | Apre la pagina **Personalizza visualizzazione** . |
| ALT + O    | Apre il menu **Preferiti** . | RISPOSTE | Apre la pagina **Aggiungi a Preferiti** . |
|          |                           | O | Apre la pagina **Organizza Preferiti** .|
| ALT + W    | Apre il menu **finestra** .| N | Verrà visualizzata un'altra finestra StorSimple Snapshot Manager.|
|          |                           | C | Consente di visualizzare tutte le finestre aperte console di stile CSS.|
|          |                           | T | Visualizza tutte le finestre console aperta in una griglia. |
|          |                           | Si | Dispone le icone in una riga orizzontale nella parte inferiore dello schermo.|
| ALT + H    | Apre il menu **della Guida** .  | H | Verrà visualizzata la Guida in linea.|
|          |                           | T | Apre la pagina web di Microsoft TechNet Tech Center.|
|          |                           | RISPOSTE | Apre la pagina **Sulle Microsoft Management Console** . |
 
### <a name="scope-pane-shortcut-keys"></a>Tasti di scelta rapida del riquadro di ambito

Nella tabella seguente mostra il collegamento combinazioni di tasti per ogni nodo nel riquadro di **ambito** . 

- [Tasti di scelta rapida nodo dispositivi](#devices-node-shortcut-keys)
- [Tasti di scelta rapida nodo volumi](#volumes-node-shortcut-keys)
- [Tasti di scelta rapida nodo gruppi volume](#volume-groups-node-shortcut-keys)
- [Tasti di scelta rapida nodo Criteri backup](#backup-policies-node-shortcut-keys)
- [Tasti di scelta rapida nodo catalogo backup](#backup-catalog-node-shortcut-keys)
- [Tasti di scelta rapida nodo processi](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Tasti di scelta rapida nodo dispositivi

| Menu di scelta rapida | Risultato                               |
|:--------------|:-------------------------------------|
| C             | Verrà visualizzata la pagina **Configura un dispositivo** . |
| D             | Consente di aggiornare l'elenco dei dispositivi e dettagli dispositivo.|
| V             | Apre il menu **Visualizza** . |
| W             | Verrà visualizzata una nuova console di StorSimple Snapshot gestione incentrata su nodo **Dettagli** . |
| F             | Aggiorna la console StorSimple Snapshot Manager. |
| G             | Apre la pagina **Esporta elenco** . 
| H             | Verrà visualizzata la Guida in linea.|
 

#### <a name="volumes-node-shortcut-keys"></a>Tasti di scelta rapida nodo volumi

| Menu di scelta rapida   | Risultato                              |
|:----------------|:------------------------------------|
| V               | L'elenco di volumi viene aggiornato.        |
| V (premere due volte) | Apre il menu **Visualizza** .            |
| W               | Verrà aperta una nuova console StorSimple Snapshot gestione incentrata su nodo **volumi** .|
| F               | Aggiorna la console StorSimple Snapshot Manager.|
| G               | Apre la pagina **Esporta elenco** . 
| H               | Verrà visualizzata la Guida in linea.|
 
#### <a name="volume-groups-node-shortcut-keys"></a>Tasti di scelta rapida nodo gruppi volume

| Menu di scelta rapida   | Risultato                              |
|:----------------|:------------------------------------|
| G               | Verrà visualizzata la pagina **Crea un gruppo di Volume** . |
| V               | Apre il menu **Visualizza** . |
| W               | Apre una nuova console di gestione di Snapshot StorSimple attivando il nodo **Gruppi Volume** .|
| F               | Aggiorna la console StorSimple Snapshot Manager. |
| G               | Apre la pagina **Esporta elenco** . |
| H               | Verrà visualizzata la Guida in linea.|

#### <a name="backup-policies-node-shortcut-keys"></a>Tasti di scelta rapida nodo Criteri backup

| Menu di scelta rapida   | Risultato                              |
|:----------------|:------------------------------------|
| B               | Verrà visualizzata la pagina **Crea un criterio** . |
| V               | Apre il menu **Visualizza** .            |
| W               | Apre una nuova console di gestione di Snapshot StorSimple attivando il nodo **Gruppi Volume** .|
| F               | Aggiorna la console StorSimple Snapshot Manager.|
| G               | Apre la pagina **Esporta elenco **. 
| H               | Verrà visualizzata la Guida in linea.|
 
#### <a name="backup-catalog-node-shortcut-keys"></a>Tasti di scelta rapida nodo catalogo backup

| Menu di scelta rapida   | Risultato                              |
|:----------------|:------------------------------------|
| W               | Apre una nuova console di gestione di Snapshot StorSimple attivando il nodo **Gruppi Volume** . |
| F               | Aggiorna la console StorSimple Snapshot Manager. |
| H               | Verrà visualizzata la Guida in linea.|
 
#### <a name="jobs-node-shortcut-keys"></a>Tasti di scelta rapida nodo processi

| Menu di scelta rapida   | Risultato                              |
|:----------------|:------------------------------------|
| V               | Apre il menu **Visualizza** .            |
| W               | Verrà visualizzata una nuova console di StorSimple Snapshot gestione incentrata su nodo **processi** .|
| F               | Aggiorna la console StorSimple Snapshot Manager.|
| G               | Apre la pagina **Esporta elenco** .     |
| H               | Verrà visualizzata la Guida in linea                   |
 
## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [utilizzare Gestione Snapshot StorSimple per amministrare la soluzione StorSimple](storsimple-snapshot-manager-admin.md).
- Informazioni su come [utilizzare Gestione Snapshot StorSimple a connettersi e gestione di dispositivi](storsimple-snapshot-manager-manage-devices.md).
