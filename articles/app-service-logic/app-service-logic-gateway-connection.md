<properties
   pageTitle="Logica App locale connessione gateway dati | Microsoft Azure"
   description="Informazioni su come creare una connessione al gateway di dati in locale da un'app di logica."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="connect-to-the-on-premises-data-gateway-for-logic-apps"></a>Connettersi a gateway di dati locali per le applicazioni di logica

Logica supportati App connettori consentono di configurare la connessione a dati locali di accesso tramite il gateway di dati in locale.  La procedura seguente verrà descritto come installare e configurare il gateway di dati in locale per l'uso con un'app di logica.

## <a name="prerequisites"></a>Prerequisiti

* Deve essere utilizzando un lavoro o dell'istituto di istruzione indirizzo di posta elettronica in Azure per associare il gateway di dati locali con l'account (account Azure Active Directory in base a)
    * Se si utilizza un Account Microsoft (ad esempio @outlook.com, @live.com) è possibile usare l'account Azure per creare un lavoro o dell'istituto di istruzione indirizzo di posta elettronica eseguendo [la procedura descritta di seguito](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)

> [AZURE.WARNING] Esiste un limite di attualmente che locale gateway installa completeranno solo quando si utilizza un account che è stato registrato con Power BI.  Nel frattempo è necessario registrare tutti gli account con "Power BI gratuito" per completare l'installazione.

* È necessario che il sia locale dati gateway [installato nel computer locale](app-service-logic-gateway-install.md).
* Gateway non deve stato richiesto da un altro gateway di dati di Azure locale ([richiedere succede con la creazione del passaggio 2 sotto](#2-create-an-azure-on-premises-data-gateway-resource)) - un'installazione solo può essere associata alla risorsa un gateway.

## <a name="installing-and-configuring-the-connection"></a>Installare e configurare la connessione

### <a name="1-install-the-on-premises-data-gateway"></a>1. installare il gateway di dati in locale

Informazioni sull'installazione di gateway di dati locali sono disponibili [in questo articolo](app-service-logic-gateway-install.md).  Prima di continuare con il resto della procedura, è necessario installare il gateway su un computer locale.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. creazione di una risorsa di gateway di dati di Azure locale

Dopo aver installato, è necessario associare l'abbonamento Azure con il gateway di dati in locale.

1. Accedere a Azure utilizzando il lavoro stesso o l'indirizzo di posta elettronica dell'istituto di istruzione che è stato utilizzato durante l'installazione del gateway
1. Fare clic su **nuova** risorsa
1. Cercare e selezionare il **gateway di dati in locale**
1. Immettere le informazioni necessarie per associare il gateway a un account - tra cui selezionare il **Nome di installazione** appropriato

    ![Connessione di Gateway di dati in locale][1]
1. Fare clic sul pulsante **Crea** per creare la risorsa

### <a name="3-create-a-logic-app-connection-in-the-designer"></a>3. creare una connessione di app logica nella finestra di progettazione

Ora che l'abbonamento Azure è associata a un'istanza del gateway di dati in locale, è possibile creare una connessione a tale all'interno di un'app di logica.

1. Aprire un'applicazione di logica e scegliere un connettore che supporta la connettività locale (in questo modo, SQL Server)
1. Selezionare la casella di controllo per **connettersi tramite gateway di dati in locale**

    ![Creazione di Gateway App progettazione logica][2]
1. Selezionare il **Gateway** per connettersi a e completare qualsiasi altra informazione di connessione richiesta
1. Fare clic su **Crea** per creare la connessione

La connessione deve correttamente configurata per l'utilizzo in un'applicazione di logica.  

## <a name="next-steps"></a>Passaggi successivi
- [Esempi e scenari per l'App logica comuni](app-service-logic-examples-and-scenarios.md)
- [Caratteristiche di integrazione Enterprise](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG