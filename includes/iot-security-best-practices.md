# <a name="internet-of-things-security-best-practices"></a>Procedure consigliate per la protezione Internet di elementi

Per garantire la protezione un Internet di elementi (IoT) infrastruttura richiede una strategia di protezione avanzata rigorosi. Questa strategia è necessario proteggere i dati nel cloud, proteggere l'integrità dei dati in transito su internet e il provisioning in modo protetto i dispositivi. Ogni livello Build maggiori garanzie di protezione dell'infrastruttura generale.

## <a name="secure-an-iot-infrastructure"></a>Sicurezza di un'infrastruttura IoT

Questa strategia di protezione può essere sviluppata ed eseguita con la partecipazione attiva di diversi lettori riguardano la produzione, lo sviluppo e distribuzione dell'infrastruttura e dei dispositivi IoT. Di seguito è una descrizione di alto livello di questi lettori.  

- **IoT hardware produttore/integratore**: questi sono in genere, i produttori di hardware IoT da distribuire, integratori di assemblaggio di hardware da diversi produttori o fornitori fornitura di hardware per una distribuzione IoT produce o integrate da altri fornitori.
- **Sviluppo di soluzioni IoT**: sviluppo di una soluzione IoT viene in genere eseguito da uno sviluppatore della soluzione. Questo per gli sviluppatori possono parte di un team interno o un integratore di sistemi (SI) specializzato in tali attività. Gli sviluppatori di soluzioni IoT possono sviluppare vari componenti della soluzione IoT da zero, integrare i vari componenti preconfigurati o open source o adottare soluzioni preconfigurate con adattamento secondaria.
- **Distributore soluzione IoT**: dopo un'IoT soluzione è stata sviluppata, deve essere distribuito nel campo. Comporta la distribuzione di componenti hardware, interconnessione dei dispositivi e distribuzione di soluzioni in dispositivi hardware o nel cloud.
- **Operatore soluzione IoT**: dopo IoT soluzione è distribuita, è necessario che le operazioni a lungo termine, il monitoraggio, gli aggiornamenti e manutenzione. Possono essere eseguita da un team interno che comprende esperti di IT informazioni, operazioni hardware e team di manutenzione e specialisti dominio monitorare il corretto funzionamento dell'infrastruttura IoT complessiva.

Nelle sezioni che seguono vengono fornite procedure consigliate per ognuno di questi lettori per sviluppare, distribuire e utilizzare un'infrastruttura protetta IoT.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT hardware produttore/integratore

Di seguito sono le procedure consigliate per i produttori di hardware IoT e integratori di hardware.

- **Hardware ambito ai requisiti minimi**: struttura dell'hardware deve includere le funzionalità minime necessarie per operazione di hardware e non più. Un esempio deve includere le porte USB solo se è necessario per il funzionamento del dispositivo. Caratteristiche aggiuntive aprire il dispositivo per gli attacchi indesiderati che deve essere evitato.
- **Componenti hardware manomissione**: creare meccanismi per rilevare fisico alterazione dei dati, ad esempio l'apertura della copertura dispositivo o rimozione di una parte del dispositivo. Queste alterare segnali può far parte del flusso di dati caricato nel cloud che potrebbe avvisare gli operatori di questi eventi.
- **Creare attorno hardware sicuro**: COGS se consente lo spostamento, compilare le funzionalità di protezione, ad esempio l'archiviazione sicura e crittografato o avviare funzionalità basate su Trusted Platform Module (GPC). Queste funzionalità rendono dispositivi più sicure e migliorare la protezione dell'infrastruttura IoT complessiva.
- **Gli aggiornamenti sicuro**: aggiornamenti Firmware per tutta la durata del dispositivo sono inevitabili. Creazione di dispositivi con percorsi protetti per gli aggiornamenti e la crittografia controllo delle versioni del firmware che consente il dispositivo sia sicura durante e dopo ogni aggiornamento.

## <a name="iot-solution-developer"></a>Sviluppo di soluzioni IoT

Le procedure consigliate per gli sviluppatori di soluzioni IoT sono i seguenti:

- **Metodologia di sviluppo software protetto follow**: sviluppo di software protetto deve essere interamente progettato sulla sicurezza, dall'inizio del progetto completamente relativa implementazione, il testing e distribuzione. Le scelte di piattaforme, le lingue e gli strumenti sono influenzate con questa metodologia. Microsoft Security Development Lifecycle fornisce un approccio passo-passo per la creazione di software protetto.
- **Scegliere open source software con attenzione**: software Open source offre la possibilità di sviluppare soluzioni appropriate. Se si sceglie il software open source, prendere in considerazione il livello di attività della community per ogni componente open source. Una community attiva assicura che software è supportato e che vengano individuati e indirizzati problemi. In alternativa, un software open source complessi e inattivo potrebbe non essere supportato e problemi potrebbe non essere individuati.
- **Integrazione con attenzione**: esistono molti problemi di protezione software in corrispondenza del limite di API e raccolte. Funzionalità che non possono essere necessari per la distribuzione corrente potrebbe ancora essere disponibili tramite un livello API. Per garantire protezione globale, assicurarsi di verificare tutte le interfacce dei componenti viene integrati per problemi di protezione.      

