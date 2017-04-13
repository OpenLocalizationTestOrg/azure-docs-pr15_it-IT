Una zona DNS viene utilizzata per ospitare i record DNS per un determinato dominio. Per avviare l'hosting del dominio, è necessario creare una zona DNS. Qualsiasi record DNS creati per un determinato dominio sarà all'interno di una zona DNS relativa al dominio. 

Ad esempio, il dominio "contoso.com" può contenere un numero di record DNS, ad esempio "mail.contoso.com" (per un server di posta elettronica) e "www.contoso.com" (per un sito web). 


## <a name="names"></a>Sui nomi di zona DNS
 
- Il nome dell'area deve essere univoco all'interno del gruppo di risorse e l'area deve non esiste già. In caso contrario, l'operazione non riesce.

- Lo stesso nome zona possa essere riutilizzato in un altro gruppo di risorse o una sottoscrizione di Azure diversa. 

- In cui più fusi orari condividono lo stesso nome, ogni istanza verrà assegnata indirizzi diversi del server e solo un'istanza può essere delegata dal dominio padre. Per ulteriori informazioni, vedere [delegato un dominio di Azure DNS](../articles/dns/dns-domain-delegation.md).