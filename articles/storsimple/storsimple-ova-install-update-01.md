<properties 
   pageTitle="Installare gli aggiornamenti in una matrice virtuale StorSimple | Microsoft Azure"
   description="Viene descritto come utilizzare il web matrice virtuale StorSimple dell'interfaccia utente per applicare gli aggiornamenti utilizzando il metodo portale e l'aggiornamento rapido"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/07/2016"
   ms.author="alkohli" />

# <a name="install-updates-on-your-storsimple-virtual-array"></a>Installare gli aggiornamenti nella matrice virtuale StorSimple

## <a name="overview"></a>Panoramica

In questo articolo vengono illustrati i passaggi necessari per installare gli aggiornamenti nella matrice virtuale StorSimple tramite l'interfaccia web locale e tramite il portale classico Azure. È necessario applicare gli aggiornamenti software o le correzioni per mantenere sempre aggiornata la matrice virtuale StorSimple. 

Tenere presente che l'installazione di un aggiornamento o aggiornamento rapido riavvio del dispositivo. Dato che la matrice virtuale StorSimple è un dispositivo singolo nodo, viene interrotta qualsiasi i/o in corso il dispositivo esperienze e il tempo di inattività. 

Prima di applicare un aggiornamento, è consigliabile creare volumi o condivisioni non in linea nell'host prima e quindi il dispositivo. In questo modo la possibilità di danneggiamento dei dati.

> [AZURE.IMPORTANT] Se si esegue l'aggiornamento 0,1 o GA software versioni, è necessario utilizzare il metodo di aggiornamento rapido tramite l'interfaccia web locale per installare l'aggiornamento 0,3. Se si esegue l'aggiornamento 0,2, è consigliabile installare gli aggiornamenti tramite il portale classico Azure.

## <a name="use-the-local-web-ui"></a>Utilizzare l'interfaccia web locale 
 
Quando si utilizza l'interfaccia web locale sono disponibili due passaggi:

- Scaricare l'aggiornamento o l'aggiornamento rapido
- Installare l'aggiornamento o l'aggiornamento rapido

### <a name="download-the-update-or-the-hotfix"></a>Scaricare l'aggiornamento o l'aggiornamento rapido

Per scaricare l'aggiornamento software dal catalogo di Microsoft Update, procedere come segue.

#### <a name="to-download-the-update-or-the-hotfix"></a>Scaricare l'aggiornamento o l'aggiornamento rapido

1. Avviare Internet Explorer e passare a [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Se si tratta del primo utilizzo catalogo di Microsoft Update nel computer in uso, fare clic su **Installa** quando viene richiesto di installare il componente aggiuntivo catalogo Microsoft Update.
  
3. Nella casella di ricerca del catalogo di Microsoft Update, immettere il numero della Knowledge Base (KB) dell'aggiornamento rapido da scaricare. Immettere **3182061** aggiornamento 0,3 e quindi fare clic su **Cerca**.

    L'elenco di correzione viene visualizzata, ad esempio, **StorSimple virtuali in forma di matrice aggiornamento 0,3**.

    ![Catalogo di ricerca](./media/storsimple-ova-install-update-01/download1.png)

4. Fare clic su **Aggiungi**. L'aggiornamento viene aggiunto al carrello.

5. Fare clic su **Visualizza carrello**.

6. Fare clic su **Scarica**. Specificare o **passare** a una posizione locale in cui si desidera scaricare vengono visualizzati. Gli aggiornamenti vengono scaricati nella posizione specificata e inseriti in una sottocartella con lo stesso nome dell'aggiornamento. La cartella può inoltre essere copiata in una condivisione di rete che sia raggiungibile dal dispositivo.

7. Aprire la cartella copiata, verrà visualizzato un file di pacchetto autonomo Microsoft Update `WindowsTH-KB3011067-x64`. Il file viene utilizzato per installare l'aggiornamento o aggiornamento rapido.


### <a name="install-the-update-or-the-hotfix"></a>Installare l'aggiornamento o l'aggiornamento rapido

Prima dell'installazione di aggiornamento o aggiornamento rapido, accertarsi che si dispone dell'aggiornamento o la correzione scaricato localmente sull'host o accessibile tramite una condivisione di rete. 

Utilizzare questo metodo per installare gli aggiornamenti in un dispositivo che esegue GA o aggiornare 0,1 versioni del software. Questa procedura consente di accedere inferiore a 2 minuti. Eseguire la procedura seguente per installare l'aggiornamento o aggiornamento rapido.


#### <a name="to-install-the-update-or-the-hotfix"></a>Per installare l'aggiornamento o l'aggiornamento rapido

1. Nell'interfaccia utente di web locale, passare a **manutenzione** > **Aggiornamento Software**.

    ![dispositivo di aggiornamento](./media/storsimple-ova-install-update-01/update1m.png)

2. In **Aggiorna percorso del file**, immettere il nome del file per l'aggiornamento o l'aggiornamento rapido. È anche possibile passare al file di installazione di aggiornamento o aggiornamento rapido se inserito in una condivisione di rete. Fare clic su **Applica**.

    ![dispositivo di aggiornamento](./media/storsimple-ova-install-update-01/update2m.png)

3.  Verrà visualizzato un avviso. Dato questo è un dispositivo singolo nodo dopo l'aggiornamento viene applicato, riavvia il dispositivo e c'è tempo di inattività. Fare clic sull'icona di controllo.

    ![dispositivo di aggiornamento](./media/storsimple-ova-install-update-01/update3m.png)

4. Verrà avviata l'aggiornamento. Dopo il dispositivo viene aggiornato correttamente, riavvia. L'interfaccia utente locale non sono accessibile in questa durata.

    ![dispositivo di aggiornamento](./media/storsimple-ova-install-update-01/update5m.png)

5. Dopo avere inserito il riavvio, si passa alla pagina di **accesso** . Per verificare che il software è aggiornato, in web locale dell'interfaccia utente, passare a **manutenzione** > **Aggiornamento Software**. La versione del software visualizzato dovrebbe essere **10.0.0.0.0.10288.0** aggiornamento 0,3.

    > [AZURE.NOTE] Verranno segnalate le versioni di software in modo leggermente diverso in web locale dell'interfaccia utente e il portale classico Azure. Ad esempio, l'interfaccia web locale report **10.0.0.0.0.10288** classica nel portale di Azure rapporti e **10.0.10288.0** per la stessa versione. 

    ![dispositivo di aggiornamento](./media/storsimple-ova-install-update-01/update6m.png)





## <a name="use-the-azure-classic-portal"></a>Usare il portale classico Azure

Se è in esecuzione aggiornamento 0,2, è consigliabile installare gli aggiornamenti tramite il portale classico Azure. La procedura portale richiede all'utente di analisi, scaricare e installare gli aggiornamenti. Questa procedura consente di accedere circa 7 minuti per completare. Eseguire la procedura seguente per installare l'aggiornamento o aggiornamento rapido.

[AZURE.INCLUDE [storsimple-ova-install-update-via-portal](../../includes/storsimple-ova-install-update-via-portal.md)]

Al termine dell'installazione (come indicato in base allo stato di processo al 100%), passare a **dispositivi > manutenzione dei servizi > aggiornamenti Software**. La versione del software visualizzato dovrebbe essere 10.0.10288.0.

![dispositivo di aggiornamento](./media/storsimple-ova-install-update-01/azupdate12m.png)

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni [sull'amministrazione della matrice virtuale StorSimple](storsimple-ova-web-ui-admin.md).
