<properties
   pageTitle="Creare un gateway di applicazioni con firewall di applicazione web tramite il portale | Microsoft Azure"
   description="Informazioni su come creare un Gateway di applicazioni con firewall di applicazione web tramite il portale"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"
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

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>Creare un gateway di applicazioni con firewall di applicazione web tramite il portale

> [AZURE.SELECTOR]
- [Portale di Azure](application-gateway-web-application-firewall-portal.md)
- [Azure Manager delle risorse PowerShell](application-gateway-web-application-firewall-powershell.md)

Il firewall di applicazione web (WAF) in Azure applicazione Gateway protegge le applicazioni web da attacchi comuni basato sul web come inserimento SQL, attacchi di script e assume il controllo della sessione. Applicazione Web consente di proteggere molti le OWASP superiore 10 web vulnerabilità comuni.

Gateway di applicazione Azure è un bilanciamento del carico di livello 7. Fornisce failover, routing prestazioni richieste HTTP tra server diversi, se sono nel cloud o locale.
Applicazione offre molte caratteristiche di applicazione recapito Controller (ADC) inclusi bilanciamento del carico HTTP, affinità sessione basata su cookie, trasferire Secure Sockets Layer (SSL), le ricerche dello stato personalizzato, il supporto per più siti e molti altri.
Per trovare un elenco completo delle funzionalità supportate, vedere [Applicazione Gateway Panoramica](application-gateway-introduction.md)

## <a name="scenarios"></a>Scenari

In questo articolo sono disponibili due scenari:

