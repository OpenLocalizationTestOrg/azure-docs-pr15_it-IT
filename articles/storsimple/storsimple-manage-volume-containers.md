<properties 
   pageTitle="Gestire i contenitori di volume StorSimple | Microsoft Azure"
   description="Spiega come è possibile utilizzare la pagina di contenitori StorSimple Manager volume del servizio per aggiungere, modificare o eliminare un contenitore di volume."
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
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-storsimple-volume-containers"></a>Utilizzare il servizio StorSimple Manager per gestire i contenitori volume StorSimple

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come utilizzare il servizio di gestione StorSimple per creare e gestire i contenitori volume StorSimple.

Un contenitore di volume in un dispositivo Microsoft Azure StorSimple contiene uno o più volumi che condividono account di archiviazione, sulla crittografia e impostazioni di consumo della larghezza di banda. Un dispositivo può contenere più contenitori volume per tutti i volumi. 

Un contenitore di volume sono previsti gli attributi seguenti:

- **Volumi** – volumi StorSimple a più livelli o localmente bloccati sono contenuti all'interno del contenitore di volume. Un contenitore di volume può contenere fino a 256 volumi di StorSimple.

- **Crittografia** : una chiave di crittografia che può essere definita per ogni contenitore volume. Questo tasto viene usato per la crittografia dei dati che viene inviati dal dispositivo StorSimple nel cloud. Un grado militare AES 256 bit usata insieme al tasto immessi dall'utente. Per proteggere i dati, è consigliabile attivare sempre la crittografia di spazio di archiviazione cloud.

- **Account di archiviazione** : l'account di archiviazione collegato al provider del servizio di archiviazione cloud. Tutti i volumi presenti in un contenitore di volume condividano questo account di archiviazione. È possibile scegliere un account di archiviazione da un elenco esistente oppure creare un nuovo account quando si crea il contenitore volume e quindi specificare le credenziali di accesso per l'account.

- **Larghezza di banda cloud** – la larghezza di banda utilizzata dal dispositivo quando i dati dal dispositivo vengono inviati nel cloud. È possibile applicare un controllo della larghezza di banda specificando un valore compreso tra 1 e 1000 Mbps quando si definisce il contenitore. Se si desidera il dispositivo per l'utilizzo della larghezza di banda disponibile tutti, impostare questo campo su illimitata. È anche possibile creare e applicare un modello di larghezza di banda per allocare della larghezza di banda in base alla programmazione.

![Pagina di contenitori volume](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

Procedure riportate di seguito viene illustrato come utilizzare la pagina StorSimple **contenitori Volume** per eseguire le operazioni comuni seguenti:

- Aggiungere un contenitore di volume 
- Modificare un contenitore di volume 
- Eliminare un contenitore di volume 

## <a name="add-a-volume-container"></a>Aggiungere un contenitore di volume

Eseguire la procedura seguente per aggiungere un contenitore di volume.

[AZURE.INCLUDE [storsimple-add-volume-container](../../includes/storsimple-add-volume-container.md)]


## <a name="modify-a-volume-container"></a>Modificare un contenitore di volume

Eseguire la procedura seguente per modificare un contenitore di volume.

[AZURE.INCLUDE [storsimple-modify-volume-container](../../includes/storsimple-modify-volume-container.md)]


## <a name="delete-a-volume-container"></a>Eliminare un contenitore di volume

Un contenitore di volume è volumi all'interno di essa. Possono essere eliminato solo se vengono eliminati tutti i volumi in essa contenuti. Eseguire la procedura seguente per eliminare un contenitore di volume.

[AZURE.INCLUDE [storsimple-delete-volume-container](../../includes/storsimple-delete-volume-container.md)]

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sulla [Gestione volumi StorSimple](storsimple-manage-volumes.md). 
- Ulteriori informazioni [sull'utilizzo del servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
