<properties
    pageTitle="Attività di accesso irregolare"
    description="Un report che include una firma aggiuntivi che sono stati identificati come anomala tramite il nostro algoritmi di apprendimento."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="gchander"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>

# <a name="irregular-sign-in-activity"></a>Attività di accesso irregolare

Componenti aggiuntivi di accesso irregolari sono quelli identificato dal nostro algoritmi, in base a una condizione "Impossibile viaggio" combinato con un segno di anomalo nel percorso e il dispositivo di apprendimento. È possibile che un pirata informatico ha completato l'accesso tramite questo account.
Se si verificano 10 o più anomali accesso eventi in un intervallo di 30 giorni viene inviato un messaggio di notifica per gli amministratori globali. Assicurarsi di includere aad-alerts-noreply@mail.windowsazure.com nell'elenco Mittenti attendibili.
