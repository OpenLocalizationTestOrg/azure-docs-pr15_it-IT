<properties 
   pageTitle="Come impostare un indirizzo IP privato statico in modalità classica ausing CLI | Microsoft Azure"
   description="Informazioni sulle IP privato statico (DIP) e come possono essere gestiti in modalità classica utilizzando CLI"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-azure-cli"></a>Come impostare un indirizzo IP statico privato (classica) in CLI Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione classica. È inoltre possibile [gestire un indirizzo IP statico privato nel modello di distribuzione Manager delle risorse](virtual-networks-static-private-ip-arm-cli.md).

I comandi di Azure CLI di esempio seguenti si aspettano un ambiente semplice già stato creato. Se si desidera eseguire i comandi così come sono visualizzati nel documento, creare innanzitutto l'ambiente di testing descritto in [creazione di un vnet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Come specificare un indirizzo IP statico privato durante la creazione di una macchina virtuale
Per creare una nuova macchina virtuale denominata *DNS01* in un nuovo servizio cloud denominato *TestService* in base allo scenario precedente, procedere come segue:

1. Se non è mai utilizzato CLI Azure, vedere [installare e configurare CLI Azure](../xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.
1. Eseguire il comando **creazione servizio azure** per creare il servizio cloud.

        azure service create TestService --location uscentral

    Output previsto:

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
    
2. Eseguire il comando **azure creare macchine virtuali** per creare la macchina virtuale. Osservare il valore per un indirizzo IP statico privato. Elenco visualizzato dopo l'output illustra i parametri utilizzati.

        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd

    Output previsto:

        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK

    - **-l (o - posizione)**. Azure area geografica in cui verrà creata la macchina virtuale. Per questo scenario, *centralus*.
    - **-n (o - macchine virtuali nome)**. Nome della macchina virtuale da creare.
    - **-w (o --nome di rete virtuale)**. Nome del VNet in cui verrà creata la macchina virtuale. 
    - **-S (o - ip statico)**. Privato indirizzo IP statico per la macchina virtuale.
    - **TestService**. Nome del servizio cloud in cui verrà creata la macchina virtuale.
    - **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. Immagine utilizzata per creare la macchina virtuale.
    - **adminuser**. Amministratore locale per la macchina virtuale di Windows.
    - **AdminP@ssw0rd**. Password di amministratore locale per la macchina virtuale di Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Come recuperare statico privato indirizzo IP per una macchina virtuale
Per visualizzare le informazioni di indirizzo IP private statiche per macchine Virtuali creati con lo script precedente, eseguire il seguente comando CLI Azure e osservare il valore per *StaticIP di rete*:

    azure vm static-ip show DNS01

Output previsto:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Come rimuovere un indirizzo IP statico privato da una macchina virtuale
Per rimuovere l'indirizzo IP statico privato aggiunto al macchine Virtuali di script riportati sopra, eseguire il comando CLI Azure seguente:
    
    azure vm static-ip remove DNS01

Output previsto:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Come aggiungere un indirizzo IP privato statico a una macchina virtuale esistente
Per aggiungere un statico privato indirizzo IP in macchine Virtuali creati con lo script sopra Run il seguente comando:

    azure vm static-ip set DNS01 192.168.1.101

Output previsto:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sugli indirizzi [IP pubblico riservato](virtual-networks-reserved-public-ip.md) .
- Informazioni sugli indirizzi [IP pubblico (ILPIP) a livello di istanza](virtual-networks-instance-level-public-ip.md) .
- Consultare le [API REST IP riservato](https://msdn.microsoft.com/library/azure/dn722420.aspx).
