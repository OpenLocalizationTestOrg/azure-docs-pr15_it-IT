<properties
   pageTitle="Internet di procedure consigliate per la protezione aspetti | Microsoft Azure"
   description="L'articolo comprende un elenco curated di Microsoft Internet di operazioni consigliate per la protezione e consigli."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="internet-of-things-security-best-practices"></a>Internet di procedure consigliate per la protezione di elementi

Protezione dell'infrastruttura Internet di elementi (IoT) è un'impresa critica per tutti i membri con IoT soluzioni. A causa del numero di dispositivi coinvolti e distribuita di questi dispositivi, l'impatto di un evento di protezione relativi a compromettere milioni di dispositivi IoT è più complessa e può avere un impatto esteso.

Per questo motivo, sicurezza IoT richiede un approccio di sicurezza. Dati devono essere sicuro nel cloud e mentre si sposta attraverso le reti pubbliche e private. Metodi devono essere nella posizione in cui effettuare il provisioning in modo sicuro periferiche IoT stesse. Ogni layer da dispositivo, alla rete, nel cloud back-end deve garanzie di protezione avanzata.

Procedure consigliate IoT possono essere suddivise nel modo seguente:

- Produttore dell'hardware IoT o integratore
- Sviluppo di soluzioni IoT
- Distributore soluzione IoT
- Operatore di soluzione IoT

In questo articolo sono riepilogate [Internet di operazioni consigliate per la protezione](../iot-suite/iot-security-best-practices.md). Fare riferimento all'articolo per informazioni più dettagliate.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Produttore dell'hardware IoT o integratore

Se si è un produttore dell'hardware IoT o un integratore hardware, seguire le procedure consigliate seguenti:

- **Hardware ambito ai requisiti minimi**: la struttura dell'hardware deve includere caratteristiche minimi necessari per l'operazione di hardware e niente più. 
- **Componenti hardware manomettere prova**: creare meccanismi per rilevare l'alterazione dei fisica dell'hardware, ad esempio aprendo la copertina del dispositivo, la rimozione di una parte del dispositivo, e così via. 
- **Creare intorno hardware sicura**: se consentire [COGS](https://en.wikipedia.org/wiki/Cost_of_goods_sold) , compilare le funzionalità di sicurezza, ad esempio lo spazio di archiviazione sicura e crittografato e le funzionalità di avvio basato su Trusted Moduletpm piattaforma.
- **Eseguire aggiornamenti sicura**: aggiornamento del firmware tutta la durata del dispositivo è inevitabile.

## <a name="iot-solution-developer"></a>Sviluppo di soluzioni IoT

Se gli sviluppatori di soluzioni IoT, seguire le procedure consigliate seguenti:

- **Metodologia di sviluppo software sicuro Segui**: sviluppo di software protetto deve essere interamente progettato sulla protezione dall'inizio del progetto tutto implementazione, test e di distribuzione.
- **Scegliere Apri origine software con attenzione**: software open source offre l'opportunità di sviluppare rapidamente soluzioni.
- **Integrazione con attenzione**: molti dei problemi di protezione software esiste al limite di API e raccolte. 

## <a name="iot-solution-deployer"></a>Distributore soluzione IoT

Se si è un distributore soluzione IoT, seguire le procedure consigliate seguenti:

- **Distribuzione hardware protetta**: distribuzioni IoT possono richiedere hardware per essere distribuito nelle posizioni non sicuro, ad esempio in locali pubblici o impostazioni locali senza supervisione.
- **Proteggere le chiavi di autenticazione**: durante l'installazione, ogni dispositivo richiede l'ID e associate chiavi di autenticazione generate dal servizio cloud. Proteggere questi tasti fisica anche dopo la distribuzione. Qualsiasi chiave compromessa può essere usate da un dispositivo dannoso il mascheramento come un dispositivo esistente.

## <a name="iot-solution-operator"></a>Operatore di soluzione IoT

Se si è un operatore di soluzione IoT, seguire le procedure consigliate seguenti:

- **Mantenere aggiornato sistemi**: assicurarsi che i sistemi operativi dispositivo e tutti i driver di dispositivo vengono aggiornati alle versioni più recenti. 
- **Protezione contro attività dannoso**: se il sistema operativo consente, posizionare le funzionalità più recente di software antivirus e anti-malware su ogni sistema operativo del dispositivo. 
- **Controllo spesso**: il controllo dell'infrastruttura IoT per relative alla protezione problemi è chiave quando si risponde ai problemi di protezione.
- **Proteggere l'infrastruttura di IoT**: peggiori attacchi di protezione contro IoT infrastruttura vengono avviati con accesso fisico ai dispositivi.
- **Le credenziali di protezione cloud**: le credenziali di autenticazione cloud utilizzate per configurare e utilizzare i una distribuzione IoT sono forse il modo più semplice per accedere e compromettere un sistema IoT. 
