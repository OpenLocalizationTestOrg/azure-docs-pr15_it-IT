<properties
    pageTitle="Sincronizzazione di Azure AD Connect: funzioni riferimento | Microsoft Azure"
    description="Guida di riferimento delle espressioni di provisioning dichiarative di sincronizzazione di Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-functions-reference"></a>Sincronizzazione di Azure AD Connect: funzioni di riferimento

In Azure AD Connect vengono utilizzate per modificare un valore dell'attributo durante la sincronizzazione.  
La sintassi delle funzioni è espressa nel formato seguente:  
`<output type> FunctionName(<input type> <position name>, ..)`

Se una funzione è sovraccarico e accetta più sintassi, sono elencati tutti i tipi di sintassi valide.  
Le funzioni sono tipizzate e vengono verificare che il tipo passato corrispondenze il tipo di documentazione.  
Se il tipo non corrisponde, viene generato un errore.

I tipi di sono espressi con la sintassi seguente:

- **collocazione** -binario
- **bool** : Boolean
- **dt** -data/ora UTC
- **enumerazione** : enumerazione delle costanti note
- **exp** -espressione, previsto in modo che restituisca un valore Boolean
- **mvbin** – multivalore binario
- **mvstr** -stringa multivalore
- **mvref** -riferimento multivalore
- **num** -numerico
- **ref** -riferimento
- **str** -stringa
- **var** : una variante di (quasi) qualsiasi altro tipo
- **void** – non restituisca un valore

Le funzioni con tipi **mvbin**, **mvstr**e **mvref** possono utilizzare solo gli attributi multivalore. Funzioni di **classe**, **str**e **ref** attributi sia a valore singolo e multivalore.

## <a name="functions-reference"></a>Guida di riferimento funzioni

