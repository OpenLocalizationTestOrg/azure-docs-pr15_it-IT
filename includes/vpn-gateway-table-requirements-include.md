Nella tabella seguente elenca i requisiti per i gateway PolicyBased e RouteBased VPN. In questa tabella si applica alle Manager delle risorse e modelli di distribuzione classica. Per il modello classico gateway PolicyBased VPN sono le stesse gateway statico e basate su Route gateway sono le stesse gateway dinamico.


|   | **Gateway VPN PolicyBased Basic** | **Gateway VPN RouteBased Basic** | **Gateway VPN RouteBased Standard**   | **RouteBased massime prestazioni VPN Gateway** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
|    **Connettività da sito (S2S)**  | Configurazione PolicyBased VPN        | Configurazione RouteBased VPN  | Configurazione RouteBased VPN     | Configurazione RouteBased VPN    |
| Connettività **punto al sito (P2S**)      | Non è supportata   | Supportati (possono coesistere con S2S)  | Supportati (possono coesistere con S2S)  | Supportati (possono coesistere con S2S) |
| **Metodo di autenticazione**                 |    Chiave già condivisa  | Chiave già condivisa per la connettività S2S, certificati per la connettività P2S | Chiave già condivisa per la connettività S2S, certificati per la connettività P2S | Chiave già condivisa per la connettività S2S, certificati per la connettività P2S |
| **Numero massimo di connessioni S2S**       | 1                              | 10                                                                    | 10                                | 30                               |
| **Numero massimo di connessioni P2S**       | Non è supportata                  | 128                                                                   | 128                               | 128                              |
|**Supporto routing attivo (BGP)**           | Non è supportata                  | Non è supportata                                                         | Supportati                     | Supportati                   |
 