Nel primo scenario informazioni su come [aggiungere firewall di applicazione web per un gateway di applicazioni esistente](#add-web-application-firewall-to-an-existing-application-gateway).

Nel secondo scenario informazioni su come [creare un gateway di applicazioni con firewall di applicazione web](#create-an-application-gateway-with-web-application-firewall)

![Scenario di esempio][scenario]

>[AZURE.NOTE] Esegue la ricerca di un'ulteriore configurazione del gateway applicazione, tra cui dello stato personalizzato, gli indirizzi di back-end del pool e regole aggiuntive sono configurati dopo aver configurato il gateway di applicazioni e non durante l'installazione iniziale.

## <a name="before-you-begin"></a>Prima di iniziare

Gateway di applicazione Azure richiede propria subnet. Quando si crea una rete virtuale, assicurarsi di lasciare sufficiente spazio di indirizzi per avere più subnet. Quando si distribuisce un gateway di applicazioni a una subnet, gateway applicazione solo aggiuntivi sono in grado da aggiungere alla subnet.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Aggiungere un gateway di applicazioni esistente firewall applicazione web

Questo scenario consente di aggiornare un gateway di applicazioni esistente per supportare firewall applicazione web in modalità programmi.

### <a name="step-1"></a>Passaggio 1

Passare al portale di Azure, selezionare un Gateway di applicazioni esistente.

![Creazione di Gateway di applicazioni][1]

### <a name="step-2"></a>Passaggio 2

Fare clic su **configurazione** e aggiornare le impostazioni del gateway dell'applicazione. Al termine, fare clic su **Salva**

Le impostazioni per aggiornare un gateway di applicazioni esistente per supportare firewall applicazione web sono:

- **Livello** - livello selezionato deve essere **WAF** per il supporto firewall applicazione web
- **Dimensioni SKU** - questa impostazione è la dimensione del gateway dell'applicazione con firewall di applicazione web, le opzioni disponibili sono (**medie** e **grandi dimensioni**).
- **Stato del firewall** - questa impostazione disabilita o Abilita firewall di applicazione web.
- **Modalità firewall** - questa impostazione è come firewall applicazione web gestisce il traffico dannoso. Modalità di **rilevamento** registra solo gli eventi in modalità di **prevenzione** registra gli eventi e interrompe il traffico dannoso.

![impostazioni di base con blade][2]

>[AZURE.NOTE] Per visualizzare i registri firewall applicazione web, è necessario che sia abilitato: posta in arrivo e ApplicationGatewayFirewallLog selezionato. È possibile scegliere un conteggio delle istanze di 1 a scopo di testing. È importante sapere che qualsiasi conteggio delle istanze in due istanze non è coperto dal contratto di servizio e sono pertanto non è consigliabile. Piccolo gateway non sono disponibili quando si utilizza il firewall di applicazione web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Creare un gateway di applicazioni con firewall di applicazione web

Questo scenario consente di:

- Creare un gateway di applicazione web medio applicazione firewall con due istanze.
- Creare una rete virtuale denominata AdatumAppGatewayVNET con un blocco CIDR riservato di 10.0.0.0/16.
- Creare una subnet chiamata Appgatewaysubnet che utilizza 10.0.0.0/28 come il blocco CIDR.
- Configurare un certificato per offload SSL.

### <a name="step-1"></a>Passaggio 1

Passare al portale di Azure, fare clic su **Nuovo** > **rete** > **Gateway di applicazioni**

![Creazione di Gateway di applicazioni][1-1]

### <a name="step-2"></a>Passaggio 2

Compilare quindi le informazioni di base per il gateway di applicazione. Assicurarsi di scegliere **WAF** del livello. Al termine fare clic su **OK**

Le informazioni necessarie per le impostazioni di base sono:

- **Nome** - nome per il gateway di applicazione.
- **Livello** - il livello del gateway applicazione le opzioni disponibili sono (**Standard** e **WAF**). È disponibile nel livello WAF solo firewall di applicazione Web.
- **Dimensioni SKU** - questa impostazione è la dimensione del gateway dell'applicazione, le opzioni disponibili sono (**medie** e **grandi dimensioni**).
- **Conteggio delle istanze** - il numero di istanze, questo valore deve essere un numero compreso tra **2** e **10**.
- **Gruppo di risorse** - al gruppo di risorse per contenere il gateway di applicazione, può essere un gruppo di risorse esistente o uno nuovo.
- **Posizione** , l'area per il gateway di applicazione, è nella stessa posizione del gruppo di risorse. *Il percorso è importante come la rete virtuale e IP pubblico deve essere nello stesso percorso il gateway*.

![impostazioni di base con blade][2-2]

>[AZURE.NOTE] È possibile scegliere un conteggio delle istanze di 1 a scopo di testing. È importante sapere che qualsiasi conteggio delle istanze in due istanze non è coperto dal contratto di servizio e sono pertanto non è consigliabile. Piccolo gateway non sono supportati per gli scenari firewall di applicazione web.

### <a name="step-3"></a>Passaggio 3

Una volta definite le impostazioni di base, il passaggio successivo consiste nel definire virtuali da utilizzare. La rete virtuale ospita l'applicazione che il gateway di applicazione il bilanciamento del carico per.

Fare clic su **Scegli una rete virtuale** per configurare la rete virtuale.

![blade con le impostazioni di gateway di applicazioni][3]

### <a name="step-4"></a>Passaggio 4

In e lo **Scegliere virtuali** , fare clic su **Crea nuovo**

Mentre non illustrate in questo scenario, una rete virtuale esistente che selezionata a questo punto.  Se si utilizza una rete virtuale esistente, è importante conoscere che la rete virtuale subnet vuota o subnet solo risorse delle applicazioni gateway da utilizzare.

![Scegliere blade virtuali][4]

### <a name="step-5"></a>Passaggio 5

Compilare le informazioni di rete e il **Creare virtuali** come descritto nella descrizione dello [Scenario](#scenario) precedente.

![Creare blade virtuali con le informazioni immesse][5]

### <a name="step-6"></a>Passaggio 6

Dopo aver creata la rete virtuale, il passaggio successivo consiste nel definire l'indirizzo IP front-end per il gateway di applicazione. A questo punto, la scelta è compresa tra un pubblico o un indirizzo IP privato per front-end. La scelta varia a seconda che l'applicazione internet esposto o interno solo. Questo scenario presuppone usando un indirizzo IP pubblico. Per scegliere un indirizzo IP privato, è possibile fare clic su pulsante **privato** . Viene selezionato automaticamente assegnato un indirizzo IP o è possibile fare clic sulla casella di controllo **scegliere uno specifico indirizzo IP privato** per immettere manualmente uno.

Fare clic su **Scegli un indirizzo IP pubblico**. Se è disponibile un indirizzo IP pubblico esistente è possibile accedere a questo punto, in questo scenario si crea un nuovo indirizzo IP pubblico. Fare clic su **Crea nuovo**

![Scegliere blade di indirizzo IP pubblico][6]

### <a name="step-7"></a>Passaggio 7

Quindi specificare un nome descrittivo per l'indirizzo IP pubblico e fare clic su **OK**

![Creare blade indirizzo IP pubblico][7]

### <a name="step-8"></a>Passaggio 8

Successivamente, impostare la configurazione di comunicare ascoltatore.  Se viene utilizzato **http** non viene lasciata per configurare e possibile fare clic su **OK** . Per utilizzare **https** ulteriormente la configurazione è necessaria.

Per utilizzare **https**, è necessario un certificato. È necessaria la chiave privata del certificato in modo che un file pfx esportare il certificato deve essere fornito e la password per il file.

Fare clic su **HTTPS**, fare clic sull'icona di **cartella** accanto alla casella di testo di **caricare PFX certificato** .
Passare al file di certificato PFX nel file system. Dopo aver selezionato, specificare un nome descrittivo per il certificato e digitare la password per il file. pfx.

Al termine fare clic su **OK** per rivedere le impostazioni per il Gateway di applicazione.

![Sezione Configurazione comunicare ascoltatore blade impostazioni][8]

### <a name="step-9"></a>Passaggio 9

Configurare le impostazioni specifiche **WAF** .

- **Stato del firewall** - questa impostazione attiva WAF o disattivato.
- **Modalità firewall** - questa impostazione determina che WAF azioni assume il traffico dannoso. Se si sceglie **il rilevamento** , il traffico solo ha eseguito l'accesso.  Se si sceglie **prevenzione** , il traffico è stato effettuato l'accesso e interrotto con un 403 accesso non autorizzato.

![impostazioni firewall dell'applicazione Web][9]

### <a name="step-10"></a>Passaggio 10

Rivedere la pagina di riepilogo e fare clic su **OK**.  Il gateway di applicazione è ora messe e creato.

### <a name="step-12"></a>Passaggio 12

Dopo aver creato il gateway di applicazione, passare a tale nel portale per continuare la configurazione del gateway dell'applicazione.

![Visualizzazione delle risorse di applicazione Gateway][10]

Questa procedura crea un gateway di applicazione di base con le impostazioni predefinite per comunicare ascoltatore, pool di back-end, le impostazioni http back-end e le regole. È possibile modificare queste impostazioni in base alla distribuzione dopo il provisioning ha esito positivo

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare la registrazione diagnostica per registrare gli eventi che vengono rilevati o impedire con Firewall di applicazione Web visitando [Applicazione Gateway diagnostica](application-gateway-diagnostics.md)

Informazioni su come creare le ricerche dello stato personalizzato visitando la [creazione di una verifica dell'integrità personalizzato](application-gateway-create-probe-portal.md)

Informazioni su come configurare SSL possibilità di scaricare ed eseguire la decrittografia SSL costosa disattivare il server web, visitare [Configurare trasferire SSL](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[1-1]: ./media/application-gateway-web-application-firewall-portal/figure1-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[4]: ./media/application-gateway-web-application-firewall-portal/figure4.png
[5]: ./media/application-gateway-web-application-firewall-portal/figure5.png
[6]: ./media/application-gateway-web-application-firewall-portal/figure6.png
[7]: ./media/application-gateway-web-application-firewall-portal/figure7.png
[8]: ./media/application-gateway-web-application-firewall-portal/figure8.png
[9]: ./media/application-gateway-web-application-firewall-portal/figure9.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png