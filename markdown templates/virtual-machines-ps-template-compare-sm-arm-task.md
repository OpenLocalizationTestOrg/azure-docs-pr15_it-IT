<!--save a copy of this file to your local repo. It's important that you follow the naming conventions by starting with the service name, and use lowercase only for the file name. See "file-names-and-locations.md" under the "contributor-guide" folder in your repo.

Info to help you use the template are enclosed in the Markdown comments using the caret, hyphen, dash syntax. Delete these from your file.

Text not wrapped in comment syntax is intended to be used as is, or with updates enclosed in [  ]. Add the info and delete the bracket. 

Pay attention to spacing and indents. They affect formatting. 

--> 

<!--replace this with Properties and Tags sections. These are required sections. See "article-metadata.md" in under the "contributor-guide" folder in your repo. Attributes in each section can be placed on separate lines to make them easier to read and check-->

# <a name="use-azure-powershell-to-task"></a>Usare PowerShell Azure a [attività]

In questo articolo viene illustrato come [attività], usando i comandi da Azure modulo e il modulo di gestione delle risorse di Azure. Si tratta per acquisire i nuovi comandi, oltre a eseguire la migrazione di script esistenti per i nuovi comandi.

## <a name="prerequisite-install-a-recent-version-of-azure-powershell"></a>Prerequisito: Installare una versione recente di PowerShell Azure

Se già fatto, installare almeno la versione [numero di versione] di Azure PowerShell nel computer locale. Se si usa una versione precedente, non dispone dei cmdlet Gestione risorse Azure descritti in questo articolo. Per informazioni dettagliate, vedere:
 
- Informazioni su [come installare e configurare Azure PowerShell](install-configure-powershell.md) per istruzioni sulla configurazione di Azure PowerShell.
- [Uso di Windows PowerShell con Gestione risorse](powershell-azure-resource-manager.md) per nozioni fondamentali sull'uso di Manager delle risorse.

> [AZURE.NOTE] La maggior parte delle attività è necessario utilizzare un prompt dei comandi di PowerShell Azure livello di amministratore.

## <a name="command-comparison"></a>Confronto di comando

Questo [tabella | sezione] viene illustrata la sintassi di comando.

<!--[optional image - to use an image in this article, add a folder with the same name as the article file name without extension, inside the Media folder of the repo. Use only this folder to store the images. Don't attempt to use a common folder to share images you want to use in more than 1 file.]
Then, use the following syntax to add a reference to the image in your article:
![](./media/name-of-file-without-extension/image-name-no-spaces.png)
-->

<!--if a command string uses variables, define the variables first, using the  following construction. In some cases the variable is straightforward and doesn't need much explanation. But parameters such as location and size can benefit from brief explanation or listing all accepted values:--> 

In questi esempi di comando utilizzano le variabili seguenti:

$FriendlyName"<Describe value>"

<!-- if it makes more sense to present this in a table, use this. Otherwise, delete. The table won't render until it's in Github or published to Sandbox.-->

Gestione dei servizi | Manager delle risorse
---|----
`syntax` | `syntax`


<!--if it makes more sense to present this one command block after the other instead of a table, use this. Otherwise, delete-->
  
[Frase breve introduzione sul comando. Omettere se non è semplicemente da trattare. Ma se utilizza approcci ad esempio una pipeline di spiegare che]:

    [command string]

## <a name="script-examples"></a>Esempi di script

Ecco un esempio di utilizzo [nomi di cmdlet]) a [attività]. Include i comandi che:

- [verbo breve, utilizzato, sono previsti, è e così via]
- [successivo breve verbo] 

<!--include this statement if it uses variables that weren't introduced earlier-->Sono incluse le variabili seguenti:

- [variabile 1]
- [variabile 2]

<!--This shows you how a recent example was presented as well as how it was formatted. Preceding each line with one tab or four spaces to format in a code block-->

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
    
    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"
    
    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4
    
    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching
    
    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="additional-resources"></a>Risorse aggiuntive
<!--At a minimum, include a link back to the migration task list article. Use the formats shown below. See create-links-markdown.md for more info -->
<!--use this format for links to other articles, such as the migration task list. -->
[Gestire la disponibilità](virtual-machines-windows-manage-availability.md)

<!--To link to an ACOM page outside the /documentation/ subdomain (such as a pricing page, SLA page or anything else that is not a documentation article), use an absolute URL, but omit the locale:

    [link text](http://azure.microsoft.com/pricing/details/virtual-machines/)-->

<!--use this for URLs outside of ACOM. Be sure to locale, and if you're linking to the Azure library on MSDN, include the '/azure/' part of the URL-->
[Documentazione di macchine virtuali](https://msdn.microsoft.com/library/azure/jj156003.aspx)

