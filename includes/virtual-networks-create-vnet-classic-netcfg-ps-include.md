## <a name="how-to-create-a-vnet-using-a-network-config-file-from-powershell"></a>Come creare un VNet utilizzando un file di configurazione di rete di PowerShell

Azure utilizza un file xml per definire VNets tutti disponibili a un abbonamento. È possibile scaricare questo file e modificarlo per modificare o eliminare VNets esistente e crearne di nuovi. In questo documento verrà informazioni su come scaricare questo file definito come file di configurazione (o netcgf) rete e modificarlo per creare un nuovo VNet. Selezionare lo [schema di configurazione di Azure virtuali](https://msdn.microsoft.com/library/azure/jj157100.aspx) per altre informazioni sui file di configurazione della rete.

Per creare un VNet usando un file di netcfg con PowerShell, seguire la procedura seguente.

1. Se non è mai utilizzato Azure PowerShell, vedere [come installare e configurare PowerShell Azure](../articles/powershell-install-configure.md) e seguire le istruzioni fino alla fine per eseguire l'accesso a Azure e selezionare l'abbonamento.
2. Dalla console di PowerShell di Azure, utilizzare il cmdlet **Get-AzureVnetConfig** di scaricare il file di configurazione della rete eseguendo il comando seguente. 

        Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

    Output previsto:

        XMLConfiguration                                                                                                     
        ----------------                                                                                                     
        <?xml version="1.0" encoding="utf-8"?>...  

3. Aprire il file salvato nel passaggio 2 precedente utilizzando qualsiasi applicazione di editor XML o testo e cercare il **<VirtualNetworkSites>** elemento. Se si dispone di tutte le reti già state create, ogni rete verrà visualizzato come i propri **<VirtualNetworkSite>** elemento.
4. Per creare la rete virtuale descritta in questo scenario, aggiungere il seguente codice XML solo nel **<VirtualNetworkSites>** elemento:

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

9.  Salvare il file di configurazione della rete.
10. Dalla console di PowerShell di Azure, utilizzare il cmdlet **Set-AzureVnetConfig** per caricare il file di configurazione della rete eseguendo il comando seguente. Si noti l'output sotto il comando, dovrebbero essere visualizzati **completato** in **OperationStatus**. Se ovvero non il caso, selezionare il file xml per gli errori.

        Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

    Ecco l'output previsto per il comando precedente:

        OperationDescription OperationId                          OperationStatus
        -------------------- -----------                          ---------------
        Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
    
11. Dalla console di PowerShell di Azure, utilizzare il cmdlet **Get-AzureVnetSite** per verificare che la nuova rete è stato aggiunto il comando seguente. 

        Get-AzureVNetSite -VNetName TestVNet

    Ecco l'output previsto per il comando precedente:

        AddressSpacePrefixes : {192.168.0.0/16}
        Location             : Central US
        AffinityGroup        : 
        DnsServers           : {}
        GatewayProfile       : 
        GatewaySites         : 
        Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
        InUse                : False
        Label                : 
        Name                 : TestVNet
        State                : Created
        Subnets              : {FrontEnd, BackEnd}
        OperationDescription : Get-AzureVNetSite
        OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
        OperationStatus      : Succeeded