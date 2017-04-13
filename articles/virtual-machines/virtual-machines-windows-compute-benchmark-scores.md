<properties
 pageTitle="Calcolare punteggi benchmark per macchine virtuali di Windows | Microsoft Azure"
 description="Confronto tra punteggi benchmark di elaborazione SPECint per macchine virtuali di Azure che eseguono Windows Server"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="cynthn"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="cynthn"/>

# <a name="compute-benchmark-scores-for-windows-vms"></a>Calcolare punteggi benchmark per macchine virtuali di Windows

I punteggi benchmark SPECInt seguenti illustrare le prestazioni di calcolo per in linea con macchine Virtuali di prestazioni elevate di Azure che eseguono Windows Server. Calcolare punteggi benchmark sono inoltre disponibili per [Le macchine virtuali Linux](virtual-machines-linux-compute-benchmark-scores.md).



## <a name="a-series---compute-intensive"></a>Serie-complesse


Dimensioni | vCPUs | Nodi NUMA | CPU | Verrà eseguito | Tasso di base Media | Deviazione standard
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_A8 | 8 | 1 | Intel Xeon CPU E5 2670 0 @ 2,6 GHz | 10 | 236.1 | 1.1
Standard_A9 | 16 | 2 | Intel Xeon CPU E5 2670 0 @ 2,6 GHz | 10 | 450.3 | 7.0
Standard_A10 | 8 | 1 | Intel Xeon CPU E5 2670 0 @ 2,6 GHz | 5 | 235.6 | 0,9
Standard_A11 | 16 | 2 | Intel Xeon CPU E5 2670 0 @ 2,6 GHz |7 | 454.7 | 4.8

## <a name="dv2-series"></a>Serie di Dv2


Dimensioni | vCPUs | Nodi NUMA | CPU | Verrà eseguito | Tasso di base Media | Deviazione standard
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_D1_v2 | 1 | 1 | Intel Xeon E5 2673 v3 @ 2,4 GHz | 83 | 36,6 | 2.6
Standard_D2_v2 | 2 | 1 | Intel Xeon E5 2673 v3 @ 2,4 GHz | 27 | 70.0 | 3.7
Standard_D3_v2 | 4 | 1 | Intel Xeon E5 2673 v3 @ 2,4 GHz | 19 | 130.5 | 4.4
Standard_D4_v2 | 8 | 1 | Intel Xeon E5 2673 v3 @ 2,4 GHz | 19 | 238.1 | 5.2
Standard_D5_v2 | 16 | 2 | Intel Xeon E5 2673 v3 @ 2,4 GHz | 14 | 460.9 | da 15,4
Standard_D11_v2 | 2 | 1 | Intel Xeon E5 2673 v3 @ 2,4 GHz | 19 | 70,1 | 3.7
Standard_D12_v2 | 4 | 1 | Intel Xeon E5 2673 v3 @ 2,4 GHz | 2 | 132.0 | 1.4
Standard_D13_v2 | 8 | 1 | Intel Xeon E5 2673 v3 @ 2,4 GHz | 17 | 235.8 | 3.8
Standard_D14_v2 | 16 | 2 | Intel Xeon E5 2673 v3 @ 2,4 GHz | 15 | 460.8 | 6.5


## <a name="g-series-gs-series"></a>Serie G, GS serie


Dimensioni | vCPUs | Nodi NUMA | CPU | Verrà eseguito | Tasso di base Media | Deviazione standard
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_G1, Standard_GS1  | 2 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 31 | 71.8 | 6.5
Standard_G2, Standard_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 5 | 133.4 | 13.0
Standard_G3, Standard_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 6 | 242.3 | 6.0
Standard_G4, Standard_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 15 | 398.9 | 6.0
Standard_G5, Standard_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 @ 2 GHz | 22 | 762.8 | 3.7

## <a name="h-series"></a>Serie di H

Dimensioni | vCPUs | Nodi NUMA | CPU | Verrà eseguito | Iterazioni/sec | Deviazione standard
------- | ------ | ---- | -------| ---- | ---- | -----
Standard_H8 | 8 | 1 | Intel Xeon E5 2667 v3 @ 3,2 GHz | 5 | 297.4 | 0,9
Standard_H16 | 16 | 2 | Intel Xeon E5 2667 v3 @ 3,2 GHz | 5 | 575.8 | 6,8
Standard_H8m | 8 | 1 | Intel Xeon E5 2667 v3 @ 3,2 GHz | 5 | 297.0 | 1.2
Standard_H16m | 16 | 2 | Intel Xeon E5 2667 v3 @ 3,2 GHz | 5 | 572.2 | 3.9
Standard_H16r | 16 | 2 | Intel Xeon E5 2667 v3 @ 3,2 GHz | 5 | 573.2 | 2.9
Standard_H16mr | 16 | 2 | Intel Xeon E5 2667 v3 @ 3,2 GHz | 7 | 569.6 | 2,8

## <a name="about-specint"></a>Sulle SPECint

I numeri di Windows sono stati calcolati eseguono Windows Server [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) . SPECint è stata eseguita tramite l'opzione tasso base (SPECint_rate2006), con una copia per core. SPECint composto da 12 separato test, ogni eseguire tre volte, eseguendo la mediana da ogni test e peso in modo da ottenere un punteggio composto. Tali quindi test tra più macchine virtuali di fornire la media dei voti visualizzata.


## <a name="next-steps"></a>Passaggi successivi

* Per capacità di archiviazione, dettagli su disco e altre considerazioni per la scelta tra le dimensioni di macchine Virtuali, vedere [le dimensioni per macchine virtuali](virtual-machines-windows-sizes.md).
