<properties 
   pageTitle="Sui dispositivi VPN per le connessioni da sito VPN Gateway per le reti virtuali Azure | Microsoft Azure"
   description="In questo articolo vengono illustrati i dispositivi VPN e parametri IPsec per le connessioni S2S VPN Gateway e vengono forniti collegamenti a esempi e istruzioni di configurazione."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
  tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/13/2016"
   ms.author="yushwang;cherylmc" />

# <a name="about-vpn-devices-for-site-to-site-vpn-gateway-connections"></a>Sui dispositivi VPN per le connessioni da sito VPN Gateway

Per configurare una connessione VPN a siti (S2S), è necessario un dispositivo VPN. Le connessioni al sito possono essere usate per creare una soluzione ibrida oppure ogni volta che si desidera una connessione protetta tra la rete locale e la rete virtuale. In questo articolo vengono illustrati i dispositivi VPN compatibili e parametri di configurazione. 

>[AZURE.NOTE] Quando si configura una connessione a siti, è necessario per il dispositivo VPN un indirizzo di indirizzi IPv4 pubblico.                                                                                                                                                                               

Se il dispositivo non compare nella tabella [dispositivi convalidati VPN](#devicetable) , vedere la sezione [dispositivi Non convalidato VPN](#additionaldevices) di questo articolo. È possibile che il dispositivo potrebbe funzionare correttamente con Azure. Supporto per i VPN dispositivi, contattare il produttore del dispositivo.

**Elementi da tenere presente quando si visualizzano le tabelle:**

- È stata apportata una modifica della terminologia per il routing della statiche e dinamiche. Si verificheranno probabile che entrambi i termini. Nessuna modifica funzionalità, solo i nomi da modificare.
    - Il Routing statico = PolicyBased
    - Il Routing dinamico = RouteBased
- Specifiche per gateway VPN prestazioni elevate e gateway RouteBased VPN sono gli stessi se non diversamente specificato. Ad esempio, i dispositivi VPN convalidati compatibili con gateway RouteBased VPN anche sono compatibili con il gateway di Azure ad alta prestazioni VPN. 


## <a name="devicetable"></a>Dispositivi VPN convalidati 

Abbiamo abbiamo convalidati un set di dispositivi VPN standard in collaborazione con fornitori di dispositivo. Tutti i dispositivi in famiglie di dispositivo contenute nell'elenco seguente devono usare gateway VPN Azure. Vedere [Su Gateway VPN](vpn-gateway-about-vpngateways.md) per verificare il tipo di gateway che è necessario creare per la soluzione che si desidera configurare. 

Per informazioni su come configurare il dispositivo VPN, fare clic sui collegamenti che corrispondono a famiglia dispositivo appropriato. Supporto per i VPN dispositivi, contattare il produttore del dispositivo.



| **Fornitore**                      | **Famiglia di dispositivo**                                        | **Versione minima del sistema operativo**                             | **PolicyBased**                                                                                                                                                                                                             | **RouteBased**                                                                                                                                                                    |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Telesis delle                  | Router VPN serie AR                                    | 2.9.2                                              | Prossimamente                                                                                                                                                                                                                                          | Non è compatibile                                                                                                                                                                                               |
| Reti barracuda, Inc.        | Serie con barracuda NextGen Firewall F             | PolicyBased: 5.4.3, RouteBased: 6.2.0  | [Istruzioni di configurazione](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) | [Istruzioni di configurazione](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW)                                                                                                                                                                                              |
| Reti barracuda, Inc.        |  Serie di NextGen Firewall X barracuda                 | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Non è compatibile                                                                                                                                                                                               |
| Con ricamo                         | VRouter Vyatta 5400                                      | GA 6.6R3 Router virtuale                            | [Istruzioni di configurazione](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html)                                       | Non è compatibile                                                                                                                                                                                               |
| Punto di controllo                     | Gateway di protezione                                         | R75.40, R75.40VS                                     | [Istruzioni di configurazione](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275)                                         | [Istruzioni di configurazione](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco                           | ASA                                                      | 8.3                                                | [Esempi di Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA)                                                                                                                                                                        | Non è compatibile                                                                                                                                                                                               |
| Cisco                           | RIPRISTINO AUTOMATICO DI SISTEMA                                                      | IOS 15.1 (PolicyBased), IOS 15.2 (RouteBased)                | [Esempi di Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                                                        | [Esempi di Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                 |
| Cisco                           | ISR                                                      | IOS 15.0 (PolicyBased), IOS 15.1 (RouteBased *)               | [Esempi di Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                                                        | [Esempi di Cisco *](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                |
| Citrix                          | VPX NetScaler MPX, SDX,      |10,1 e versioni successive                                           | [Istruzioni di integrazione](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html)                                                                                                                                                                            | Non è compatibile                                                                                                                                                                                               |
| Dell SonicWALL                  | Serie TZ, NSA, SuperMassive serie, E classe NSA serie | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6. x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646 )          | [Istruzioni - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Istruzioni - SonicOS 5,9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                   | [Istruzioni - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Istruzioni - SonicOS 5,9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                                                                      |
| F5                              | Serie di indirizzi IP di grande                                 |           12.0                                            | [Istruzioni di configurazione](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip)                                                                                                                                                                          | [Istruzioni di configurazione](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling)                                                                                                                                                                                         |
| Fortinet                        | FortiGate                                                | FortiOS 5.2.7                                      | [Istruzioni di configurazione](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                                                          | [Istruzioni di configurazione](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                  |
| Giappone iniziativa Internet (IIJ) | Serie SEIL                                              | SEIL / X 4.60 4.60 SEIL/B1, SEIL/x86 3,20            | [Istruzioni di configurazione](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf)                                                                                                                                                                   | Non è compatibile                                                                                                                                                                                               |
| Juniper                         | SRX                                                      | JunOS 10.2 (PolicyBased) JunOS 11.4 (RouteBased)            | [Esempi di Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                                                                      | [Esempi di Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                              |
| Juniper                         | Serie di J                                                 | JunOS 10.4r9 (PolicyBased) JunOS 11.4 (RouteBased)          | [Esempi di Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                                                                 | [Esempi di Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                         |
| Juniper                         | ISG                                                      | ScreenOS 6.3 (PolicyBased e RouteBased)                  | [Esempi di Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                                                                      | [Esempi di Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                              |
| Juniper                         | SSG                                                      | ScreenOS 6.2 (PolicyBased e RouteBased)                  | [Esempi di Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                                                                      | [Esempi di Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                              |
| Microsoft                       | Routing servizio e accesso remoto                        | Windows Server 2012                                | Non è compatibile                                                                                                                                                                                                                                       | [Esempi di Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761)                                                                                           |
| Apri sistemi AG | Controllo della missione Gateway di protezione | N/D | [Guida all'installazione](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) | [Guida all'installazione](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan                        | Openswan                                                 | 2.6.32                                             | (Disponibile a breve)                                                                                                                                                                                                                                        | Non è compatibile                                                                                                                                                                                               |
| Reti Palo Alto              | Tutti i dispositivi che eseguono operativi PAN     | Operativi PAN 6.1.5 o versioni successive (PolicyBased) operativi PAN 7.0.5 o versioni successive (RouteBased)       | [Istruzioni di configurazione]( https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065)                                                                                                                                                                                          | [Istruzioni di configurazione](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340)                                                                                                                                                                                    |
| WatchGuard                      | Tutti                                                      | Fireware XTM v11.x                                 | [Istruzioni di configurazione](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/)                                                                                                                                                                          | Non è compatibile                                                                                                                                                                                               |

(*) Serie 7200 ISR router supportano solo PolicyBased VPN.

## <a name="additionaldevices"></a>Non convalidato dispositivi VPN

Se il dispositivo elencato nella tabella dispositivi convalidati VPN non è visibile, è comunque possibile funzioni con una connessione a siti. Verificare che il dispositivo VPN soddisfi i requisiti minimi descritti nella sezione requisiti del Gateway dell'articolo [Su gateway VPN](vpn-gateway-about-vpngateways.md#gateway-requirements) . Dispositivi che soddisfi i requisiti minimi anche funziona bene con gateway VPN. Per ulteriori istruzioni di configurazione e supporto tecnico, contattare il produttore del dispositivo.


## <a name="editing-device-configuration-samples"></a>Esempi di configurazione di dispositivi di modifica

Dopo avere scaricato l'esempio di configurazione di dispositivi VPN fornito, è necessario sostituire alcuni dei valori in modo da rispecchiare le impostazioni per l'ambiente. 

**Per modificare un campione:**

1. Aprire l'esempio utilizzando il blocco note. 
1. Cercare e sostituire tutte le stringhe <*testo*> con i valori relativi al proprio ambiente. Assicurarsi di includere < e >. Quando viene specificato un nome, il nome che scelto deve essere univoco. Se un comando non funziona, consultare la documentazione produttore del dispositivo.

| **Testo di esempio**                | **Passare alla**                                                                                                        |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP_OnPremisesNetwork&gt;           | Il nome scelto per l'oggetto. Esempio: myOnPremisesNetwork                                                       |
| &lt;RP_AzureNetwork&gt;                | Il nome scelto per l'oggetto. Esempio: myAzureNetwork                                                            |
| &lt;RP_AccessList&gt;                  | Il nome scelto per l'oggetto. Esempio: myAzureAccessList                                                         |
| &lt;RP_IPSecTransformSet&gt;           | Il nome scelto per l'oggetto. Esempio: myIPSecTransformSet                                                       |
| &lt;RP_IPSecCryptoMap&gt;              | Il nome scelto per l'oggetto. Esempio: myIPSecCryptoMap                                                          |
| &lt;SP_AzureNetworkIpRange&gt;         | Specificare l'intervallo. Esempio: 192.168.0.0                                                                                  |
| &lt;SP_AzureNetworkSubnetMask&gt;      | Specificare la maschera di subnet. Esempio: 255.255.0.0                                                                            |
| &lt;SP_OnPremisesNetworkIpRange&gt;    | Specificare l'intervallo locale. Esempio: 10.2.1.0                                                                         |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; | Specificare maschera subnet locale. Esempio: 255.255.255.0                                                              |
| &lt;SP_AzureGatewayIpAddress&gt;       | Queste informazioni specifiche per la rete virtuale e si trova nel portale di gestione come **indirizzo IP del Gateway**. |
| &lt;SP_PresharedKey&gt;                | Queste informazioni sono specifiche per la rete virtuale e si trovano nel portale di gestione come gestire chiave.          |



## <a name="ipsec-parameters"></a>Parametri IPsec

>[AZURE.NOTE] Anche se i valori elencati nella tabella seguente sono supportati dal Gateway VPN Azure, attualmente non è in alcun modo in cui specificare o selezionare una combinazione specifica dal Gateway VPN Azure. È necessario specificare i vincoli dal dispositivo VPN locale. Inoltre, è necessario morsa MSS in 1350.

### <a name="ike-phase-1-setup"></a>Configurazione di IKE fase 1

| **Proprietà**                                       | **PolicyBased** | **Gateway RouteBased e Standard o VPN prestazioni elevate** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| Versione IKE                                        | IKEv1                          | IKEv2                                                            |
| Gruppo Diffie-Hellman                               | Gruppo 2 (1024 bit)             | Gruppo 2 (1024 bit)                                               |
| Metodo di autenticazione                              | Chiave già condivisa                 | Chiave già condivisa                                                   |
| Algoritmi di crittografia                              | AES256 AES128 3DES             | 3DES AES256                                                      |
| Algoritmo                                  | SHA1(SHA128)                   | SHA1(SHA128), SHA2(SHA256)                                                     |
| Fase 1 protezione associazioni durata (orario) | 28.800 secondi                 | secondi 10,800                                                   |


### <a name="ike-phase-2-setup"></a>Configurazione di IKE fase 2

| **Proprietà**                                                             | **PolicyBased**                 | **Gateway RouteBased e Standard o VPN prestazioni elevate**   |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| Versione IKE                                                              | IKEv1                                          | IKEv2                                                              |
| Algoritmo                                                        | SHA1(SHA128)                                   | SHA1(SHA128)                                                       |
| Fase 2 associazioni di protezione durata (orario)                        | 3.600 secondi                                  | 3.600 secondi                                                                  |
| Fase 2 associazioni di protezione durata (velocità)                  | KB 102,400,000                                 | -                                                                  |
| Crittografia SA IPsec e l'autenticazione offre (in ordine di preferenza) | 1. AES256 ESP 2. ESP-AES128 3. ESP-3DES 4. N/D | Vedere *RouteBased Gateway IPsec associazione di protezione () offre* (sotto) |
| Segreto inoltro perfetto (questa opzione)                                            | No                                             | Nessuna (*)|
| Il rilevamento Peer                                                      | Non è supportata                                  | Supportati                                                          |

(*) Azure Gateway come risponditore IKE può accettare questa opzione DH gruppo 1, 2, 5, 14, 24.

### <a name="routebased-gateway-ipsec-security-association-sa-offers"></a>Offre Gateway RouteBased IPsec associazione di protezione)

Nella tabella seguente sono elencati crittografia SA IPsec e offre l'autenticazione. Offerte sono elencate l'ordine di preferenza che presentata o accettato l'offerta.

| **Offerte di autenticazione e la crittografia SA IPsec** | **Azure Gateway come ruolo iniziale**                               | **Azure Gateway come risponditore**                                   |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1                                                 | ESP AES_256 SHA                                              | ESP AES_128 SHA                                              |
| 2                                                 | ESP AES_128 SHA                                              | MD5 3_DES ESP                                                |
| 3                                                 | MD5 3_DES ESP                                                | ESP 3_DES SHA                                                |
| 4                                                 | ESP 3_DES SHA                                                | SHA1 AH con ESP AES_128 con HMAC null                      |
| 5                                                 | SHA1 AH con ESP AES_256 con HMAC null                      | SHA1 AH con ESP 3_DES con HMAC null                        |
| 6                                                 | SHA1 AH con ESP AES_128 con HMAC null                      | MD5 AH con ESP 3_DES con HMAC null, senza durata proposta |
| 7                                                 | SHA1 AH con ESP 3_DES con HMAC null                        | SHA1 AH con ESP 3_DES SHA1, senza durata                    |
| 8                                                 | MD5 AH con ESP 3_DES con HMAC null, senza durata proposta | MD5 AH con ESP 3_DES MD5, senza durata                     |
| 9                                                 | SHA1 AH con ESP 3_DES SHA1, senza durata                    | ESP DES MD5                                                  |
| 10                                                | MD5 AH con ESP 3_DES MD5, senza durata                     | ESP DES SHA1, senza durata                                   |
| 11                                                | ESP DES MD5                                                  | Nessuna durata come previsto SHA1 con HMAC null ESP DES, proposta        |
| 12                                                | ESP DES SHA1, senza durata                                   | Nessuna durata come previsto MD5 con HMAC null ESP DES, proposta        |
| 13                                                | Nessuna durata come previsto SHA1 con HMAC null ESP DES, proposta        | SHA1 AH con ESP DES SHA1, senza durata                      |
| 14                                                | Nessuna durata come previsto MD5 con HMAC null ESP DES, proposta        | MD5 AH con ESP DES MD5, senza durata                       |
| 15                                                | SHA1 AH con ESP DES SHA1, senza durata                      | ESP SHA, senza durata                                        |
| 16                                                | MD5 AH con ESP DES MD5, senza durata                       | ESP MD5, senza durata                                        |
| 17                                                | -                                                            | Come previsto SHA, senza durata                                         |
| 18                                                | -                                                            | MD5 AH, senza durata                                        |


- È possibile specificare la crittografia IPsec ESP NULL con gateway RouteBased e VPN prestazioni elevato. Crittografia base non offre una protezione ai dati in transito e deve essere utilizzata solo quando massima produttività e minimo latenza è necessaria.  I clienti possono scegliere di utilizzare questo in scenari di comunicazione VNet a VNet o la crittografia viene applicata in un punto della soluzione.

- Per la connessione tra locale tramite Internet, utilizzare le impostazioni del gateway VPN Azure predefinito con hashing algoritmi elencati nelle tabelle precedenti, per garantire la sicurezza delle comunicazioni critiche e la crittografia.





