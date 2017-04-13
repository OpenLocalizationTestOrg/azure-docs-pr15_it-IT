<properties
   pageTitle="Creare un gateway di applicazione tramite CLI Azure in Gestione risorse | Microsoft Azure"
   description="Informazioni su come creare un Gateway di applicazione tramite CLI Azure in Gestione risorse"
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

# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Creare un gateway di applicazione tramite CLI Azure

> [AZURE.SELECTOR]
- [Portale di Azure](application-gateway-create-gateway-portal.md)
- [Azure Manager delle risorse PowerShell](application-gateway-create-gateway-arm.md)
- [Azure PowerShell classica](application-gateway-create-gateway.md)
- [Modelli di Azure Manager delle risorse](application-gateway-create-gateway-arm-template.md)
- [CLI Azure](application-gateway-create-gateway-cli.md)

Gateway di applicazione Azure è un bilanciamento del carico di livello 7. Fornisce failover, routing prestazioni richieste HTTP tra server diversi, se sono nel cloud o locale. Gateway di applicazione sono le seguenti funzionalità di recapito applicazione: HTTP caricare le ricerche dello stato personalizzato di bilanciamento del carico, affinità sessione basata su cookie e carico di lavoro Secure Sockets Layer (SSL) e al supporto per più siti.

## <a name="prerequisite-install-the-azure-cli"></a>Prerequisito: Installare CLI Azure

Per eseguire la procedura descritta in questo articolo, è necessario [installare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows Azure CLI ()](../xplat-cli-install.md) ed è necessario effettuare [l'accesso a Azure](../xplat-cli-connect.md). 

> [AZURE.NOTE] Se non si dispone di un account Azure, è più necessario. Salire di accesso per la [versione di valutazione di seguito](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Scenario:

In questo scenario, si viene illustrato come creare un gateway di applicazione tramite il portale di Azure.

Questo scenario consente di:

- Creare un gateway di applicazione Media con due istanze.
- Creare una rete virtuale denominata AdatumAppGatewayVNET con un blocco CIDR riservato di 10.0.0.0/16.
- Creare una subnet chiamata Appgatewaysubnet che utilizza 10.0.0.0/28 come il blocco CIDR.
- Configurare un certificato per offload SSL.

![Scenario di esempio][scenario]

>[AZURE.NOTE] Esegue la ricerca di un'ulteriore configurazione del gateway applicazione, tra cui dello stato personalizzato, gli indirizzi di back-end del pool e regole aggiuntive sono configurati dopo aver configurato il gateway di applicazioni e non durante l'installazione iniziale.

## <a name="before-you-begin"></a>Prima di iniziare

Gateway di applicazione Azure richiede propria subnet. Quando si crea una rete virtuale, assicurarsi di lasciare sufficiente spazio di indirizzi per avere più subnet. Quando si distribuisce un gateway di applicazioni a una subnet, gateway applicazione solo aggiuntivi sono in grado da aggiungere alla subnet.

## <a name="log-in-to-azure"></a>Accedere a Azure

Aprire il **Prompt dei comandi di Microsoft Azure**e accedere. 

    azure login

Dopo aver digitato l'esempio precedente, viene fornito un codice. Passare a https://aka.ms/devicelogin in un browser per continuare la procedura di accesso.

![login di dispositivo con cmd][1]

Nel browser, immettere il codice ricevuto. Si verrà reindirizzati alla pagina di accesso.

![browser per immettere il codice][2]

Dopo aver immesso il codice è effettuato l'accesso, chiudere il browser per continuare con lo scenario.

![accesso effettuato][3]

## <a name="switch-to-resource-manager-mode"></a>Passare alla modalità di gestione risorse

    azure config mode arm

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse

Prima di creare il gateway di applicazione, viene creato un gruppo di risorse per contenere il gateway di applicazione. Di seguito viene illustrato il comando.

    azure group create -n AdatumAppGatewayRG -l eastus

## <a name="create-a-virtual-network"></a>Creare una rete virtuale

Dopo aver creato il gruppo di risorse, viene creata una rete virtuale per il gateway di applicazione.  Nell'esempio seguente, lo spazio di indirizzi è stato come 10.0.0.0/16 come definito nelle note scenario precedente.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

## <a name="create-a-subnet"></a>Creare una subnet

Dopo aver creata la rete virtuale, viene aggiunta una subnet per il gateway di applicazione.  Se si prevede di usare gateway di applicazioni con un'app web ospitata nella stessa rete virtuale il gateway di applicazione, assicurarsi di lasciare spazio sufficiente per un'altra subnet.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

## <a name="create-the-application-gateway"></a>Creare il gateway di applicazione

Dopo aver creati la rete virtuale e subnet, i prerequisiti per il gateway di applicazione sono stati completati. Inoltre un certificato PFX precedentemente esportato e la password per il certificato sono necessari per il passo successivo: indirizzi IP utilizzati per back-end sono gli indirizzi IP per il server di back-end. Questi valori possono essere IP privato nella rete virtuale, IP pubblico o nomi di dominio completo per il server di back-end.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd -z 2 -a Standard_Medium -w Basic -j 443 -f Enabled -o 80 -i http -b https -u Standard

> [AZURE.NOTE] Per un elenco dei parametri disponibili durante la creazione di eseguire il comando seguente: **gateway di applicazioni azure rete creare - Guida**.

In questo esempio viene creato un gateway di applicazione di base con le impostazioni predefinite per comunicare ascoltatore, pool di back-end, le impostazioni http back-end e le regole. Inoltre, viene configurata offload SSL. È possibile modificare queste impostazioni in base alla distribuzione dopo il provisioning ha esito positivo.
Se si dispone già di applicazione web definito con il pool di back-end nei passaggi precedenti, una volta creato, il bilanciamento del carico inizia.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare le ricerche dello stato personalizzato visitando la [creazione di una verifica dell'integrità personalizzato](application-gateway-create-probe-portal.md)

Informazioni su come configurare SSL possibilità di scaricare ed eseguire la decrittografia SSL costosa disattivare il server web, visitare [Configurare trasferire SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png