**Obiettivo-C**: 

1. Nel Mac aprire _QSTodoListViewController.m_ in Xcode e aggiungere il metodo seguente. Modificare _google_ per _microsoftaccount_, _twitter_, _facebook_o _windowsazureactivedirectory_ se non si usa Google come provider di identità. Se si usa Facebook, [sarà necessario proprietà consentite Facebook domini nell'app](https://developers.facebook.com/docs/ios/ios9#whitelist).

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. Sostituire `[self refresh]` in `viewDidLoad` in _QSTodoListViewController.m_ con le operazioni seguenti:

            [self loginAndGetData];

3. Premere _eseguire_ per avviare l'app e quindi accedere. Quando si è connessi, dovrebbe essere possibile visualizzare l'elenco e effettuare aggiornamenti.

**Swift**:

1. Nel Mac aprire _ToDoTableViewController.swift_ in Xcode e aggiungere il metodo seguente. Modificare _google_ per _microsoftaccount_, _twitter_, _facebook_o _windowsazureactivedirectory_ se non si usa Google come provider di identità. Se si usa Facebook, [sarà necessario proprietà consentite Facebook domini nell'app](https://developers.facebook.com/docs/ios/ios9#whitelist).
        
            func loginAndGetData() {
                
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
                
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }


2. Rimuovere le righe `self.refreshControl?.beginRefreshing()` e `self.onRefresh(self.refreshControl)` alla fine di `viewDidLoad()` in _ToDoTableViewController.swift_. Aggiungere una chiamata a `loginAndGetData()` al posto:

            loginAndGetData()

3. Premere _eseguire_ per avviare l'app e quindi accedere. Quando si è connessi, dovrebbe essere possibile visualizzare l'elenco e effettuare aggiornamenti.
