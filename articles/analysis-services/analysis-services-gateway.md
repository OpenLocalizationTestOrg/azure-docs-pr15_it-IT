<properties
   pageTitle="Gateway di dati in locale | Microsoft Azure"
   description="Un gateway via locale è necessario se il server Analysis Services in Azure consentirà di connettersi a origini dati locali."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="on-premises-data-gateway"></a>Gateway di dati in locale

Il gateway di dati in locale funge da bridge, fornendo trasferimento la protezione dei dati tra origini dati locali e il server Azure Analysis Services nel cloud.

Un gateway è installato in un computer della rete. Per ogni server Azure Analysis Services che sono nell'abbonamento Azure, è necessario installare un gateway. Ad esempio, se si dispone di due server nell'abbonamento Azure in grado di connettersi a origini dati locali, un gateway deve essere installato in due computer diversi della rete.

## <a name="requirements"></a>Requisiti

**Requisiti minimi:**

- 4.5 .NET framework
- versione a 64 bit di Windows 7 / Windows Server 2008 R2 (o versioni successive)

**Consigliata:**

- CPU core 8
- 8 GB di memoria
- versione a 64 bit di Windows 2012 R2 (o versioni successive)

**Considerazioni importanti:**

- Non è installato il gateway su un controller di dominio.

- Solo un gateway può essere installato in un unico computer.

- Installare il gateway su un computer che rimanga attivate e non va allo stato di sospensione. Se non si trova nel computer, il server Azure Analysis Services non è possibile connettersi a origini dati locali per aggiornare i dati.

- Non è possibile installare il gateway in un computer wireless connesso alla rete. Possono essere ridotta, le prestazioni.

- Per modificare il nome del server per un gateway che è già stato configurato, è necessario reinstallare e configurare un nuovo gateway.

- In alcuni casi, modelli tabulari di connessione a origini dati tramite provider nativi, ad esempio SQL Server Native Client (SQLNCLI11) potrebbero restituire un errore. Per ulteriori informazioni, vedere [connessioni di origine dati](analysis-services-datasource.md).

## <a name="supported-on-premises-data-sources"></a>Origini dati locali supportati
Per l'anteprima, il gateway supporta connessioni tra server Azure Analysis Services e le origini dati locali seguenti:

- SQL Server
- SQL Data Warehouse
- PUNTI DI ACCESSO
- Oracle
- Teradata


