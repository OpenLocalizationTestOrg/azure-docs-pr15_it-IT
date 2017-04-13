<properties
    pageTitle="Azure B2C Directory attiva: Attributi personalizzati | Microsoft Azure"
    description="Come usare gli attributi personalizzati in Azure Active Directory B2C di raccogliere informazioni sui consumatori"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

#  <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure B2C Directory attiva: Consente di raccogliere informazioni sui consumatori attributi personalizzati

Directory di Azure Active Directory (Azure Active Directory) B2C viene fornito con una serie incorporata di informazioni (attributi): nome, cognome, città, CAP e altri attributi. Tuttavia, applicazione ogni consumer esposto a specifiche esigenze in quali attributi per raccogliere dai consumatori. Con Azure Active Directory B2C, è possibile estendere l'insieme di attributi archiviati nella pagina account ogni consumer. È possibile creare attributi personalizzati nel [portale di Azure](https://portal.azure.com/) e usare i criteri per l'abbonamento, come illustrato di seguito. È anche possibile leggere e scrivere questi attributi tramite l' [API di Azure Active Directory grafico](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [AZURE.NOTE]
Gli attributi personalizzati utilizzare [estensioni dello Schema di Azure Active Directory grafico API Directory](https://msdn.microsoft.com/library/azure/dn720459.aspx).

## <a name="create-a-custom-attribute"></a>Creare un attributo personalizzato

1. [Seguire questa procedura per passare a e il caratteristiche B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **attributi dell'utente**.
3. Fare clic su **Add +** nella parte superiore e il.
4. Specificare un **nome** per l'attributo personalizzato (ad esempio, "ShoeSize") e facoltativamente una **Descrizione**. Fare clic su **Crea**.

    > [AZURE.NOTE]
    Solo il **Tipo di dati** di "Stringa" è attualmente disponibile.

L'attributo personalizzato è ora disponibile nell'elenco degli **attributi utente**e per poterlo usare nei criteri di iscrizione.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Utilizzare un attributo personalizzato nei criteri di iscrizione

1. [Seguire questa procedura per passare a e il caratteristiche B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **criteri per l'abbonamento**.
3. Fare clic sui criteri per l'abbonamento (ad esempio, "B2C_1_SiUp") per aprirlo. Fare clic su **Modifica** nella parte superiore e il.
4. Fare clic su **iscrizione attributi** e selezionare l'attributo personalizzato (ad esempio, "ShoeSize"). Fare clic su **OK**.
5. Selezionare l'attributo personalizzata **reclami applicazione** . Fare clic su **OK**.
6. Fare clic su **Salva** nella parte superiore e il.

È possibile utilizzare la funzionalità "Esegui" sul criterio per verificare l'esperienza utente. È ora necessario vedere "ShoeSize" nell'elenco degli attributi raccolte durante l'iscrizione consumer e visualizzarlo nel token inviato all'applicazione.

## <a name="notes"></a>Note

- Insieme criteri per l'abbonamento, attributi personalizzati possono essere utilizzati anche in criteri per l'abbonamento o di accesso e modifica dei criteri del profilo.
- Esiste un limite noto di attributi personalizzati. Viene creato solo la prima volta che viene usato in qualsiasi criterio e non quando si aggiunge all'elenco di **attributi utente**.
