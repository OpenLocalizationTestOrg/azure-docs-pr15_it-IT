<properties
    pageTitle="Ridistribuire Stack Azure | Microsoft Azure"
    description="Ridistribuire Stack Azure."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="erikje"/>

# <a name="redeploy-azure-stack"></a>Ridistribuire Stack Azure

Per ridistribuire dello Stack di Azure, è necessario ricominciare da zero come descritto di seguito.

## <a name="steps-to-redeploy-azure-stack"></a>Procedura per la ridistribuzione dello Stack di Azure

1. Riavviare l'host nel sistema operativo originale (installato per metallo bare). Questo non è l'impostazione predefinita nel menu di avvio, pertanto è necessario utilizzare KVM o console locale per selezionarlo durante il riavvio (durante l'installazione, è denominata "Avvio dal disco rigido virtuale" del sistema operativo a "AzureStack TP2", in questo modo identificare ossia OS che).

    Non è necessario rimuovere la voce di avvio esistente (il nuovo supporto per script "PrepareBootFromVHD.ps1" si occupa di cui si.)

2. Se non si dispone di KVM o si desidera scegliere il sistema operativo avvio prima di riavviare:
    
    1. Individuare.\BootMenuNoKVM.ps1 script. Il file è disponibile con gli altri script di supporto forniti insieme a questa compilazione.
    
    2. Eseguire lo script con privilegi elevati. Selezionare il nome del sistema operativo Host originale. Consente di avviare l'host nel sistema operativo host originale senza richiedere l'accesso KVM.
    
    3. Una volta completato lo script verrà chiesto di confermare il riavvio del computer.

    - Se sono presenti altri utenti connessi, questo comando non viene eseguito.

    - Solo eseguire il comando seguente: Restart-Computer-forza 
 
3. Eliminare il file CloudBuilder.vhdx utilizzato come parte della distribuzione precedente.

    Non è necessario eliminare il Pool di archiviazione esistente da distribuzione TP2 precedente. Script di distribuzione rileva pulisce esistente, quindi crea nuovi.

5. Ridistribuire di copiare una nuova copia CloudBuilder.vhdx, avvio, e così via.

## <a name="next-steps"></a>Passaggi successivi

[Connettersi a Stack Azure](azure-stack-connect-azure-stack.md)
