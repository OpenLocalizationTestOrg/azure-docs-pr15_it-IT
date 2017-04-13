<properties
    pageTitle="Spostare risorse Web App in un altro gruppo di risorse"
    description="Vengono descritti gli scenari in cui è possibile spostare Web App e servizi di App da un gruppo di risorse a un'altra."
    services="app-service"
    documentationCenter=""
    authors="ZainRizvi"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/04/2016"
    ms.author="zarizvi"/>
    
# <a name="supported-move-configurations"></a>Configurazioni spostamento supportati

È possibile spostare le risorse di Azure Web App tramite l' [Api risorse spostare ARM](../resource-group-move-resources.md).

Azure Web Apps attualmente supporta gli scenari di spostamento seguenti:

* Spostare l'intero contenuto di un gruppo di risorse (web apps, piani di servizio app e certificati) a un altro gruppo di risorse 
    * Nota: Il gruppo di risorse di destinazione non può contenere le risorse Microsoft.Web in questo scenario
* Spostamento di singoli web App a un gruppo di risorse diversi, mentre sono ancora ospitate il piano di servizio app corrente (il piano di servizio app rimane nel gruppo di risorse precedente)
