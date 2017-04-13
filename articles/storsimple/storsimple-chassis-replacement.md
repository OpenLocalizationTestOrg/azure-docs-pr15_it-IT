<properties 
   pageTitle="Sostituire chassis in un dispositivo StorSimple | Microsoft Azure"
   description="In questo articolo viene descritto come rimuovere e sostituire chassis per il simbolo principale StorSimple o simbolo EBOD."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="replace-the-chassis-on-your-storsimple-device"></a>Sostituire chassis sul dispositivo StorSimple

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come rimuovere e sostituire uno chassis in un dispositivo di serie 8000 StorSimple. Il modello 8100 StorSimple è un dispositivo di simbolo singola (un'unica struttura), mentre il 8600 è un dispositivo simbolo doppio (due chassis). Per un modello di 8600, esistono potenzialmente due chassis che potrebbe non riuscire nel dispositivo: chassis per il simbolo principale o chassis per il simbolo EBOD.

In entrambi i casi, chassis sostituzione forniti da Microsoft è vuoto. Nessun Power e raffreddamento moduli (PCMs), moduli, controller disco SSD (), l'unità disco rigido (dischi rigidi) o moduli EBOD verranno inclusi.

>[AZURE.IMPORTANT] Prima di rimozione e la sostituzione chassis, rivedere le informazioni di sicurezza in [sostituzione di componenti hardware StorSimple](storsimple-hardware-component-replacement.md).

## <a name="remove-the-chassis"></a>Rimuovere lo chassis

Eseguire la procedura seguente per rimuovere chassis sul dispositivo StorSimple.

#### <a name="to-remove-a-chassis"></a>Per rimuovere un telaio

1. Assicurarsi che il dispositivo StorSimple arrestato e disconnessi da tutte le origini di power.

2. Rimuovere tutte le rete e cavi SA, se applicabile.

3. Rimuovere l'unità dal rack.

4. Rimozione di ognuna delle unità e notare le bande da cui vengono rimossi. Per ulteriori informazioni, vedere [rimuovere l'unità disco](storsimple-disk-drive-replacement.md#remove-the-disk-drive).

5. In simbolo EBOD (se si tratta di chassis che non è riuscito), rimuovere i moduli di controller EBOD. Per ulteriori informazioni, vedere [rimuovere un controller di EBOD](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller). 

    In simbolo principale (se si tratta di chassis che non è riuscito), rimuovere i controller e osservare le bande da cui vengono rimossi. Per ulteriori informazioni, vedere [rimuovere un controller](storsimple-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Installare il chassis

Eseguire la procedura seguente per installare il chassis in un dispositivo StorSimple.

#### <a name="to-install-a-chassis"></a>Per installare un telaio

1. Installare chassis nel rack. Per ulteriori informazioni, vedere [installazione a Rack dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) o [dispositivo 8600 StorSimple di installazione a Rack](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).

2. Dopo lo chassis nel rack, installare i moduli controller nella stessa posizione in cui in precedenza sono stati installati.

3. Installare le unità nella stessa posizione e bande orarie che in precedenza sono stati installati in.

    >[AZURE.NOTE] È consigliabile installare prima le SSDs nelle bande e quindi installare i dischi rigidi.

2. Con il dispositivo è installato in rack e i relativi componenti, collegare il dispositivo alle origini presa di corrente e attivare il dispositivo. Per informazioni dettagliate, vedere [cavo dispositivo 8100 StorSimple](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) o [il cavo dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla [sostituzione di componenti hardware StorSimple](storsimple-hardware-component-replacement.md).

