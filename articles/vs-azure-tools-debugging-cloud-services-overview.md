<properties 
   pageTitle="Il debug di servizi Cloud Azure | Microsoft Azure"
   description="Il debug di servizi Cloud Azure"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="debugging-cloud-services"></a>Il debug di servizi cloud

È possibile utilizzare diversi approcci per eseguire il debug di un'applicazione Azure con gli strumenti di Azure per Microsoft Visual Studio e Azure SDK:

- È possibile eseguire il debug un'applicazione Azure da Visual Studio quando si sviluppa, come si farebbe qualsiasi applicazione c# o Visual Basic. Per ulteriori informazioni, vedere [eseguire il Debug il servizio cloud nel computer locale](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

- È possibile utilizzare Azure diagnostica per registrare informazioni dettagliate dal codice in esecuzione all'interno di ruoli, se i ruoli in esecuzione nell'ambiente di sviluppo o in Azure. Per ulteriori informazioni, vedere [registrazione di raccolta dati tramite diagnostica Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).

- Se si utilizza Visual Studio Enterprise per scrivere i ruoli di destinate a .NET Framework 4 o .NET Framework 4.5, è possibile abilitare IntelliTrace al momento si distribuisce un servizio cloud Azure da Visual Studio. IntelliTrace fornisce un file di log è possibile usare con Visual Studio per il debug dell'applicazione come se venisse eseguita nello Azure. Per ulteriori informazioni, vedere [debug di un servizio cloud pubblicato con traccia di diagnostica e Visual Studio]( http://go.microsoft.com/fwlink/p/?LinkId=623016).

- È possibile attivare il debug remoto nel servizi cloud in esecuzione quando si distribuisce il servizio cloud da Visual Studio. Se si sceglie di attivare il debug remoto per una distribuzione, servizi di debug remoti vengono installati nelle macchine virtuali che eseguono ogni istanza del ruolo. Questi servizi, ad esempio msvsmon.exe, non influiscono sulle prestazioni o comportare costi aggiuntivi. Per ulteriori informazioni, vedere [eseguire il Debug di un servizio cloud di Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).



