<properties
    pageTitle="Web App in pila Azure - problemi noti e risoluzione dei problemi | Microsoft Azure"
    description="Istruzioni dettagliate per la distribuzione Web Apps in pila di Azure"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="web-apps-resource-provider---known-issues-and-troubleshooting"></a>Provider di risorse di applicazioni Web - problemi noti e risoluzione dei problemi

> [AZURE.NOTE] Le informazioni seguenti si applicano solo alle distribuzioni TP1 Stack Azure.

Se si verificano problemi durante la distribuzione o sull'utilizzo di Web App in pila Azure fare riferimento alle linee guida seguente.

## <a name="azure-stack-web-apps-not-available-in-the-portal"></a>Azure Stack Web App non disponibile nel portale

![Azure Stack Web Apps: creare nuova App Web][1]

Dopo aver completato la [registrazione del Provider di Azure Stack Web App](azure-stack-webapps-deploy.md#register-the-newly-deployed-azure-stack-web-apps-provider-with-arm) se non è possibile trovare il Web + blade per dispositivi mobili, provare a eseguire le operazioni seguenti:
* **Disconnessione del portale** e **chiudere il browser** e quindi in un **nuovo browser finestra account di accesso al portale** e riprovare.

Se ancora non è visibile il web e dispositivi mobili blade provare a eseguire le operazioni seguenti:

1.  Sul **Computer Host Stack Azure** nella **Gestione di Hyper-V** individuare le **xRPVM** e **connettersi** a macchina virtuale.
2.  Aprire un **prompt dei comandi come amministratore** ed eseguire **IISRESET**
3.  Tornare alla **ClientVM** e aprire una **nuova finestra del browser**, **accedere al portale** e riprovare.

## <a name="deployment-fails-when-creating-a-web-app-in-a-new-resource-group"></a>Distribuzione non riesce quando si crea un'App Web in un nuovo gruppo di risorse

Nella prima anteprima del Web App, è necessario creare **Tutte le applicazioni Web** nello stesso gruppo di risorse al Provider di risorse di Web App (**AppService locale**).  Si tratta di un problema noto e verrà risolto in un'anteprima dei futura.

## <a name="other-issues"></a>Altri problemi

Se si verificano altri problemi con Web Apps in pila Azure è possibile inviare [Forum di Stack Azure] (https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) in cui i membri del team siano controllando il post.


<!--Image references-->
[1]: ./media/azure-stack-webapps-troubleshoot-known-issues/NewWebandMobile.png



<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
