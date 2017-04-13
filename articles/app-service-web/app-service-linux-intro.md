<properties 
    pageTitle="Introduzione al servizio App su Linux | Microsoft Azure" 
    description="Informazioni sul servizio App su Linux." 
    keywords="servizio di applicazione Azure, linux, sistemi operativi"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="introduction-to-app-service-on-linux"></a>Introduzione al servizio App su Linux
Servizio App su Linux attualmente Public Preview e supporta l'esecuzione di applicazioni web a livello nativo su Linux. 

## <a name="overview"></a>Panoramica ##
Clienti consente servizio App su Linux host web App a livello nativo su Linux per stack di applicazioni supportate. Nella sezione caratteristiche seguente Elenca stack applicazione attualmente supportata.

## <a name="features"></a>Caratteristiche ##
Servizio di App su Linux supporta attualmente seguenti stack di applicazioni

- Node
- PHP

Clienti di distribuire le applicazioni utilizzando

- FTP.
- Operazioni locale.
- GitHub o BitBucket.

Per il ridimensionamento dell'applicazione


- I clienti possono scalare app web alto e verso il basso modificando il livello nel loro piano di servizio App. 
- I clienti possono scalare applicazioni fuori ed eseguire app tra più istanze entro i confini del loro SKU.

Per Kudu alcune delle funzionalità di base funzionerà

- Ambiente.
- Distribuzioni.
- Console di base.

## <a name="limitations"></a>Limitazioni ##

Portale di gestione Azure verrà solo visualizzare caratteristiche attualmente supportate per servizio App su Linux e nascondere tutte le altre applicazioni. Come il nostro team abilitazione altre caratteristiche sono verrà mantenere che riflettono questo nel portale di gestione. Alcune funzionalità come integrazione VNET e AAD / Kudu sito estensioni o l'autenticazione di terze parti non attualmente funzionano. Ma come si ottengono questi uso Microsoft aggiornerà la documentazione e blog sulle modifiche apportate.

Questa versione di anteprima pubblico è attualmente disponibile solo nei seguenti paesi

-   Stati Uniti ovest.
-   Europa occidentale.
-   Sudest asiatico.

Web app su Linux è supportato solo nelle dedicato App servizio plan di messaggistica unificata e non dispone di un livello disponibile o condivisi. Inoltre, i piani di servizio app per regolare e Linux web apps si escludono a vicenda, non è possibile creare un'app web Linux in un piano di servizio non Linux app.

Web app in Linux devono essere creati in un gruppo di risorse che non supporta le applicazioni web non Linux nella stessa regione.

A causa di mancanza di riciclaggio sovrapposta delle web App, clienti aspettarsi che se si ha riavviato un tempo di inattività piccole in caso di un'app web. 

## <a name="next-steps"></a>Passaggi successivi ##

Seguire i collegamenti seguenti per iniziare con il servizio di App su Linux. Inviare domande e problemi al [nostro forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [La creazione di applicazioni Web nel servizio di App su Linux](./app-service-linux-how-to-create-a-web-app.md)
* [Utilizzo di configurazione PM2 per Node nelle App Web su Linux](./app-service-linux-using-nodejs-pm2.md)

