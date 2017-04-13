<properties 
   pageTitle="Creare un contratto per i Partner commerciali nel servizio di App Azure | Microsoft Azure" 
   description="Creare Trading accordi Partner" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
    ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/23/2016"
   ms.author="rajram"/>

# <a name="creating-a-trading-partner-agreement"></a>Creazione di un contratto per i Partner commerciali   

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Partner sono entità interessato B2B comunicazioni (Business-to-Business). Quando due partner stabilire una relazione, questo viene definito un *contratto*. Il contratto definito dipende la comunicazione due partner desiderano raggiungere e protocollo o trasporto specifico. I vari protocolli B2B e trasporti supportati dal servizio di Azure App includono:

- AS2 (applicabilità istruzione 2)
- EDIFACT (United Nations/Electronic Data Interchange di amministrazione, e-Commerce e trasporto (UN/EDIFACT))
- X12 (ASC X12)

### <a name="biztalk-api-apps-that-support-b2b-scenarios"></a>App API BizTalk che supportano scenari B2B
Le seguenti applicazioni API abilitare queste funzionalità usando un'esperienza ricca e intuitiva nel portale di Azure:


## <a name="biztalk-trading-partner-management-tpm"></a>Gestione dei Partner (TPM) commerciali BizTalk
- Creazione e la gestione delle identità, profili e partner
- Archiviazione e la gestione di schemi EDI
- Spazio di archiviazione e gestione dei certificati (utilizzato in AS2 protocol)
- Creazione e la gestione dei contratti AS2
- Creazione e la gestione dei contratti EDIFACT (include il batch sul lato invio)
- Creare e gestire le X12 accordi (include il batch sul lato invio)

![][1]


## <a name="as2-connector"></a>Connettore AS2
- Esegue contratti AS2 come definito nell'istanza di TPM API App correlata
- Informazioni di elaborazione/verifica AS2 per la risoluzione dei problemi di aree


## <a name="biztalk-edifact"></a>BizTalk EDIFACT
- Esegue contratti EDIFACT come definito nell'istanza di TPM API App correlata
- Informazioni di elaborazione/verifica EDIFACT superfici per la risoluzione dei problemi
- Fornisce gestione dello stato di batch (start e stop) definito nel contratto EDIFACT istanza TPM API App correlata


## <a name="biztalk-x12"></a>BizTalk X12
- Esegue X12 accordi definiti nell'istanza di TPM API App correlata 
- Aree X12 elaborazione/verifica informazioni per la risoluzione dei problemi
- Consente di gestire lo stato di batch (start e stop) come definito nella X12 contratti dell'istanza TPM API App correlata

Come descritto in precedenza, AS2, X 12 ed EDIFACT API App richiedono un'App di API TPM funzionino come previsto.


## <a name="getting-started"></a>Guida introduttiva
Per creare accordi partner commerciali:

1. Creare un'istanza del connettore **BizTalk gestione Partner commerciali** . È necessario un Database SQL vuoto alla funzione. Prima di iniziare accertarsi di disporre di un database vuoto disponibile per l'uso.
2. Caricare schemi e certificati in base alle esigenze dei contratti di. Eseguire questa operazione esplorazione istanza TPM creato e l'accesso a parte 'Schemi' o 'Certificati'
3. Passare all'istanza TPM creato e seguire la procedura nella parte **partner**
4. Creare i partner in base alle esigenze. Anche modificare i profili in base alle esigenze e aggiungere le identità necessari
5. Utilizzare ora la parte **accordi** per creare contratti. Quando si crea un contratto, è necessario selezionare il protocollo che verrà utilizzato. Le altre opzioni di configurazione sono in base al protocollo selezionato.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png
 
