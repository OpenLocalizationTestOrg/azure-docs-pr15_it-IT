#### <a name="create-an-a-record-set-with-single-record"></a>Creare un record a impostare a record singolo

Per creare un set di record, utilizzare `azure network dns record-set create`. Specificare il gruppo di risorse, nome zona record impostare relativo nome, tipo di record e ora a live (TTL).

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

Dopo aver creato il set di record, aggiungere l'indirizzo IPv4 al record set con `azure network dns record-set add-record`.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Creare un record AAAA set con un singolo record

    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### <a name="create-a-cname-record-set-with-a-single-record"></a>Creare un record CNAME set con un singolo record

Record CNAME è consentito solo un singolo valore stringa.


    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### <a name="create-an-mx-record-set-with-a-single-record"></a>Creare un record MX set con un singolo record

In questo esempio è usare il nome del set di record "@" per creare il record MX in apice zona (in questo caso, "contoso.com"). Si tratta comune per i record MX.

    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### <a name="create-an-ns-record-set-with-a-single-record"></a>Creare un record NS set con un singolo record

    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### <a name="create-a-ptr-record-set-with-a-single-record"></a>Creare un record PTR set con un singolo record  
In questo caso "personale-arpa-zone.com' rappresenta l'area ARPA che rappresenta l'intervallo di indirizzi IP.  Ogni record PTR impostare in quest'area corrisponde a un indirizzo IP all'interno dell'intervallo di indirizzi IP.    

    azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"   

#### <a name="create-an-srv-record-set-with-a-single-record"></a>Creare un record SRV set con un singolo record

Se si sta creando un record SRV nella radice dell'area, è possibile specificare Service"e"_protocol"nel nome del record. Non è necessario includere "@" nel nome del record.


    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### <a name="create-a-txt-record-set-with-single-record"></a>Creare un record TXT Imposta singolo record

    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"
