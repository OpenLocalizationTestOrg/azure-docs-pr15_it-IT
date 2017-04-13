<properties
    pageTitle="Scegliere un dominio Internet della società a un nome di dominio gestore del traffico | Microsoft Azure"
    description="In questo articolo consente di scegliere il nome di dominio della società a un nome di dominio gestore del traffico."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Scegliere un dominio Internet della società a un dominio di Azure il traffico Manager

Quando si crea un profilo di gestore del traffico, Azure assegna automaticamente un nome DNS per tale profilo. Per utilizzare un nome di zona DNS, creare un record CNAME DNS che esegue il mapping al nome di dominio del profilo gestore del traffico. Il nome di dominio il traffico Manager sono disponibili nella sezione **Generale** nella pagina Configurazione del profilo di traffico Manager.

Ad esempio, per scegliere contoso.trafficmanager.net di nome DNS Manager il traffico nome www.contoso.com, creare il record di risorse DNS seguente:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Tutte le richieste di traffico a *www.contoso.com* ottenere indirizzate a *contoso.trafficmanager.net*.

>[AZURE.IMPORTANT] Un dominio di secondo livello, ad esempio *contoso.com*, non è possibile scegliere il dominio di gestore del traffico. Standard protocollo DNS non consentire i record CNAME per i nomi di dominio di secondo livello.

## <a name="next-steps"></a>Passaggi successivi

- [Metodi di routing il traffico Manager](traffic-manager-routing-methods.md)
- [Abilita Manager - disattiva, il traffico o eliminare un profilo](disable-enable-or-delete-a-profile.md)
- [Il traffico Manager - disabilitare o abilitare un estremo](disable-or-enable-an-endpoint.md)
