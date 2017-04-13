<properties
   pageTitle="Configurare un gateway di applicazione esistente per più siti nel portale di Azure | Microsoft Azure"
   description="Questa pagina vengono fornite istruzioni per configurare un gateway di applicazione Azure esistente per l'hosting più applicazioni web sullo stesso gateway con il portale di Azure."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>Configurare un gateway di applicazione esistente per l'hosting più applicazioni web

> [AZURE.SELECTOR]
- [Portale di Azure](application-gateway-create-multisite-portal.md)
- [Azure Manager delle risorse PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)

Hosting di siti più consente di distribuire più applicazioni web nel gateway applicazione stesso. Si basa sulla presenza di intestazione host nella richiesta HTTP in arrivo, per determinare quali comunicare ascoltatore riceverà il traffico. Comunicare ascoltatore quindi indirizza il traffico al pool di back-end appropriati come configurato nella definizione di regole del gateway. Nelle applicazioni web SSL abilitato, gateway applicazione si basa su estensione indicazione di nome Server (SNI) per scegliere comunicare ascoltatore corretto per il traffico web. Generalmente utilizzata per l'hosting di siti più consiste nel carico per i domini web diverso al pool di diversi server di back-end. Allo stesso modo più sottodomini dello stesso dominio radice possono essere inoltre ospitati nello stesso gateway dell'applicazione.

## <a name="scenario"></a>Scenario:

Nell'esempio seguente gateway applicazione serve il traffico verso contoso.com e fabrikam.com con due pool di server di back-end: contoso pool di server e il server di fabrikam. Configurazione simili potrebbe essere utilizzate per sottodomini host come app.contoso.com e blog.contoso.com.

![scenario multisito][multisite]

## <a name="before-you-begin"></a>Prima di iniziare

Questo scenario aggiunge il supporto di più siti un gateway di applicazioni esistente. Per completare questo scenario scenario un gateway di applicazioni esistente deve essere disponibile per configurare. Vedere [creare un gateway di applicazione tramite il portale](./application-gateway-create-gateway-portal.md) per imparare a creare un gateway di applicazione di base nel portale.

## <a name="requirements"></a>Requisiti

- **Pool di server di back-end:** Elenco di indirizzi IP di server back-end. Indirizzi IP elencati devono appartenere alla subnet virtuali o devono essere un IP/VIP pubblico. Nome di dominio completo può anche essere utilizzato.
- **Le impostazioni del pool di server di back-end:** Ogni pool ha impostazioni come porta, il protocollo e affinità basate su cookie. Queste impostazioni sono collegate a un pool e vengono applicate a tutti i server del pool.
- **Porta front-end:** Questa porta è la porta pubblica che viene aperta nel gateway dell'applicazione. Il traffico raggiunge la porta e quindi venga reindirizzato a uno dei server back-end.
- **Comunicare ascoltatore:** Comunicare ascoltatore dispone di una porta front-end, un protocollo (Http o Https, questi valori sono maiuscole e minuscole) e il nome del certificato SSL (se la configurazione di SSL trasferire). Per i gateway applicazione abilitata più siti, nome host e gli indicatori di SNI aggiunti anche.
- **Regola:** La regola associa comunicare ascoltatore, pool server di back-end e consente di definire quali pool di server di back-end il traffico deve essere indirizzato al quando arriva una particolare comunicare ascoltatore.
- **Certificati:** Ogni comunicare ascoltatore richiede un certificato univoco, in questo esempio vengono creati 2 listener per più siti. È necessario creare due PFX certificati e le password.

## <a name="create-an-application-gateway"></a>Creare un gateway di applicazioni

Di seguito sono i passaggi necessari per aggiornare il gateway di applicazione:

1. Creare pool di back-end da utilizzare per ogni sito.
2. Creare un nuovo listener per ogni gateway applicazione sito supporterà.
3. Creare regole per eseguire il mapping di ogni comunicare ascoltatore con back-end appropriato.