Elenco di funzioni | | | | |  
--------- | --------- | --------- | --------- | --------- | ---------
**Conversione** |  
[CBool](#cbool) | [CDate](#cdate) | [CGuid](#cguid) | [ConvertFromBase64](#convertfrombase64)
[ConvertToBase64](#converttobase64) | [ConvertFromUTF8Hex](#convertfromutf8hex) | [ConvertToUTF8Hex](#converttoutf8hex) | [CNum](#cnum)
[CRef](#cref) | [CStr](#cstr) | [StringFromGuid](#StringFromGuid) | [StringFromSid](#stringfromsid)
**Data / ora** |  
[DateAdd](#dateadd) | [DateFromNum](#datefromnum) | [FormatDateTime](#formatdatetime) | [A questo punto](#now)
[NumFromDate](#numfromdate) |  
**Directory** |  
[DNComponent](#dncomponent) | [DNComponentRev](#dncomponentrev) | [EscapeDNComponent](#escapedncomponent)
**Valutazione** |  
[IsBitSet](#isbitset) | [IsDate](#isdate) | [IsEmpty](#isempty) | [IsGuid](#isguid)
[IsNull](#isnull) | [IsNullOrEmpty](#isnullorempty) | [IsNumeric](#isnumeric) | [IsPresent](#ispresent) |
[IsString](#isstring) |  
**Matematiche** |  
[BitAnd](#bitand) | [Funzione bitOr](#bitor) | [RandomNum](#randomnum)
**Multivalore** |  
[Contiene](#contains) | [Conta. numeri](#count) | [Elemento](#item) | [ItemOrNull](#itemornull)
[Join](#join) | [RemoveDuplicates](#removeduplicates) | [Divisione](#split) |
**Flusso di programma** |  
[Errore](#error) | [IIF](#iif)  | [Opzione](#switch)
**Testo** |  
[GUID](#guid) | [InStr](#instr) | [Funzione InStrRev](#instrrev) | [Funzione LCase](#lcase)
[A sinistra](#left) | [Lunghezza](#len) | [Funzioni LTrim](#ltrim) | [Stringa. Estrai](#mid)
[PadLeft](#padleft) | [PadRight](#padright) | [PCase](#pcase) | [Sostituisci](#replace)
[ReplaceChars](#replacechars) | [Ok](#right) | [RTrim](#rtrim) | [Tagliare](#trim)
[Funzione UCase](#ucase) | [Word](#word)

----------
### <a name="bitand"></a>BitAnd

**Descrizione:**  
La funzione BitAnd imposta bit specificato in un valore.

**Sintassi:**  
`num BitAnd(num value1, num value2)`

- val1; val2: valori numerici che non devono essere applicato l'operatore AND insieme

**Note:**  
Questa funzione converte la rappresentazione binaria entrambi i parametri e imposta un po' su:

- 0 - se uno o entrambi i bit corrispondente nella *maschera* e *contrassegno* sono 0
- 1 - se entrambi i bit corrispondente è 1.

In altre parole, restituisce 0 in tutti i casi tranne quando i bit corrispondenti di entrambi i parametri sono 1.

**Esempio:**  
`BitAnd(&HF, &HF7)`  
Restituisce 7 perché esadecimale "F" e "F7" restituire il valore.

----------
### <a name="bitor"></a>Funzione bitOr

**Descrizione:**  
La funzione BitOr imposta bit specificato in un valore.

**Sintassi:**  
`num BitOr(num value1, num value2)`

- val1; val2: valori numerici che non devono essere sottoposti OR

**Note:**  
Questa funzione converte entrambi i parametri per la rappresentazione binaria e imposta un bit su 1 se uno o entrambi i bit corrispondenti nella maschera e contrassegno sono 1 e 0 se entrambi i bit corrispondente è 0. In altre parole, restituisce 1 in tutti i casi eccetto nel punto in cui i bit corrispondenti di entrambi i parametri sono 0.

----------
### <a name="cbool"></a>CBool

**Descrizione:**  
La funzione CBool restituisce un valore Boolean in base all'espressione valutata

**Sintassi:**  
`bool CBool(exp Expression)`

**Note:**  
Se l'espressione restituisce un valore diverso da zero, quindi CBool restituisce True, altrimenti restituisce FALSO.

**Esempio:**  
`CBool([attrib1] = [attrib2])`  

Restituisce VERO se entrambi gli attributi hanno lo stesso valore.

----------
### <a name="cdate"></a>CDate

**Descrizione:**  
La funzione CDate restituisce un valore DateTime UTC da una stringa. DateTime non è un tipo di attributo nativo aggiornato ma viene utilizzato da alcune funzioni.

**Sintassi:**  
`dt CDate(str value)`

- Valore: Una stringa con una data, ora e facoltativamente fuso orario

**Note:**  
La stringa restituita è sempre in formato UTC.

**Esempio:**  
`CDate([employeeStartTime])`  
Restituisce che un valore DateTime base per il dipendente ora di inizio

`CDate("2013-01-10 4:00 PM -8")`  
Restituisce un DateTime che rappresenta "2013-01-11 12:00 AM"

----------
### <a name="cguid"></a>CGuid

**Descrizione:**  
La funzione CGuid Converte la rappresentazione di un GUID in rappresentazione binaria.

**Sintassi:**  
`bin CGuid(str GUID)`

- Una stringa formattata con questo modello: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx o {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

----------
### <a name="contains"></a>Contiene

**Descrizione:**  
La funzione Contains trova una stringa all'interno di un attributo multivalore

**Sintassi:**  
`num Contains (mvstring attribute, str search)`-maiuscole/minuscole  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`-maiuscole/minuscole

- attributo: attributo multivalore eseguire la ricerca.
- ricerca: stringa per trovare l'attributo.
- Casetype: CaseInsensitive o viene infatti.

Restituisce l'indice nell'attributo multivalore nel punto in cui è stata trovata la stringa. Se la stringa non viene trovata, viene restituito 0.

**Note:**  
Per gli attributi di stringa multivalore, la ricerca trova sottostringhe nella casella valori.  
Per gli attributi di riferimento, la stringa di ricerca deve corrispondere esattamente al valore per essere considerato una corrispondenza.

**Esempio:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Se l'attributo proxyAddresses ha un indirizzo di posta elettronica principale (indicato da maiuscolo "SMTP:"), quindi tornare l'attributo proxyAddress, altrimenti restituisce un errore.

----------
### <a name="convertfrombase64"></a>ConvertFromBase64

**Descrizione:**  
La funzione ConvertFromBase64 converte il valore di base 64 specificato codificato in una stringa normale.

**Sintassi:**  
`str ConvertFromBase64(str source)`-prende in considerazione per la codifica Unicode  
`str ConvertFromBase64(str source, enum Encoding)`

- origine: stringa con codifica base 64  
- Codifica: UTF8 Unicode, ASCII,

**Esempio**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Entrambi gli esempi restituiscono "*Hello world!*"

----------
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex

**Descrizione:**  
La funzione ConvertFromUTF8Hex converte il valore esadecimale UTF8 specificato in una stringa.

**Sintassi:**  
`str ConvertFromUTF8Hex(str source)`

- origine: UTF8 2 byte codificato stringa

**Note:**  
La differenza tra questa funzione e ConvertFromBase64([],UTF8) in cui il risultato è descrittivo per l'attributo nome distinto.  
In questo formato viene utilizzato da Azure Active Directory come nome distinto.

**Esempio:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Restituisce "*Hello world!*"

----------
### <a name="converttobase64"></a>ConvertToBase64

**Descrizione:**  
La funzione ConvertToBase64 converte una stringa in una stringa di base 64 Unicode.  
Converte il valore di una matrice di numeri interi in rappresentazione stringa equivalente codificata con cifre base 64.

**Sintassi:**  
`str ConvertToBase64(str source)`

**Esempio:**  
`ConvertToBase64("Hello world!")`  
Restituisce "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

----------
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex

**Descrizione:**  
La funzione ConvertToUTF8Hex converte una stringa in un valore esadecimale UTF8.

**Sintassi:**  
`str ConvertToUTF8Hex(str source)`

**Note:**  
Formato di output di questa funzione è utilizzato da Azure Active Directory come formato attributo nome distinto.

**Esempio:**  
`ConvertToUTF8Hex("Hello world!")`  
Restituisce 48656C6C6F20776F726C6421

----------
### <a name="count"></a>Conta. numeri

**Descrizione:**  
La funzione Conta numeri restituisce il numero di elementi in un attributo multivalore

**Sintassi:**  
`num Count(mvstr attribute)`

----------
### <a name="cnum"></a>CNum

**Descrizione:**  
La funzione CNum accetta una stringa e restituisce un tipo di dati numerici.

**Sintassi:**  
`num CNum(str value)`

----------
### <a name="cref"></a>CRef

**Descrizione:**  
Converte una stringa in un attributo di riferimento

**Sintassi:**  
`ref CRef(str value)`

**Esempio:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

----------
### <a name="cstr"></a>CStr

**Descrizione:**  
La funzione CStr converte in un tipo di dati string.

**Sintassi:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

- valore: può essere un valore numerico, l'attributo di riferimento o Boolean.

**Esempio:**  
`CStr([dn])`  
Può restituire "cn = Fausto, Cc = contoso, Cc = com"

----------
### <a name="dateadd"></a>DateAdd

**Descrizione:**  
Restituisce una data che contiene una data in cui è stato aggiunto un intervallo di tempo specificato.

**Sintassi:**  
`dt DateAdd(str interval, num value, dt date)`

- intervallo: espressione stringa che rappresenta l'intervallo di tempo da aggiungere. La stringa deve avere uno dei seguenti valori:
 - yyyy anno
 - domande trimestre
 - m mese
 - y giorno dell'anno
 - g giorno
 - w del giorno della settimana
 - ss settimana
 - h ore
 - n minuti
 - s secondo
- valore: il numero di unità da aggiungere. Può essere positivo (per ottenere date future) o negativo (per ottenere date passate).
- Data: DateTime che rappresenta data in cui è stata aggiunta l'intervallo.

**Esempio:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Aggiunge 3 mesi e restituisce un valore DateTime che rappresenta "2001-04-01".

----------
### <a name="datefromnum"></a>DateFromNum

**Descrizione:**  
Converte funzione DateFromNum formattare un valore data di Active Directory a un tipo di DateTime.

**Sintassi:**  
`dt DateFromNum(num value)`

**Esempio:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Restituisce un valore DateTime che rappresenta 2012-01 / 01 23:00:00

----------
### <a name="dncomponent"></a>DNComponent

**Descrizione:**  
La funzione DNComponent restituisce il valore di un componente di nome distinto specificato da sinistra.

**Sintassi:**  
`str DNComponent(ref dn, num ComponentNumber)`

- nome distinto: l'attributo di riferimento per interpretare
- ComponentNumber: Il componente di nome distinto per restituire

**Esempio:**  
`DNComponent([dn],1)`  
Se nome distinto "cn = Fausto, ou =...," restituirà Fausto

----------
### <a name="dncomponentrev"></a>DNComponentRev

**Descrizione:**  
La funzione DNComponentRev restituisce il valore di un componente di nome distinto specificato da destra (fine).

**Sintassi:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- nome distinto: l'attributo di riferimento per interpretare
- ComponentNumber - il componente di nome distinto per restituire
- Opzioni: Cc: ignorare tutti i componenti con "controller di dominio ="

**Esempio:**  
Se nome distinto "cn = Fausto, ou = Atlanta, ou = GA, ou = US, Cc = contoso, controller di dominio = com" quindi  
`DNComponentRev([dn],3)`  
`DNComponentRev([dn],1,"DC")`  
Aiutaci a restituiscono.

----------
### <a name="error"></a>Errore

**Descrizione:**  
La funzione di errore viene utilizzata per restituire un errore personalizzato.

**Sintassi:**  
`void Error(str ErrorMessage)`

**Esempio:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Se il nome account dell'attributo non è presente, genera un errore nell'oggetto.

----------
### <a name="escapedncomponent"></a>EscapeDNComponent

**Descrizione:**  
La funzione EscapeDNComponent accetta un componente di un nome distinto e in modo che può essere rappresentato in LDAP, lo ignora.

**Sintassi:**  
`str EscapeDNComponent(str value)`

**Esempio:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Verifica che l'oggetto possa essere creato in una directory LDAP anche se l'attributo displayName contiene caratteri che devono essere codificati in LDAP.

----------
### <a name="formatdatetime"></a>FormatDateTime

**Descrizione:**  
Funzione FormatDateTime viene utilizzata per formattare un valore DateTime in una stringa con un formato specificato

**Sintassi:**  
`str FormatDateTime(dt value, str format)`

- valore: un valore nel formato DateTime
- formato: una stringa che rappresenta il formato da convertire in.

**Note:**  
I valori possibili per il formato sono disponibili qui: [Formati di data/ora definiti dall'utente (funzione Format)](http://msdn2.microsoft.com/library/73ctwf33\(VS.90\).aspx)

**Esempio:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Risultato "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Causando "20140905081453.0Z"

----------
### <a name="guid"></a>GUID

**Descrizione:**  
La funzione GUID genera un nuovo GUID casuale

**Sintassi:**  
`str GUID()`

----------
### <a name="iif"></a>IIF

**Descrizione:**  
La funzione IIF restituisce un insieme di valori in base a una condizione specificata.

**Sintassi:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condizione: qualsiasi valore o espressione tramite cui viene restituito true o false.
- se_vero: se la condizione restituisce true, il valore restituito.
- argomento se_falso quando test: se la condizione restituisce FALSO, il valore restituito.

**Esempio:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Se l'utente è un interno, restituisce l'alias di un utente con "t-" aggiunto all'inizio di esso, altre l'alias dell'utente come.

----------
### <a name="instr"></a>InStr

**Descrizione:**  
Funzione InStr trovata la prima occorrenza di una sottostringa in una stringa

**Sintassi:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- StringCheck: stringa da ricercare
- StringMatch: stringa da ricercare
- avviare: posizione per trovare la sottostringa iniziale
- confronto tra: vbTextCompare o vbBinaryCompare

**Note:**  
Restituisce la posizione in cui è stata trovata la sottostringa o 0 se non viene trovato.

**Esempio:**  
`InStr("The quick brown fox","quick")`  
Visualizzate a 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Restituisce 7

----------
### <a name="instrrev"></a>Funzione InStrRev

**Descrizione:**  
Funzione InStrRev trova l'ultima occorrenza di una sottostringa in una stringa

**Sintassi:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- StringCheck: stringa da ricercare
- StringMatch: stringa da ricercare
- avviare: posizione per trovare la sottostringa iniziale
- confronto tra: vbTextCompare o vbBinaryCompare

**Note:**  
Restituisce la posizione in cui è stata trovata la sottostringa o 0 se non viene trovato.

**Esempio:**  
`InStrRev("abbcdbbbef","bb")`  
Restituisce 7

----------
### <a name="isbitset"></a>IsBitSet

**Descrizione:**  
La funzione IsBitSet verifica se un po' è impostata o meno

**Sintassi:**  
`bool IsBitSet(num value, num flag)`

- valore: un valore numerico è evaluated.flag: un valore numerico che contiene il bit da valutare

**Esempio:**  
`IsBitSet(&HF,4)`  
Restituisce vero poiché bit "4" è impostato il valore esadecimale "F"

----------
### <a name="isdate"></a>IsDate

**Descrizione:**  
Se l'espressione può essere valuta come tipo DateTime, la funzione IsDate restituisce True.

**Sintassi:**  
`bool IsDate(var Expression)`

**Note:**  
Consente di determinare se CDate() può essere completato.

----------
### <a name="isempty"></a>IsEmpty

**Descrizione:**  
Se l'attributo è presenta nel CS o MV ma restituisce una stringa vuota, quindi la funzione IsEmpty restituisce True.

**Sintassi:**  
`bool IsEmpty(var Expression)`

----------
### <a name="isguid"></a>IsGuid

**Descrizione:**  
Se la stringa può essere convertita in un GUID, la funzione IsGuid valutata su true.

**Sintassi:**  
`bool IsGuid(str GUID)`

**Note:**  
GUID è definito come stringa seguendo uno di questi modelli: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx o {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Consente di determinare se CGuid() può essere completato.

**Esempio:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Se il StrAttribute ha un formato GUID, restituire una rappresentazione in formato binario, in caso contrario restituisce un valore Null.

----------
### <a name="isnull"></a>IsNull

**Descrizione:**  
Se l'espressione restituisce Null, la funzione IsNull restituisce true.

**Sintassi:**  
`bool IsNull(var Expression)`

**Note:**  
Per un attributo, un valore Null è espresso l'assenza dell'attributo.

**Esempio:**  
`IsNull([displayName])`  
Restituisce VERO se l'attributo non è presenta nel CS o MV.

----------
### <a name="isnullorempty"></a>IsNullOrEmpty

**Descrizione:**  
Se l'espressione è null o una stringa vuota, la funzione IsNullOrEmpty restituisce true.

**Sintassi:**  
`bool IsNullOrEmpty(var Expression)`

**Note:**  
Per un attributo, si preferisce restituiscono True se l'attributo è presente o è presenta ma è una stringa vuota.  
L'inversa della funzione denominato IsPresent.

**Esempio:**  
`IsNullOrEmpty([displayName])`  
Restituisce VERO se l'attributo non è presenta o non è una stringa vuota nella SC o MV.

----------
### <a name="isnumeric"></a>IsNumeric

**Descrizione:**  
La funzione IsNumeric restituisce un valore Boolean che indica se un'espressione può essere valutata come un tipo di numero.

**Sintassi:**  
`bool IsNumeric(var Expression)`

**Note:**  
Consente di determinare se CNum() può essere completato per analizzare il valore di expression.

----------
### <a name="isstring"></a>IsString

**Descrizione:**  
Se l'espressione può essere valutata a un tipo di stringa, quindi la funzione IsString restituisce True.

**Sintassi:**  
`bool IsString(var expression)`

**Note:**  
Consente di determinare se CStr () può essere completato per analizzare il valore di expression.

----------
### <a name="ispresent"></a>IsPresent

**Descrizione:**  
Se l'espressione restituisce una stringa che non è Null e non è vuoto, la funzione IsPresent restituisce true.

**Sintassi:**  
`bool IsPresent(var expression)`

**Note:**  
L'inversa della funzione denominato IsNullOrEmpty.

**Esempio:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

----------
### <a name="item"></a>Elemento

**Descrizione:**  
La funzione elemento restituisce un singolo elemento da un stringa/attributo multivalore.

**Sintassi:**  
`var Item(mvstr attribute, num index)`

- attributo: attributo multivalore
- indice: indice di un elemento nella stringa multivalore.

**Note:**  
La funzione Item è utile con la funzione Contains poiché la funzione di quest'ultima restituisce l'indice di un elemento nell'attributo multivalore.

Se indice è compreso nell'intervallo, genera un errore.

**Esempio:**  
`Mid(Item([proxyAddress],Contains([proxyAddress], "SMTP:")),6)`  
Restituisce l'indirizzo di posta elettronica principale.

----------
### <a name="itemornull"></a>ItemOrNull

**Descrizione:**  
La funzione ItemOrNull restituisce un singolo elemento da un stringa/attributo multivalore.

**Sintassi:**  
`var ItemOrNull(mvstr attribute, num index)`

- attributo: attributo multivalore
- indice: indice di un elemento nella stringa multivalore.

**Note:**  
La funzione ItemOrNull è utile con la funzione Contains poiché la funzione di quest'ultima restituisce l'indice di un elemento nell'attributo multivalore.

Se indice è compreso nell'intervallo, viene restituito un valore Null.

----------
### <a name="join"></a>Join

**Descrizione:**  
Funzione Join accetta una stringa multivalore e restituisce una stringa a valore singolo con separatore specificato inserita tra ogni elemento.

**Sintassi:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

- attributo: attributo multivalore contenente stringhe da unire.
- delimitatore: qualsiasi stringa, utilizzato per separare le sottostringhe nella stringa restituita. Se omesso, il carattere di spazio ("") viene utilizzato. Se delimitatore è una stringa di lunghezza zero ("") o niente, tutti gli elementi dell'elenco vengono concatenati senza delimitatori.

**Note**  
Esiste uniformità tra le funzioni Join e divisione. La funzione Join utilizza una matrice di stringhe e li unisce con un delimitatore per restituire un'unica stringa. La funzione Split accetta una stringa e la separa in corrispondenza del delimitatore per restituire una matrice di stringhe. Tuttavia, la differenza principale è che partecipa può concatenare stringhe con qualsiasi delimitatore, divisione può separare le stringhe mediante un singolo carattere delimitatore.

**Esempio:**  
`Join([proxyAddresses],",")`  
Può restituire:"SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

----------
### <a name="lcase"></a>Funzione LCase

**Descrizione:**  
Funzione LCase converte tutti i caratteri in una stringa in lettere minuscole.

**Sintassi:**  
`str LCase(str value)`

**Esempio:**  
`LCase("TeSt")`  
Restituisce "test".

----------
### <a name="left"></a>A sinistra

**Descrizione:**  
Funzione Left restituisce un numero specificato di caratteri a sinistra di una stringa.

**Sintassi:**  
`str Left(str string, num NumChars)`

- stringa: la stringa di caratteri da restituire
- NumChars: un numero che identifica il numero di caratteri da restituire dall'inizio (a sinistra) della stringa

**Note:**  
Stringa contenente i primi caratteri numChars nella stringa:

- Se numChars = 0, restituire una stringa vuota.
- Se numChars < 0, tornare stringa di input.
- Se stringa null, restituisce una stringa vuota.

Se stringa contiene caratteri inferiore rispetto numChars di specificato nel numero, viene restituita una stringa identica a stringa (che è, contenente tutti i caratteri nel parametro 1).

**Esempio:**  
`Left("John Doe", 3)`  
Restituisce "Joh".

----------
### <a name="len"></a>Lunghezza

**Descrizione:**  
La funzione lunghezza restituisce numero di caratteri di una stringa.

**Sintassi:**  
`num Len(str value)`

**Esempio:**  
`Len("John Doe")`  
Restituisce 8

----------
### <a name="ltrim"></a>Funzioni LTrim

**Descrizione:**  
La funzione LTrim rimuove gli spazi vuoti iniziali da una stringa.

**Sintassi:**  
`str LTrim(str value)`

**Esempio:**  
`LTrim(" Test ")`  
Restituisce "Test"

----------
### <a name="mid"></a>Stringa. Estrai

**Descrizione:**  
Funzione Mid restituisce un numero specificato di caratteri da una posizione specificata di una stringa.

**Sintassi:**  
`str Mid(str string, num start, num NumChars)`

- stringa: la stringa di caratteri da restituire
- avviare: un numero che identifica il primo posizionare nella stringa di caratteri da restituire
- NumChars: un numero che identifica il numero di caratteri da restituire dalla posizione nella stringa

**Note:**  
Restituire caratteri numChars a partire dall'inizio alla posizione nella stringa.  
Stringa contenente i caratteri numChars dall'inizio alla posizione nella stringa:

- Se numChars = 0, restituire una stringa vuota.
- Se numChars < 0, tornare stringa di input.
- Se start > la lunghezza della stringa, tornare stringa di input.
- Se inizio < = 0, restituire una stringa di input.
- Se stringa null, restituisce una stringa vuota.

Se non esistono numChar caratteri rimanenti in stringa dall'inizio alla posizione, come numero di caratteri possibili vengono restituiti.

**Esempio:**  
`Mid("John Doe", 3, 5)`  
Restituisce "hn eseguire".

`Mid("John Doe", 6, 999)`  
Restituisce "Rossi"

----------
### <a name="now"></a>A questo punto

**Descrizione:**  
La funzione adesso restituisce un valore DateTime che specifica la data e ora correnti, in base alla data di sistema e l'ora del computer.

**Sintassi:**  
`dt Now()`

----------
### <a name="numfromdate"></a>NumFromDate

**Descrizione:**  
La funzione NumFromDate restituisce una data nel formato di data Active Directory.

**Sintassi:**  
`num NumFromDate(dt value)`

**Esempio:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Restituisce 129699324000000000

----------
### <a name="padleft"></a>PadLeft

**Descrizione:**  
I PadLeft funzione sinistra-aggiungere una stringa in un determinato periodo utilizzando un carattere di riempimento specificato.

**Sintassi:**  
`str PadLeft(str string, num length, str padCharacter)`

- stringa: la stringa per impostare la spaziatura.
- lunghezza: un numero intero che rappresenta la lunghezza della stringa desiderata.
- padCharacter: una stringa composta da un singolo carattere da utilizzare come carattere di riempimento

**Note:**

- Se la lunghezza della stringa è minore di lunghezza, padCharacter viene aggiunto più volte fino all'inizio (a sinistra) della stringa fino a quando non ha una lunghezza uguale alla lunghezza.
- PadCharacter può essere uno spazio, ma non può essere un valore null.
- Se la lunghezza della stringa è uguale o maggiore di lunghezza, viene restituita una stringa invariato.
- Se stringa di lunghezza maggiore o uguale alla lunghezza, viene restituita una stringa identica a stringa.
- Se la lunghezza della stringa è minore di lunghezza, una nuova stringa di lunghezza desiderata viene restituita stringa contenente riempito con un padCharacter.
- Se stringa è null, la funzione restituisce una stringa vuota.

**Esempio:**  
`PadLeft("User", 10, "0")`  
Restituisce "000000User".

----------
### <a name="padright"></a>PadRight

**Descrizione:**  
I PadRight funzione destra-aggiungere una stringa in un determinato periodo utilizzando un carattere di riempimento specificato.

**Sintassi:**  
`str PadRight(str string, num length, str padCharacter)`

- stringa: la stringa per impostare la spaziatura.
- lunghezza: un numero intero che rappresenta la lunghezza della stringa desiderata.
- padCharacter: una stringa composta da un singolo carattere da utilizzare come carattere di riempimento

**Note:**

- Se la lunghezza della stringa è minore di lunghezza, quindi padCharacter è più volte alla fine (a destra) della stringa fino a quando non ha una lunghezza uguale alla lunghezza.
- padCharacter può essere uno spazio, ma non può essere un valore null.
- Se la lunghezza della stringa è uguale o maggiore di lunghezza, viene restituita una stringa invariato.
- Se stringa di lunghezza maggiore o uguale alla lunghezza, viene restituita una stringa identica a stringa.
- Se la lunghezza della stringa è minore di lunghezza, una nuova stringa di lunghezza desiderata viene restituita stringa contenente riempito con un padCharacter.
- Se stringa è null, la funzione restituisce una stringa vuota.

**Esempio:**  
`PadRight("User", 10, "0")`  
Restituisce "User000000".

----------
### <a name="pcase"></a>PCase

**Descrizione:**  
La funzione PCase converte il primo carattere di ogni parola delimitato da spazio in una stringa in lettere maiuscole e tutti gli altri caratteri vengono convertite in lettere minuscole.

**Sintassi:**  
`String PCase(string)`

**Note:**

- Questa funzione non è attualmente disponibili iniz maiuscole e minuscole per convertire una parola interamente in maiuscolo, ad esempio un acronimo.

**Esempio:**  
`PCase("TEsT")`  
Restituisce "Test".

`PCase(LCase("TEST"))`  
Restituisce "Test"

----------
### <a name="randomnum"></a>RandomNum

**Descrizione:**  
La funzione RandomNum restituisce un numero casuale compreso tra un intervallo di tempo specificato.

**Sintassi:**  
`num RandomNum(num start, num end)`

- avviare: un numero che identifica il limite inferiore del valore casuale per generare
- fine: un numero che identifica il limite massimo di valore casuale per generare

**Esempio:**  
`Random(100,999)`  
Può restituire 734.

----------
### <a name="removeduplicates"></a>RemoveDuplicates

**Descrizione:**  
La funzione RemoveDuplicates accetta una stringa multivalore e verificare che ogni valore sia univoco.

**Sintassi:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Esempio:**  
`RemoveDuplicates([proxyAddresses])`  
Restituisce un attributo proxyAddress puro in cui sono stati rimossi tutti i valori duplicati.

----------
### <a name="replace"></a>Sostituisci

**Descrizione:**  
La funzione RIMPIAZZA sostituisce tutte le occorrenze di una stringa in un'altra stringa.

**Sintassi:**  
`str Replace(str string, str OldValue, str NewValue)`

- stringa: sostituire i valori in una stringa.
- Vecchio valore: La stringa da cercare e sostituire.
- NuovoValore: La stringa da sostituire a.

**Note:**  
La funzione riconosce i moniker speciali seguenti:

- \n-nuova riga
- \r-ritorno a capo
- \t-scheda

**Esempio:**  
`Replace([address],"\r\n",", ")`  
Sostituisce CRLF con una virgola e uno spazio e portare a "One Microsoft modo, Redmond, WA, USA"

----------
### <a name="replacechars"></a>ReplaceChars

**Descrizione:**  
La funzione ReplaceChars sostituisce tutte le occorrenze dei caratteri nella stringa di ReplacePattern trovati.

**Sintassi:**  
`str ReplaceChars(str string, str ReplacePattern)`

- stringa: sostituire i caratteri in una stringa.
- ReplacePattern: una stringa contenente un dizionario con caratteri da sostituire.

Il formato è {source1}: {target1}, {source2}: {target2}, {sourceN}, {targetN} dove origine è il carattere per trovare e sostituire con la stringa di destinazione.

**Note:**

- La funzione accetta tutte le occorrenze di origini definite e li sostituisce con le destinazioni.
- L'origine deve essere esattamente un carattere (unicode).
- L'origine non può essere vuoto o più di un carattere (errore di analisi).
- La destinazione può contenere più caratteri, ad esempio ö:oe, β:ss.
- La destinazione può essere vuota che indica che il carattere deve essere rimosso.
- L'origine tra maiuscole e minuscole e deve essere una corrispondenza esatta.
- (Virgola) e: (due punti) sono caratteri riservati e non può essere sostituito con questa funzione.
- Gli spazi e altri caratteri nella stringa di ReplacePattern bianchi vengono ignorati.

**Esempio:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Restituisce Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Restituisce "ONeil" singolo segno di graduazione è definito per richiedere la rimozione.

----------
### <a name="right"></a>Ok

**Descrizione:**  
Funzione Right restituisce un numero specificato di caratteri da destra (fine) di una stringa.

**Sintassi:**  
`str Right(str string, num NumChars)`

- stringa: la stringa di caratteri da restituire
- NumChars: un numero che identifica il numero di caratteri da restituire dalla fine (a destra) della stringa

**Note:**  
NumChars caratteri vengono restituiti dall'ultima posizione della stringa.

Stringa contenente gli ultimi caratteri di numChars nella stringa:

- Se numChars = 0, restituire una stringa vuota.
- Se numChars < 0, tornare stringa di input.
- Se stringa null, restituisce una stringa vuota.

Se stringa contiene caratteri inferiore rispetto NumChars di specificato nel numero, viene restituita una stringa identica a stringa.

**Esempio:**  
`Right("John Doe", 3)`  
Restituisce "Dellamore".

----------
### <a name="rtrim"></a>RTrim

**Descrizione:**  
La funzione RTrim rimuove gli spazi finali da una stringa.

**Sintassi:**  
`str RTrim(str value)`

**Esempio:**  
`RTrim(" Test ")`  
Restituisce "Test".

----------
### <a name="split"></a>Divisione

**Descrizione:**  
La funzione Split accetta una stringa separata da un delimitatore e rende una stringa multivalore.

**Sintassi:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

- valore: la stringa con un carattere delimitatore per separare.
- delimitatore: singolo carattere da utilizzare come delimitatore.
- limite: numero massimo di valori che può restituire.

**Esempio:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Restituisce una stringa multivalore con 2 elementi utili per l'attributo proxyAddress.

----------
### <a name="stringfromguid"></a>StringFromGuid

**Descrizione:**  
La funzione StringFromGuid accetta binarie GUID e lo converte in una stringa

**Sintassi:**  
`str StringFromGuid(bin GUID)`

----------
### <a name="stringfromsid"></a>StringFromSid

**Descrizione:**  
La funzione StringFromSid converte una matrice di byte contenente un identificatore di protezione in una stringa.

**Sintassi:**  
`str StringFromSid(bin ObjectSID)`  

----------
### <a name="switch"></a>Opzione

**Descrizione:**  
Funzione Switch viene utilizzata per restituire un singolo valore in base a condizioni valutate.

**Sintassi:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- espr: espressione Variant da valutare.
- valore: valore da restituire se l'espressione corrispondente è vero.

**Note:**  
L'elenco di argomenti di funzione Switch è costituito da coppie di valori e le espressioni. Le espressioni vengono valutate da sinistra a destra e viene restituito il valore associato alla prima espressione che restituisce True. Se le parti non vengono accoppiate correttamente, si verifica un errore di run-time.

Ad esempio, se Espr1 è vero, cambia restituirà val1. Se espr-1 è False ma espr-2 è vero, Switch restituirà valore 2 e così via.

Opzione restituisce un niente se:

- Nessuna delle espressioni hanno valore vero.
- La prima espressione True ha un valore corrispondente è Null.

Parametro viene valutato tutte le espressioni, anche se ne restituisce solo uno di essi. Per questo motivo, è necessario prestare attenzione ai potenziali. Ad esempio, se la valutazione di un'espressione dà come risultato una divisione per zero, si verifica un errore.

Valore può essere anche la funzione di errore, che restituisce una stringa personalizzata.

**Esempio:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Restituisce la lingua parlata in alcune città principali, altrimenti viene restituito un errore.

----------
### <a name="trim"></a>Tagliare

**Descrizione:**  
Consente di rimuovere la funzione Annulla spazi iniziali e finali spazi da una stringa.

**Sintassi:**  
`str Trim(str value)`  

**Esempio:**  
`Trim(" Test ")`  
Restituisce "Test".

`Trim([proxyAddresses])`  
Rimuove spazi per ogni valore dell'attributo proxyAddress iniziali e finali.

----------
### <a name="ucase"></a>Funzione UCase

**Descrizione:**  
Funzione UCase converte tutti i caratteri in una stringa in lettere maiuscole.

**Sintassi:**  
`str UCase(str string)`

**Esempio:**  
`UCase("TeSt")`  
Restituisce "TEST".

----------
### <a name="word"></a>Word

**Descrizione:**  
La funzione di Word restituisce una parola contenuta in una stringa, in base a cui vengono descritti i delimitatori da utilizzare e il numero di word per restituire i parametri.

**Sintassi:**  
`str Word(str string, num WordNumber, str delimiters)`

- stringa: la stringa per restituire una parola da.
- WordNumber: deve restituire un numero che identifica il numero di word.
- delimitatori: una stringa che rappresenta la delimiter(s) che deve essere utilizzato per identificare le parole

**Note:**  
Ogni stringa di caratteri nella stringa separati da uno dei caratteri in delimitatori vengono identificati come parole:

- Se num < 1, restituisce una stringa vuota.
- Se stringa è null, restituisce una stringa vuota.

Se stringa contiene minore numero di parole o stringa non contiene le parole identificate da delimitatori, viene restituita una stringa vuota.

**Esempio:**  
`Word("The quick brown fox",3," ")`  
Restituisce "Rossi"

`Word("This,string!has&many separators",3,",!&#")`  
Restituisce "contiene"

## <a name="additional-resources"></a>Risorse aggiuntive

* [Informazioni sulle espressioni di Provisioning dichiarative](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Azure Active Directory connettersi sincronizzazione: Le opzioni di personalizzazione di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
