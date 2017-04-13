<properties
    pageTitle="Clonare App Web nel portale di Azure"
    description="Informazioni su come duplicare le app Web di nuove app Web nel portale di Azure."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/08/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-azure-portal"></a>Applicazione di servizio di App Azure clonare con Azure portale#

La caratteristica duplicazione di [Azure App servizio Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) consente di duplicare facilmente alle applicazioni web esistenti di un'app appena creata in un'area diversa o nella stessa regione. In questo modo i clienti distribuire un numero di App nelle diverse aree in modo semplice e rapido.

La duplicazione App è attualmente supportato solo per i piani di servizio premium livello app. La nuova caratteristica utilizza le stesse limitazioni come caratteristica di Backup di applicazioni Web, vedere [eseguire il backup un'app web nel servizio App Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 


## <a name="cloning-an-existing-app"></a>La duplicazione di un'App esistente ##

L'applicazione web deve essere in esecuzione la modalità di **Premium** per poter creare un duplicato per l'applicazione web.

1. Nel [Portale di Azure](https://portal.azure.com/), aprire blade dell'applicazione web.
2. Fare clic su **Strumenti**. Quindi, in e lo **Strumenti** , fare clic su **App duplicato**.

    ![][1]

    > [AZURE.NOTE]
    > Se l'applicazione web non è già in modalità **Premium** , si riceverà un messaggio che indica la modalità supportate per la duplicazione di app. A questo punto è possibile selezionare **l'aggiornamento**.
    
3. In e il **App duplicato** specificare un nome di nuova app web, gruppo di risorse e pianificare servizi App. Inoltre l'utente sarà possibile scegliere di duplicare un numero di impostazioni origine web app o meno.

    ![][2]

4. Dopo aver fatto clic **creare** la piattaforma inizierà lavoro sulla creazione di un duplicato di origine web app.

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>La duplicazione di un'App esistente in un ambiente di servizio App##

In e il **App Clona** il cliente avrà la possibilità di scegliere un pool di applicazioni in un ambiente App esistente.

## <a name="current-restrictions"></a>Restrizioni corrente ##

Questa funzionalità è attualmente in anteprima, ci stiamo impegnando per aggiungere nuove funzionalità nel tempo, l'elenco seguente sono associate le restrizioni note sul supporto corrente a app clonare nel portale di Azure:

- Impostazioni di gestione di traffico Azure non sono duplicate
- Impostazioni di ridimensionamento automatico non sono duplicate
- Impostazioni di backup pianificazione non sono duplicate
- Impostazioni di VNET non sono duplicate
- Informazioni dettagliate sui App non sono automaticamente impostati App web di destinazione
- Impostazioni di autenticazione semplice non sono duplicate
- Estensione di kudu non sono duplicati
- Suggerimento regole non sono duplicate
- Contenuto del database non sono duplicati


### <a name="references"></a>Riferimenti ###
- [Web App clonare tramite PowerShell](app-service-web-app-cloning.md)
- [Eseguire il backup un'app web nel servizio App Azure](web-sites-backup.md)
- [Come creare un ambiente App](app-service-web-how-to-create-an-app-service-environment.md)
- [Creare un'app web in un ambiente App](app-service-web-how-to-create-a-web-app-in-an-ase.md)
- [Introduzione all'ambiente di servizio di App](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png