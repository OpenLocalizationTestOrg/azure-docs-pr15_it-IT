<properties
    pageTitle="Per configurare le connessioni VPN in Azure API Management"
    description="Informazioni su come configurare una connessione VPN Azure API e nella gestione dei servizi web di access attraversata."
    services="api-management"
    documentationCenter=""
    authors="antonba"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="antonba"/>

# <a name="how-to-setup-vpn-connections-in-azure-api-management"></a>Per configurare le connessioni VPN in Azure API Management

Supporto VPN API gestione consente di connettere il gateway di gestione dell'API a una rete virtuale Azure (classico). In questo modo API Gestione clienti in modo sicuro connettersi ai servizi web back-end locale o in caso contrario non sono accessibili a internet.

>[AZURE.NOTE] Gestione dell'API Azure funziona con VNETs classica. Per informazioni sulla creazione di un VNET classico, vedere [creare una rete virtuale (classica) tramite il portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Per informazioni sulla connessione VNETs classico per ARM VNETS, vedere [connessione VNets classico per VNets nuovo](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="enable-vpn"> </a>Connessioni VPN attiva

>La connettività VPN è disponibile solo in livelli **Premium** e **sviluppo** . Per passare all'applicazione, aprire il servizio di gestione dell'API nel [Portale classica Azure][] e quindi aprire la scheda **scala** . Nella sezione **Generale** selezionare il livello di Premium e fare clic su Salva.

Per abilitare la connettività VPN, aprire il servizio di gestione dell'API nel [Portale classica Azure][] e passare alla scheda **Configura** . 

Nella sezione VPN passare **connessione VPN** su **On**.

![Configurare la scheda di gestione delle API istanza][api-management-setup-vpn-configure]

Vedrete un elenco di tutti i paesi in cui viene eseguito il provisioning del servizio di gestione dell'API.

Selezionare un VPN e subnet per ogni area. Elenco delle VPN vengono inseriti in base a reti virtuali disponibili nel proprio abbonamento Azure impostati nell'area che si sta configurando.

![Selezionare VPN][api-management-setup-vpn-select]

Fare clic su **Salva** nella parte inferiore dello schermo. Non sarà possibile eseguire altre operazioni il servizio di gestione delle API dal portale classica di Azure durante aggiornamento. Il gateway servizio rimarranno disponibile e le chiamate di runtime potrebbero non essere ottimali.

Si noti che l'indirizzo VIP del gateway cambierà ogni volta che VPN è attivato o disattivato.

## <a name="connect-vpn"> </a>Connettersi a un servizio web dietro VPN

Dopo il servizio di gestione dell'API è connesso alla VPN, accesso ai servizi web all'interno della rete virtuale vi è alcuna differenza rispetto all'accesso alla servizi pubblici. Digitare l'indirizzo locale o il nome host (se è stato configurato un server DNS per la rete virtuale Azure) del servizio web nel campo **URL del servizio Web** quando si crea una nuova API o la modifica di uno esistente.

![Aggiungere API da VPN][api-management-setup-vpn-add-api]

## <a name="required-ports-for-api-management-vpn-support"></a>Porte richieste per il supporto dell'API Gestione VPN

Quando un'istanza di servizio di gestione delle API è ospitata in un VNET, vengono utilizzate le porte descritti nella tabella seguente. Se queste porte sono bloccate, il servizio potrebbe non funzionare correttamente. Con una o più delle seguenti porte bloccate è il problema più comune di configurazione errata quando si usa l'API gestione con un VNET.

| Porte                      | Direzione        | Protocollo di trasporto | Scopo                                                          | Origine / destinazione              |
|------------------------------|------------------|--------------------|------------------------------------------------------------------|-----------------------------------|
| 80, 443                      | In ingresso          | TCP                | Comunicazione client alla gestione dei API                           | INTERNET / VIRTUAL_NETWORK        |
| 80,443                       | In uscita         | TCP                | API Gestione dipendenza da Azure spazio di archiviazione e Bus di servizio Azure | VIRTUAL_NETWORK / INTERNET        |
| 1433                         | In uscita         | TCP                | Dipendenza dell'API Gestione SQL                               | VIRTUAL_NETWORK / INTERNET        |
| 9350, 9351, 9352, 9353, 9354 | In uscita         | TCP                | Dipendenza dell'API Gestione Bus di servizio                       | VIRTUAL_NETWORK / INTERNET        |
| 5671                         | In uscita         | AMQP               | Dipendenza di gestione di API per Log all'evento criteri Hub            | VIRTUAL_NETWORK / INTERNET        |
| 6381, 6382, 6383             | In ingresso/uscita | UDP                | Dipendenza dell'API Gestione Cache Redis                       | VIRTUAL_NETWORK / VIRTUAL_NETWORK |
| 445                          | In uscita         | TCP                | API Gestione dipendenza da una condivisione File Azure per fra            | VIRTUAL_NETWORK / INTERNET        |

## <a name="custom-dns"> </a>Il programma di installazione di custom DNS server

Gestione delle API dipende da diversi servizi Azure. Quando un'istanza di servizio di gestione delle API è ospitata in un VNET in cui viene utilizzato un server DNS personalizzato, è necessario essere in grado di risolvere nomi host di tali servizi Azure. Seguire [questa](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) Guida alle impostazioni DNS personalizzato.  

## <a name="related-content"> </a>Contenuto correlato


* [Creare una rete virtuale con una connessione VPN da sito tramite il portale classica di Azure][]
* [Come usare la caratteristica controllo API tracciare chiamate in Azure API Management][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Portale classica Azure]: https://manage.windowsazure.com/

[Creare una rete virtuale con una connessione VPN da sito tramite il portale classica di Azure]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[Come usare la caratteristica controllo API tracciare chiamate in Azure API Management]: api-management-howto-api-inspector.md
