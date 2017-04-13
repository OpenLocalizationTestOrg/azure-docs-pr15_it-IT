<properties
   pageTitle="Gestire più ambienti nel servizio tessuti | Microsoft Azure"
   description="Le applicazioni di servizio tessuti possono essere eseguite sui cluster in un intervallo dimensioni da un computer a migliaia di computer. In alcuni casi, possibile configurare l'applicazione in modo diverso per i diversi ambienti. In questo articolo viene descritto come definire i parametri di applicazione diverso per ogni ambiente."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/19/2016"
   ms.author="seanmck"/>

# <a name="manage-application-parameters-for-multiple-environments"></a>Gestire i parametri di applicazione per gli ambienti più

È possibile creare cluster di Azure servizio tessuti utilizzando in un punto qualsiasi da uno a migliaia di computer. Durante l'esecuzione di file binari dell'applicazione possano senza alcuna modifica tra questa ampia gamma di ambienti, spesso si desidera configurare l'applicazione in modo diverso, a seconda del numero di computer che esegue la distribuzione.

Un semplice esempio valutare la possibilità di `InstanceCount` di un servizio senza informazioni sullo stato. Quando si eseguono applicazioni in Azure, è in genere che si desidera impostare questo parametro sul valore speciale-"1". In questo modo che il servizio sia in esecuzione su tutti i nodi del cluster. Questa configurazione non è adatta per un cluster di computer unico poiché non può contenere più processi ascolto lo stesso endpoint su un unico computer. Se, tuttavia, verrà in genere impostata `InstanceCount` su "1".

## <a name="specifying-environment-specific-parameters"></a>Specificare i parametri specifici dell'ambiente

Per risolvere questo problema di configurazione è un insieme di servizi parametri predefiniti e i file parametro applicazione compilare i valori di parametro per un determinato ambiente. Parametri di applicazioni e servizi predefiniti vengono configurati nei manifesti di servizi e applicazioni. Definizione dello schema per i file ServiceManifest.xml e ApplicationManifest.xml viene installata con il servizio tessuti SDK e strumenti per *C:\Programmi\Microsoft c:\Programmi\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Servizi predefiniti

Le applicazioni di servizio tessuti sono costituite da un insieme di istanze del servizio. Sebbene sia possibile creare un'applicazione vuota e quindi creare in modo dinamico tutte le istanze di servizio, la maggior parte delle applicazioni dispongono di un set di servizi di base che deve sempre essere creata quando si crea un'istanza dell'applicazione. Questi sono denominati "servizi predefiniti". Vengono specificati in manifesto dell'applicazione, a sinistra con segnaposto per una configurazione per ogni ambiente incluso tra parentesi quadre:

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type"
                TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
                MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]"
                    LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

Ognuno dei parametri denominati deve essere definito all'interno dell'elemento parametri del manifesto dell'applicazione:

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

L'attributo DefaultValue specifica il valore da utilizzare in assenza di un parametro più specifici per un determinato ambiente.

>[AZURE.NOTE] Non tutti i parametri di istanza di servizio sono adatti per una configurazione per ogni ambiente. Nell'esempio precedente, i valori LowKey e HighKey per schema di partizione del servizio definiti in modo esplicito per tutte le istanze del servizio poiché l'intervallo di partizione è una funzione del dominio di dati, non nell'ambiente.


### <a name="per-environment-service-configuration-settings"></a>Impostazioni di configurazione del servizio per ambiente

Il [modello di applicazione di servizio tessuti](service-fabric-application-model.md) consente ai servizi includere i pacchetti di configurazione che includono coppie di parole chiave valore personalizzati che siano leggibili in fase di esecuzione. I valori di queste impostazioni possono anche essere distinti dall'ambiente specificando un `ConfigOverride` nel manifesto dell'applicazione.

Si supponga di disporre le seguenti impostazioni nel file Config\Settings.xml per il `Stateful1` servizio:


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Per ignorare questo valore per una coppia di ambiente di applicazioni specifiche, creare un `ConfigOverride` quando si importa manifesto servizio nel manifesto dell'applicazione.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Questo parametro quindi può essere configurato dall'ambiente, come illustrato sopra. È possibile eseguire questa operazione dichiarazione nella sezione parametri del manifesto dell'applicazione e specificando valori specifici dell'ambiente nei file di parametro dell'applicazione.

>[AZURE.NOTE] In caso di impostazioni di configurazione del servizio, sono disponibili tre posizioni in cui è possibile impostare il valore di una chiave: il pacchetto di configurazione del servizio, manifesto dell'applicazione e il file di parametro dell'applicazione. Servizio tessuti verrà sempre scelto dal file di parametro dell'applicazione prima (se specificato), quindi manifesto dell'applicazione e infine il pacchetto di configurazione.


### <a name="application-parameter-files"></a>File di parametro dell'applicazione

Il progetto di applicazione di servizio tessuti può includere uno o più file di parametro applicazione. Ognuna di esse definisce i valori specifici per i parametri definiti nel manifesto dell'applicazione:

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

Per impostazione predefinita, una nuova applicazione include due file parametro applicazione, denominati Local.xml e Cloud.xml:

![File di parametro dell'applicazione in Esplora soluzioni][app-parameters-solution-explorer]

Per creare un nuovo file di parametro, è sufficiente copiare e incollare uno esistente e assegnare un nuovo nome.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Identificazione dei parametri specifici dell'ambiente durante la distribuzione

In fase di distribuzione, è necessario scegliere il file di parametro appropriato per applicare l'applicazione. È possibile eseguire questa operazione tramite la finestra di dialogo Pubblica in Visual Studio o tramite PowerShell.

### <a name="deploy-from-visual-studio"></a>Distribuire da Visual Studio

È possibile scegliere dall'elenco dei file dei parametri disponibili quando si pubblica l'applicazione in Visual Studio.

![Scegliere un file di parametri nella finestra di dialogo Pubblica][publishdialog]

### <a name="deploy-from-powershell"></a>Distribuire da PowerShell

Il `Deploy-FabricApplication.ps1` script di PowerShell incluso nel modello di progetto di applicazione accetta un profilo di pubblicazione come parametro e la PublishProfile contiene un riferimento al file di parametri dell'applicazione.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su alcuni dei concetti di base descritti in questo argomento, vedere la [Panoramica tecnica servizio tessuti](service-fabric-technical-overview.md). Per informazioni su altre funzionalità di gestione di app disponibili in Visual Studio, vedere [gestire le applicazioni di servizio tessuti in Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