## <a name="create-back-end-pools-for-each-site"></a>Creare pool di back-end per ogni sito

Verrà tale gateway applicazione un pool di back-end per ogni sito di supporto è necessaria, in questo caso verrà creato, uno per contoso11.com e uno per fabrikam11.com 2.

### <a name="step-1"></a>Passaggio 1

Passare a un gateway di applicazioni esistente nel portale di Azure (https://portal.azure.com). Selezionare **il pool di back-end** e fare clic su **Aggiungi**

![aggiungere i pool di back-end][7]

### <a name="step-2"></a>Passaggio 2

Inserire le informazioni per il pool di back-end **pool1**, aggiunta di indirizzi ip o il FQDN per il server di back-end e fare clic su **OK**

![impostazioni di back-end del pool pool1][8]

### <a name="step-3"></a>Passaggio 3

Scegliere **Aggiungi** per aggiungere un pool di back-end aggiuntivi **pool2**, aggiunta di indirizzi ip o il FQDN per il server di back-end e il pool di back-end e fare clic su **OK**

![impostazioni di back-end del pool pool2][9]

### <a name="create-listeners-for-each-back-end"></a>Creare listener per ogni back-end

Gateway di applicazioni si basa su intestazioni host HTTP 1.1 per ospitare più di un sito Web stesso indirizzo IP pubblico e porta. Comunicare ascoltatore base creata nel portale non contiene questa proprietà.

### <a name="step-1"></a>Passaggio 1

Fare clic su **listener** nel gateway applicazione esistente e fare clic su **più siti** per aggiungere il primo listener.

![blade Panoramica listener][1]

### <a name="step-2"></a>Passaggio 2

Compilare le informazioni per comunicare ascoltatore, In questo esempio terminazione SSL è configurato, creare una nuova porta front-end. Caricare il certificato PFX da utilizzare per terminazione SSL. L'unica differenza in questo blade rispetto a e il comunicare ascoltatore base standard è il nome host.

![blade proprietà comunicare ascoltatore][2]

### <a name="step-3"></a>Passaggio 3

Fare clic su **più siti** e creare un'altra comunicare ascoltatore come descritto nel passaggio precedente per il secondo sito. Assicurarsi di usare un certificato diverso per comunicare ascoltatore secondo. L'unica differenza in questo blade rispetto a e il comunicare ascoltatore base standard è il nome host. Compilare le informazioni per comunicare ascoltatore e fare clic su **OK**.

![blade proprietà comunicare ascoltatore][3]

> [AZURE.NOTE] Creazione di listener nel portale di Azure per gateway applicazione è un'attività lunga, può richiedere alcuni istanti per creare due listener in questo scenario. Quando eseguire la presentazione listener nel portale, come illustrato nell'immagine seguente.

![Panoramica di comunicare ascoltatore][4]

### <a name="create-rules-to-map-listeners-to-backend-pools"></a>Creare regole per mappare listener pool di back-end

### <a name="step-1"></a>Passaggio 1

Passare a un gateway di applicazioni esistente nel portale di Azure (https://portal.azure.com). Selezionare **le regole** e scegliere esistente di regola predefinito **regola 1** e fare clic su **Modifica**.

### <a name="step-2"></a>Passaggio 2

Compilare e il regole, come illustrato nell'immagine seguente. Scegliere il primo comunicare ascoltatore e primo pool e fare clic su **Salva** al termine.

![Modifica regola esistente][6]

### <a name="step-3"></a>Passaggio 3

Fare clic su **regola di base** per creare la regola 2a. Compilare il modulo con la seconda comunicare ascoltatore e secondo pool di back-end e fare clic su **OK** per salvare.

![aggiungere blade regola di base][10]

È stata completata la configurazione di un gateway di applicazioni esistente con supporto per più siti tramite il portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come proteggere i siti Web con [Gateway applicazione - Firewall applicazione Web](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png