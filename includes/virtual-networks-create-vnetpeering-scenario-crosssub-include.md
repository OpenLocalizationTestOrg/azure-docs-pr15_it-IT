## <a name="peering-across-subscriptions"></a>Peering tra abbonamenti

In questo scenario si creerà un peering tra due VNets che appartengono a diversi abbonamenti.

![scenario sub cross](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet peering si basa sul controllo dell'accesso basato sui ruoli (RBAC) per l'autorizzazione. Per gli scenari tra abbonamenti, è necessario innanzitutto concedere autorizzazioni agli utenti che ha creato il collegamento di peering:

> [AZURE.NOTE] Se lo stesso nome utente dispone di privilegi su entrambe le sottoscrizioni, è possibile ignorare nel passaggio 1-4 riportata di seguito.