## <a name="iot-solution-deployer"></a>Distributore soluzione IoT

Procedure consigliate per deployers soluzione IoT sono i seguenti:

- **Implementazione dell'hardware in modo sicuro**: IoT distribuzioni potrebbe essere necessario hardware distribuito in percorsi non sicuri, ad esempio locali pubblici o le impostazioni internazionali senza supervisione. In tal caso, verificare che la distribuzione di hardware manomissione al massimo. Se sono disponibili nell'hardware USB o altre porte, assicurarsi che sono descritti in modo protetto. Numero di attacchi è possono utilizzare questi come punti di ingresso.
- **Proteggere le chiavi di autenticazione**: durante la distribuzione richiede l'ID dispositivo ogni dispositivo e associate chiavi di autenticazione generate dal servizio cloud. Mantenere queste sequenze di tasti fisicamente sicuro anche dopo la distribuzione. Qualsiasi chiave compromessa è utilizzabile da un dispositivo dannoso il mascheramento come un dispositivo esistente.

## <a name="iot-solution-operator"></a>Operatore soluzione IoT

Le procedure consigliate per gli operatori di soluzione IoT sono i seguenti:

- **Mantenere aggiornato il sistema**: verificare che per le versioni più recenti vengono aggiornati i sistemi operativi di dispositivi e tutti i driver di dispositivo. Se si attiva aggiornamenti automatici in Windows 10 (IoT o altri SKU), Microsoft mantiene aggiornate, fornendo un sistema operativo protetto per i dispositivi IoT. Mantenere altri sistemi operativi (ad esempio Linux) aggiornati assicura che sono inoltre protetti da attacchi dannosi.
- **Protezione contro i attività dannose**: se il sistema operativo consente, è possibile installare più recenti funzionalità antivirus e antimalware in ogni sistema operativo del dispositivo. Ciò consente di ridurre le minacce esterne più. È possibile proteggere più moderni sistemi operativi contro le minacce eseguendo i passaggi appropriati.
- **Controllo frequentemente**: controllo IoT infrastruttura per i problemi relativi alla protezione è chiave per la risposta ai problemi di protezione. La maggior parte dei sistemi operativi forniscono la registrazione degli eventi incorporati che è consigliabile leggere frequentemente per assicurarsi che nessun violazione della protezione. Controllo da inviare come flusso separato telemetria al servizio cloud cui può essere analizzata.
- **Isolare fisicamente infrastruttura IoT**: I peggiore protezione attacchi IoT infrastruttura vengono avviati utilizzando access fisici ai dispositivi. Un importante safety consigliata consiste nel protezione contro l'utilizzo per scopi dannosi delle porte USB e altri accesso fisico. Registrazione di una chiave per contribuire al rilevamento violazioni sono stati generati Access fisici, ad esempio utilizza la porta USB. Nuovamente, Windows 10 (altri SKU e IoT) abilita la registrazione dettagliata di questi eventi.
- **Le credenziali cloud Protect**: le credenziali di autenticazione Cloud utilizzate per la configurazione e il funzionamento di una distribuzione IoT sono forse il modo più semplice per accedere e danneggiare un sistema IoT. Proteggere le credenziali per la modifica della password frequentemente ed evitare l'utilizzo di queste credenziali per i computer pubblici.

Le funzionalità di diversi dispositivi IoT variano. In alcuni dispositivi potrebbero essere computer che eseguono sistemi operativi desktop comuni e alcuni dispositivi potrebbero essere che eseguono sistemi operativi prestazioni molto più semplice. Procedure consigliate descritte in precedenza potrebbero essere applicabili a questi dispositivi in diversi livelli. Se specificato, sicurezza e distribuzione di procedure consigliate aggiuntive dai produttori di questi dispositivi devono essere seguiti.

Alcuni dispositivi legacy e vincolate potrebbero non sono stati progettati in modo specifico per la distribuzione IoT. Questi dispositivi potrebbero non è presente la possibilità di crittografare i dati, la connessione a Internet o fornire il controllo avanzate. In questi casi, un gateway campo moderno e protetta può aggregare dati dai dispositivi legacy e garantire la sicurezza necessaria per la connessione di questi dispositivi tramite Internet. Gateway di campo può fornire autenticazione protetta, la negoziazione di sessioni crittografate, carico dei comandi dal cloud e molte altre funzionalità di sicurezza.
