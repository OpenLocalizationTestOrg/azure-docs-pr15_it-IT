<properties
   pageTitle="Creare una regola per un gateway di applicazioni basate su percorso tramite il portale | Microsoft Azure"
   description="Informazioni su come creare una regola per un gateway di applicazioni basate su percorso tramite il portale"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-portal"></a>Creare una regola per un gateway di applicazioni basate su percorso tramite il portale

> [AZURE.SELECTOR]
- [Portale di Azure](application-gateway-create-url-route-portal.md)
- [Azure Manager delle risorse PowerShell](application-gateway-create-url-route-arm-ps.md)

URL basato sul percorso routing consente di associare route in base al percorso URL della richiesta Http. Controlla se esiste una route a un pool di back-end configurato per gli elenchi di URL nel Gateway di applicazioni e inviare il traffico di rete al pool di back-end definito. Un utilizzo comune per il routing basato su URL è carico per diversi tipi di contenuto per i pool diversi server di back-end.

Routing basato su URL introduce un nuovo tipo di regola al gateway di applicazione. Gateway di applicazione offre due tipi di regole: regole di base e basato sul percorso. Tipo di regola base fornisce servizio circolari per i pool di back-end durante le regole basate sul percorso oltre distribuzione circolari, tiene motivo percorso dell'URL della richiesta in considerazione anche la scelta del pool di back-end.

## <a name="scenario"></a>Scenario:

Lo scenario seguente passa attraverso la creazione di una regola basata su percorso in un gateway di applicazioni esistente.
Lo scenario presuppone che la procedura è già stato completata per [creare un Gateway di applicazioni](application-gateway-create-gateway-portal.md).

![route dell'URL][scenario]

## <a name="createrule"></a>Creare la regola di percorso

Una regola basata su percorso richiede il proprio comunicare ascoltatore, prima di verificare la creazione della regola è necessario un comunicare ascoltatore disponibili da utilizzare.

### <a name="step-1"></a>Passaggio 1

Passare a http://portal.azure.com e selezionare un gateway di applicazioni esistente. Fare clic su **regole**

![Panoramica del Gateway di applicazioni][1]

### <a name="step-2"></a>Passaggio 2

Fare clic su pulsante **basato sul percorso** per aggiungere una nuova regola percorso.

### <a name="step-3"></a>Passaggio 3

E il **componente basato sul percorso regola** include due sezioni. La prima sezione è in cui è definito comunicare ascoltatore, il nome della regola e le impostazioni di percorso predefinito. Le impostazioni di percorso predefinito sono per route che non rientrano in route basato sul percorso personalizzato. La seconda sezione e il **componente basato sul percorso regola** viene utilizzato per definire le regole basate sul percorso se stessi.

**Impostazioni di base**

- **Nome** - si tratta di un nome descrittivo per la regola che è accessibile nel portale.
- **Comunicare ascoltatore** - si tratta di comunicare ascoltatore utilizzato per la regola.
- **Pool di back-end predefinito** - questa impostazione è l'impostazione che consente di definire il back-end da utilizzare per la regola predefinita
- **Le impostazioni predefinite per HTTP** - questa impostazione è l'impostazione che consente di definire le impostazioni di HTTP da utilizzare per la regola predefinita.

**Regole basate sul percorso**

- **Nome** - si tratta di un nome alla regola basato sul percorso.
- **Percorsi** - questa impostazione definisce il percorso che la regola cercherà quando il traffico di inoltro
- **Pool di back-end** - questa impostazione è l'impostazione che consente di definire il back-end da utilizzare per la regola
- **Impostazione di HTTP** - questa impostazione è l'impostazione che consente di definire le impostazioni di HTTP da utilizzare per la regola.

>[AZURE.IMPORTANT] Percorsi: L'elenco dei modelli di percorso per la corrispondenza. Ogni deve iniziare con / e il luogo solo un "\*" consentito sia posizionato alla fine. Esempi validi sono /xyz /xyz* o /xyz/*.  

![Aggiungere blade basato sul percorso regola con informazioni compilate][2]

Aggiunta di una regola basato sul percorso per un gateway di applicazioni esistente è un processo semplice tramite il portale. Dopo aver creata una regola basata su percorso, può essere modificata per aggiungere altre regole facilmente. 

![aggiunta di altre regole basate su percorso][3]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare SSL scaricando con Azure applicazione Gateway vedere [Configurare trasferire SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png