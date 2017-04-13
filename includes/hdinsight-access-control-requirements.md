Se si utilizza una sottoscrizione Azure in cui si è non amministratore/proprietari, ad esempio una società proprietà abbonamento, è necessario verificare le operazioni seguenti prima di utilizzare la procedura descritta in questo documento:

* La procedura di accesso Azure deve avere almeno __collaboratori__ l'accesso al gruppo di risorse Azure utilizzate durante la creazione di HDInsight (e altre risorse Azure)

* Un utente con in almeno accesso __collaboratori__ per l'abbonamento Azure deve avere registrata in precedenza provider per la risorsa in uso. Registrazione provider non accade quando un utente con accesso collaboratori per l'abbonamento crea una risorsa per la prima volta dell'abbonamento. Anche possibile senza creare una risorsa tramite [la registrazione di un provider utilizzando resto](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Per ulteriori informazioni sull'utilizzo di gestione dell'accesso, vedere i documenti seguenti:

* [Guida introduttiva a gestione degli accessi nel portale di Azure](../articles/active-directory/role-based-access-control-what-is.md)
* [Utilizzare le assegnazioni di ruolo per gestire l'accesso alle risorse dell'abbonamento Azure](../articles/active-directory/role-based-access-control-configure.md)
