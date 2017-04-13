<properties
    pageTitle="Domande frequenti su database ClearDB MySql con il servizio di App Azure | Microsoft Azure"
    description="Risposte alle domande frequenti sull'utilizzo di database ClearDB MySQL con il servizio di App Azure."
    documentationCenter="php"
    services=""
    authors="sunbuild"
    manager="yochayk"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="sumuth"/>

# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Domande frequenti su database ClearDB MySql con il servizio di App Azure

In questo argomento fornisce le risposte alle domande frequenti sull'utilizzo e l'acquisto di database ClearDB MySQL per Azure Web Apps.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Quali opzioni dispone per MySQL su Azure

Sono disponibili diverse opzioni:

* [Database MySQL condivisi ClearDB](/marketplace/partners/cleardb/databases/)

* [Cluster ClearDB MySQL Premium](/marketplace/partners/cleardb-clusters/cluster/)

* [Cluster MySQL in esecuzione su una macchina virtuale Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [Singola istanza di MySQL in esecuzione in una macchina virtuale Azure](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md)

ClearDB è MySQL servizio di hosting e gestire l'infrastruttura di MySQL dell'utente. Quando si esegue il proprio cluster MySQL o database una macchina virtuale Azure, è necessario configurare il server MySQL e mantenerlo aggiornato con le patch.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>È necessario disporre di una scheda di carta di credito per il Web app + modello MySQL in Azure Marketplace?

Dipende dal tipo di abbonamento in uso. Ecco alcuni tipi di sottoscrizione di uso comune:

* [Pagare in base all'uso](/offers/ms-azr-0003p/): richiede una carta di credito e quando si acquista un database MySQL a pagamento viene addebitata la carta di credito.

* [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/): include crediti per l'utilizzo con Microsoft Azure services ma non consente l'acquisto di terze parti. Acquisto di servizi di terze parti o un database MySQL a pagamento che è necessario utilizzare una sottoscrizione di carta di credito abilitato. Per le applicazioni Web, è possibile creare un database ClearDB MySQL gratuito.

* [Abbonamento MSDN](/pricing/member-offers/msdn-benefits/) e **MSDN Dev Test pagare durante la passare**: simile alla versione di valutazione gratuita, un abbonamento MSDN è necessario disporre di una carta di credito per acquistare una soluzione MySQL a pagamento da ClearDB.

* [Contratto Enterprise (EA)](/pricing/enterprise-agreement/): i clienti EA vengono calcolati rispetto alla loro EA ogni trimestre per tutti gli acquisti di Azure Marketplace (di terze parti) in una fattura distinta, consolidato. Vengono addebitate all'esterno di impegno monetario per tutti gli acquisti marketplace. Si noti che, in questo momento archivio Azure non è disponibile per i clienti iscritti Azerbaigian, Croazia, Norvegia e Portorico. 

*  [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): È possibile creare solo database ClearDB gratuito per le applicazioni Web. Non esiste alcun limite per il numero di database ClearDB MySQL gratuite che è possibile creare. Si noti che database gratuiti non deve essere utilizzato per le applicazioni web di produzione, come questo servizio è destinato solo versione di valutazione.

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Perché è rimasto addebitato $3.50 per un'app Web + MySQL da Azure Marketplace?

L'opzione di database predefinito è Titan, ossia $3.50. Durante la creazione del database non viene visualizzato il costo ed erroneamente è possibile acquistare un database che è stato inavvertitamente. Si sta tentando di trovare un modo per migliorare l'esperienza ma nel frattempo è necessario verificare tutti i livelli di prezzi selezionati per web app e database prima di fare clic su **Crea** e iniziare la distribuzione delle risorse.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>È in esecuzione MySQL nel proprio computer virtuale Azure. È possibile connettersi all'applicazione web Azure al database?

Sì. È possibile connettersi un'app web al database come la macchina virtuale Azure ha concesso l'accesso remoto all'app web. Per ulteriori informazioni, vedere [Installare MySQL in un computer virtuale](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md).

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>In cui i paesi sono i cluster ClearDB Premium MySQL supportati?

[ClearDB Premium MySQL cluster](/marketplace/partners/cleardb-clusters/cluster/) sono disponibili in tutte le aree di Azure in tutto il mondo ad eccezione dell'India, Australia, Brasile Sud e Cina.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>È possibile creare un nuovo cluster prima di creare un database con soluzione cluster premium ClearDB?

No, non è supportata la creazione di cluster ClearDB vuoto. Portale di Azure consente di creare i database in un cluster, che può creare un nuovo cluster nello stesso momento.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Sarà ricevere avvisi se si tenta di eliminare un database ClearDB MySQL in uso da una delle applicazioni?

No, Azure non segnalerà se si elimina un acquisto marketplace dipende l'applicazione.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Quali aree è possibile creare i database delle ClearDB in

Azure Marketplace non è disponibile per i clienti Azerbaigian, Croazia, Norvegia o Portorico. ClearDB non è disponibile in queste aree geografiche.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Quali livelli prezzi è consigliabile scegliere per un'app web di produzione e il database

Utilizzare Basic o un livello di prezzo superiore per le applicazioni Web. Per ClearDB, è consigliabile se o Giove piano. Esaminare le caratteristiche e limitazioni di ogni livello prezzo da [Web App](/pricing/details/app-service/) e [database ClearDB MySQL](/marketplace/partners/cleardb/databases/) per scegliere più adatto alle proprie esigenze.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Come si aggiorna il database ClearDB da un piano a altro

Nel [portale di Azure](https://portal.azure.com), è possibile ridimensionare backup di un database di hosting condiviso ClearDB. Leggere questo [articolo](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) per altre informazioni. È attualmente non supportano l'aggiornamento per i cluster ClearDB Premium nel portale di Azure.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Non è possibile visualizzare il database ClearDB nel portale di Azure?

Se si crea database ClearDB utilizzando Gestione risorse di Azure o [Nuovo Azure portale](https://portal.azure.com), non sarà visibile nel [Portale di Azure precedente](https://manage.windowsazure.com). Per ovviare questo è il collegamento manualmente il database in web app. Allo stesso modo se creare database ClearDB nel [portale precedente](https://manage.windowsazure.com) non sarà possibile visualizzare il database nel [Nuovo portale Azure](https://portal.azure.com). Non esiste alcun ovviare per lo scenario quest'ultimo.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Chi è necessario contattare per assistenza quando il database non funziona?

Problemi relativi al contatto [ClearDB supporto](https://www.cleardb.com/developers/help/support) relative a qualsiasi database. Prepararsi a fornire le informazioni sull'abbonamento Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>È possibile creare altri utenti per la soluzione cluster di database ClearDB MySQL? 

No. Non è possibile creare altri utenti, ma è possibile creare altri database il cluster di database ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Possibile serie Basic/Pro database aggiornamento sul posto simile ai sistemi su piani oggi nel portale ClearDB?

Sì, serie di base database possono essere aggiornati sul posto (base 60 e 500 base). Serie Pro possono essere aggiornati sul posto (125 Pro e Pro 1000) ad eccezione di 60 Pro. Non è supportato l'aggiornamento del database 60 Pro attualmente. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Quando si esegue la migrazione di risorse da una sottoscrizione a un altro, il database ClearDB MySQL migrato anche? 

Quando si esegue la migrazione di risorse tra abbonamenti, applicare alcune [limitazioni](./app-service-web/app-service-move-resources.md) . Un database ClearDB MySQL è un servizio di terze parti e pertanto non vengono migrato durante la migrazione di Azure abbonamento. Se non si gestisce la migrazione del database MySQL prima della migrazione delle risorse Azure, è possono disattivare i database ClearDB MySQL. Eseguire manualmente la migrazione dei database prima di tutto, quindi eseguire la migrazione di Azure abbonamento per un'app web. 

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>È possibile trasferire un database di ClearDB da una sottoscrizione di carta di credito a un abbonamento a EA?

Database ClearDB esistenti utilizzano carta di credito associata le sottoscrizioni esistenti. Per utilizzare un abbonamento a EA che è necessario eseguire la migrazione dei dati in un nuovo database:

* È possibile acquistare un nuovo database ClearDB con l'abbonamento EA.
* Eseguire la migrazione dei dati in un nuovo database.
* Aggiornare l'applicazione per utilizzare il nuovo database.
* Eliminare il vecchio database ClearDB.

Quando si crea una nuova app web con MySQL (ClearDB) o creare un database MySQL (ClearDB), l'abbonamento che si sceglie determina come verranno pagare per il servizio. Con un abbonamento EA, non si blocca l'acquisto di servizi di terze parti, ad esempio ClearDB nel portale di Azure. Le sottoscrizioni EA effettuate all'esterno di impegno monetari e vengono addebitate trimestrali e in ritardo. Il cliente EA avrebbe impostare un metodo di pagamento, ad esempio una carta di credito per pagare per uno o più servizi marketplace di terze parti.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Dove è possibile visualizzare le spese per le risorse ClearDB un abbonamento a EA?

Per i clienti EA diretta, in base alle tariffe Azure Marketplace sono visibili nel portale aziendale. Si noti che tutti gli acquisti di marketplace e consumo effettuate all'esterno di impegno monetari e vengono addebitate trimestrale e in ritardo. I clienti EA pagare direttamente per i provider di servizi di terze parti e farlo abilitando un metodo di pagamento, ad esempio una carta di credito con il proprio account EA.

I clienti EA indiretti è possono trovare le sottoscrizioni di Azure Marketplace nella pagina **Gestione abbonamenti** del portale aziendale, ma prezzi è nascosta. Per informazioni su addebiti marketplace clienti contattare il provider di servizi linguistici.

Accesso a Azure Marketplace per i servizi di terze parti può essere gestito da amministratori di registrazione di Azure EA. Possono disattivare o riattivare l'accesso a 3 ° entità acquisti dall'archivio in Gestisci account e abbonamenti nella sezione account Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Chi è necessario contattare per eventuali domande sulla fattura per servizi ClearDB nel proprio abbonamento EA?

Contattare il [Supporto tecnico dell'organizzazione](http://aka.ms/AzureEntSupport) per quanto riguarda la fatturazione in loro registrazione EA. Il Team di supporto portale EA verrà rispondere alla domanda o risolvere il problema.

 



## <a name="more-information"></a>Ulteriori informazioni

[Domande frequenti su Azure Marketplace](/marketplace/faq/)
