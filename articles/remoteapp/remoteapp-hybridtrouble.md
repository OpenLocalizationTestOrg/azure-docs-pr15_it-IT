
<properties
    pageTitle="Risolvere i problemi di creazione RemoteApp ibrido raccolte | Microsoft Azure"
    description="Informazioni su come risolvere i problemi di errori di creazione di RemoteApp ibrido insieme"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-azure-remoteapp-hybrid-collections"></a>Risolvere i problemi di creazione Azure RemoteApp ibrido raccolte

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Un insieme di ibrida è ospitato e archivia i dati del cloud Azure, ma anche dati di access consente agli utenti le risorse archiviate nella rete locale. Gli utenti possono accedere App effettuando l'accesso con le proprie credenziali aziendale sincronizzati o federato con Azure Active Directory. È possibile distribuire un insieme di ibrida che utilizza una rete virtuale Azure esistente oppure è possibile creare una nuova rete virtuale. È consigliabile creare o usare una subnet virtuali con un intervallo CIDR sufficiente per la crescita prevista per RemoteApp di Azure.

Per creare la raccolta ancora? Vedere [creare un insieme di ibrido](remoteapp-create-hybrid-deployment.md) per i passaggi necessari.

Se si verificano problemi durante la creazione della raccolta o se la raccolta non funziona nel modo si ritiene che dovrebbe, verificare le informazioni seguenti.

## <a name="your-image-is-invalid"></a>L'immagine non è valido ##
Se viene visualizzato un messaggio come "GoldImageInvalid" quando si è in attesa per Azure effettuare il provisioning della raccolta, significa che l'immagine di modello non soddisfa [definito requisiti immagine](remoteapp-imagereqs.md). Accedere e quindi leggere tali [requisiti](remoteapp-imagereqs.md), correggere l'immagine e provare a creare la raccolta.



## <a name="does-your-vnet-have-network-security-groups-defined"></a>Il VNET dispone di gruppi di sicurezza di rete definiti? ##
Se si dispone di gruppi di sicurezza di rete definiti nella subnet in uso per la raccolta, verificare che siano accessibili dall'interno subnet questi [URL e porte](remoteapp-ports.md) .

È possibile aggiungere gruppi di sicurezza di rete aggiuntive per le macchine virtuali distribuite dall'utente alla subnet per un controllo maggiore.

## <a name="are-you-using-your-own-dns-servers-and-are-they-accessible-from-your-vnet-subnet"></a>Si sta utilizzando server DNS? E sono accessibili da subnet VNET? ##
>[AZURE.NOTE] È necessario verificare che siano sempre i server DNS il VNET e sempre possibile risolvere macchine virtuali ospitate nel VNET. Non usare Google DNS per l'oggetto.


Per gli insiemi di ibrido utilizzare server DNS. Specificare le nello schema di configurazione della rete o tramite il portale di gestione quando si crea la rete virtuale. Nell'ordine in cui sono specificati in modo failover (anziché circolari) vengono utilizzati i server DNS.  
Consultare [La risoluzione dei nomi per macchine virtuali e istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) per assicurarsi che i server DNS siano configurati correcly.

Verificare che i server DNS per la raccolta siano accessibili e disponibile da subnet VNET specificato per la raccolta.

Per esempio:

    <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
    </VirtualNetworkConfiguration>

![Definire il DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

## <a name="are-you-using-an-active-directory-domain-controller-in-your-collection"></a>Si sta utilizzando un controller di dominio Active Directory nella raccolta? ##
Al momento solo un dominio di Active Directory può essere associato RemoteApp di Azure. La raccolta ibrido supporta solo gli account Azure Active Directory che sono stati sincronizzati strumento DirSync da una distribuzione di Windows Server Active Directory; in particolare, o sincronizzate con l'opzione di sincronizzazione delle Password sincronizzato con la federazione di Active Directory Federation Services (ADFS) configurata. È necessario creare un dominio personalizzato che soddisfa il suffisso UPN per il dominio locale e impostare l'integrazione di directory.

Per ulteriori informazioni, vedere [Configurazione di Active Directory per RemoteApp di Azure](remoteapp-ad.md) .

Verificare che i dettagli dominio forniti sono validi e controller di dominio sia raggiungibile da macchine Virtuali creati in subnet usata per App Remote Azure. Accertarsi che le credenziali dell'account di servizio forniti sono autorizzati ad aggiungere computer al dominio fornito e che il nome di Active Directory fornito può essere risolto da DNS cui la VNET.

## <a name="what-domain-name-did-you-specify-when-you-created-your-collection"></a>Specificare il nome di dominio se è stata specificata al momento della creazione della raccolta? ##

Il nome di dominio è stato creato o aggiunto deve essere un nome di dominio interno (non il nome di dominio Active Directory Azure) e deve essere in formato DNS essere risolto (contoso.local). Ad esempio, si dispone di un nome interno Active Directory (contoso.local) e un Active Directory UPN (contoso.com), è necessario usare il nome interno quando si crea la raccolta.
