<properties
   pageTitle="Test della penna | Microsoft Azure"
   description="L'articolo viene fornita una panoramica di test di penetrazione processo (pentest) e come eseguire pentest contro le App in esecuzione in Azure infrastruttura."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="pen-testing"></a>Test della penna

Uno dei vantaggi delle informazioni sull'utilizzo di Microsoft Azure per la verifica dell'applicazione e distribuzione è che non è necessario creare un'infrastruttura locale per sviluppare, testare e distribuire le applicazioni. Infrastruttura di viene gestito dai servizi della piattaforma Microsoft Azure. Non è necessario preoccuparsi di richiesta approvvigionamento, durante l'acquisizione, "su rack e sovrapposizione" hardware locale.

Questo è molto utile, ma è necessario verificare che si esegue la sicurezza normale diligente. Una delle operazioni da effettuare è penetrazione testare le applicazioni si distribuisce in Azure.

Si potrebbe sanno che Microsoft esegue [test di penetrazione dell'ambiente di Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). La fine di migliorare la piattaforma e guide le azioni in termini di migliorare i controlli di sicurezza, Introduzione ai nuovi controlli di protezione e migliorare i processi di sicurezza.

Abbiamo non penna testare l'applicazione dell'utente, ma poiché verranno desiderato e devono eseguire test sulle applicazioni penna. È utile, perché quando si migliora la protezione delle applicazioni, si rendono l'intero ecosistema Azure più sicure.

Quando si penna testare le applicazioni, è possibile ottenere un attacco a Microsoft. Abbiamo [continuo monitoraggio](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) per attacchi motivi e verrà avviata un processo di risposta, se necessario. Non consentono e non Aiutaci se è attiva una risposta a causa di propria dovuto penna diligenza test.

cosa fare?

Quando si è pronti a penna testare le applicazioni ospitati Azure, è necessario sway.uservoice.com. Se si conosce che si desidera eseguire test specifici, vengono inavvertitamente non si arresta (ad esempio, bloccare l'indirizzo IP che si sta verificando da), come i test rispettare la Azure penna termini e condizioni di test.
È possibile eseguire i test standard includono:

- Test sugli endpoint per scoprire le [principali 10 vulnerabilità Apri Web applicazione protezione progetto (OWASP)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
- [Test casuali](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) degli endpoint
- [Analisi delle porte](https://en.wikipedia.org/wiki/Port_scanner) degli endpoint

Un tipo di test che non è possibile eseguire è qualsiasi tipo di [attacchi di negazione di](https://en.wikipedia.org/wiki/Denial-of-service_attack) servizio (DoS). Ad esempio si avvia un attacco DoS stesso o l'esecuzione di test correlate che possono determinare, illustrare o simulare qualsiasi tipo di attacco DoS.

Si è pronti per iniziare con penna testa le applicazioni ospitate in Microsoft Azure? Se in tal caso, quindi testa sulla sopra alla pagina [Panoramica di Test di penetrazione](https://security-forms.azure.com/penetration-testing/terms) (e fare clic su Crea un pulsante test richiedere nella parte inferiore della pagina. Sono inoltre disponibili ulteriori informazioni sulla penna test condizioni e collegamenti utili su come è possibile segnalare problemi di sicurezza relativi a Azure o qualsiasi altro servizio di Microsoft.
