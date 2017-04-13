## <a name="vpn-gateway"></a>Gateway VPN 
Una risorsa di gateway VPN consente di creare una connessione protetta tra loro centro dati locale e Azure. Una risorsa di gateway VPN può essere configurata in tre modi diversi:
 
- **Punto al sito** , è possibile accedere in modo sicuro le risorse Azure sono ospitate in un VNET tramite un client VPN da qualsiasi computer. 
- **Connessione di più siti** – è possibile connettersi da data center locale in modo sicuro alle risorse in esecuzione in una VNET. 
- **VNET a VNET** – è possibile connettersi in modo sicuro tra Azure VNETS all'interno dell'area stessa o tra le aree di compilazione di carichi di lavoro con la ridondanza geografico.

Proprietà chiave di un gateway VPN includono:
 
- **Tipo di gateway** - dinamicamente indirizzato o da un gateway indirizzato statico. 
- **VPN Client indirizzo Pool prefisso** : indirizzi IP da assegnare ai client di connessione in un punto di configurazione del sito.