## <a name="download"></a>Download
 [Scaricare il gateway](https://aka.ms/azureasgateway)


## <a name="install-and-configure"></a>Installare e configurare

1. Eseguire l'installazione.

2. Scegliere un percorso di installazione e accettare le condizioni di licenza.

3. Accedere a Azure.

4. Specificare il nome del Server di analisi di Azure. È possibile specificare solo un server per ogni gateway. Fare clic su **Configura** e si è possibile continuare.

    ![accedere a azure](./media\analysis-services-gateway\aas-gateway-configure-server.png)


## <a name="how-it-works"></a>Come funziona
Il gateway viene eseguito come un servizio di Windows, **gateway di dati in locale**in un computer nella rete dell'organizzazione. Il gateway che installare per l'uso con Azure Analysis Services si basa sullo stesso gateway utilizzato per altri servizi come Power BI, ma con alcune differenze in come configurarlo.

![Come funziona](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Lavoro flusso query e dati alla seguente:

1.  Viene creata una query dal servizio cloud con le credenziali crittografate per l'origine dati locale. Viene quindi inviato a una coda per il gateway per l'elaborazione.

2.  Il servizio cloud di gateway analizza la query e inserisce la richiesta al [Bus di servizio Azure](https://azure.microsoft.com/documentation/services/service-bus/).

3.  Il gateway di dati in locale polling Bus di servizio Azure per le richieste in sospeso.

4.  Il gateway Ottiene la query, decrittografa le credenziali e connessione alle origini dati con le credenziali.

5.  Il gateway invia la query all'origine dati per l'esecuzione.

6.  I risultati vengono inviati dall'origine dati al gateway, quindi su servizio cloud.

## <a name="windows-service-account"></a>Account del servizio Windows

Il gateway di dati in locale è configurato per l'utilizzo *SERVICE\PBIEgwService NT* per le credenziali di accesso del servizio di Windows. Per impostazione predefinita, ha il diritto di accesso come servizio; nel contesto del computer in cui si sta installando il gateway in. Questo credenziali non sono lo stesso account utilizzato per connettersi a origini dati locali o il proprio account Azure.  

Se si verificano problemi con il server proxy a causa di autenticazione, può essere necessario cambiare l'account di servizio Windows a un utente di dominio o gestito degli account del servizio.

## <a name="ports"></a>Porte

Il gateway consente di creare una connessione in uscita a Bus di servizio Azure. Comunica su porte in uscita: TCP 443 (impostazione predefinita), 5671, 5672, 9350 tramite 9354.  Il gateway non richiede porte in ingresso.

Si consiglia di proprietà consentite gli indirizzi IP per l'area dati nel firewall. È possibile scaricare l' [elenco di indirizzi IP di Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653). In questo elenco viene aggiornato ogni settimana.

> [AZURE.NOTE]  Indirizzi IP elencati nell'elenco indirizzi IP di Azure Data Center sono in notazione CIDR. Ad esempio 10.0.0.0/24 non significa 10.0.0.0 tramite 10.0.0.24. Ulteriori informazioni sulla [notazione CIDR](http://whatismyipaddress.com/cidr).

Di seguito sono i nomi di dominio completo utilizzati dal gateway.

|Nomi di dominio|Porte in uscita|Descrizione|
|---|---|---|
|*. powerbi.com|80|HTTP utilizzato per scaricare il programma di installazione.|
|*. powerbi.com|443|HTTPS|
|*. analysis.windows.net|443|HTTPS|
|*. login.windows.net|443|HTTPS|
|*. servicebus.windows.net|5671 5672|Avanzate Accodamento Protocol (AMQP)|
|*. servicebus.windows.net|443, 9350 9354|Listener nel servizio Bus inoltro su TCP (richiede 443 per l'acquisizione di token di controllo dell'accesso)|
|*. frontend.clouddatahub.net|443|HTTPS|
|*. core.windows.net|443|HTTPS|
|Login.microsoftonline.com|443|HTTPS|
|*. msftncsi.com|443|Consente di verificare la connettività internet se il gateway non è raggiungibile dal servizio Power BI.|
|*.microsoftonline p.com|443|Usato per l'autenticazione in base alla configurazione.|


### <a name="forcing-https-communication-with-azure-service-bus"></a>Forzare la comunicazione HTTPS con Bus di servizio Azure

È possibile forzare il gateway per comunicare con Bus di servizio Azure tramite HTTPS invece TCP diretto; Tuttavia, si possono rallentare le prestazioni. È necessario modificare il file *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* . Modificare il valore da `AutoDetect` a `Https`. In questo file si trova, per impostazione predefinita, su *gateway dati c:\Programmi\Microsoft Files\On locale*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```


## <a name="troubleshooting"></a>Risoluzione dei problemi
Dietro le quinte, il gateway di dati in locale utilizzato per la connessione di Azure Analysis Services per le origini dati locali è lo stesso gateway usato con Power BI.

Se si verificano problemi durante l'installazione e configurazione di un gateway, assicurarsi di vedere [risoluzione dei problemi del Gateway di Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem-tshoot/). Se si ritiene che si verificano problemi con il firewall, vedere le sezioni proxy o firewall.

Se si ritiene che si sta che si verifichino problemi di proxy, con il gateway, vedere [configurazione delle impostazioni del proxy per Power BI gateway](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy.md).

## <a name="next-steps"></a>Passaggi successivi
- [Gestire Analysis Services](analysis-services-manage.md)
- [Recuperare dati da Azure Analysis Services](analysis-services-connect.md)
