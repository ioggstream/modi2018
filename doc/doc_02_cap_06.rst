Considerazioni comparative
==========================

Un primo criterio per orientarsi tra le tecnologie di integrazione presentate nelle Sezioni 3, 4 e 5 è quella di distinguere le tecnologie adatte per una interazione sincrona da quelle adatte ad una interazione asincrona. Riguardo a questa distinzione si può fare riferimento alla seguente tabella:

+---------------------------+----------+----------+---------------------+
|                           | **SOAP** | **REST** | **Message Broker**  |
+---------------------------+----------+----------+---------------------+
| **Interazione Sincrona**  |   ✓      |    ✓     |                     |
+---------------------------+----------+----------+---------------------+  
| **Interazione Asincrona** |  ✓\*     |   ✓\*    |    ✓                |
+---------------------------+----------+----------+---------------------+

SOAP (inteso come stack WS-\*), come si evince dalla tabella, può essere utilizzato sia per interazioni sincrone che per interazioni asincrone. 

In particolare, in SOAP, l'interazione asincrona può essere ottenuta sia su protocolli di trasporto sincroni che su protocolli di trasporto asincroni. Nonostante la specifica supporti questo genere di interazioni, il supporto di middleware e framework di sviluppo a queste funzionalità è limitato. Per quanto riguarda REST invece, nonostante non originariamente previsto dalla specifica, si è visto in Sezione 5 come esso venga utilizzato come interfaccia di servizio per message broker.

Per quanto riguarda l'interazione sincrona (stile chiamata a procedura o accesso CRUD a risorsa), diverse considerazioni tecnologiche devono essere effettuate. SOAP e REST utilizzano HTTP in due modi differenti.

Mentre SOAP lo utilizza come un protocollo di trasporto, REST lo utilizza come un protocollo applicativo. La diffusione dell\'accesso alla rete ha aumentato il carico sulle infrastrutture IT, inoltre reti migliori hanno aumentato le aspettative in termini di latenza. L\'IETF nel tempo ha risposto a queste esigenze:

-   migliorando la semantica di HTTP, facendo leva sui campi Header, Status e Method RFC7230 [84]_, RFC7231 [85]_;

-   codificando le semantiche di caching RFC7234 [86]_ e controllo della concorrenza RFC7232 [87]_ per facilitare l\'implementazione di interfacce di servizio stateless, che possano scalare senza che i bilanciatori conoscano la logica applicativa;

-   orientandosi verso formati più leggeri (ad es., JSON).

Queste innovazioni fanno preferire l\'approccio REST:

-   Quando è possibile esprimere la logica applicativa tramite la semantica HTTP, poiché si guadagna:

    -   espressività (ad es., il contesto d\'utilizzo è chiarito da Method e Status);

    -   mobile-ready (esporre un\'API in un\'app con un http-wrapper);

    -   performance e scalabilità (ad es., possibilità di ruotare le chiamate in base al Method, senza inspection applicativa).

-   Quando le API devono essere fruibili anche da mobile/web;

-   L'accesso avviene in maniera prevalente con operazioni di tipo CRUD sui dati.

Quindi rispetto a quanto discusso nel documento 1 sui paradigmi di cooperazione, questo suggerisce l'uso di REST nei casi di condivisione di dati e di composizione applicativa, quando le operazioni componenti sono prevalentemente orientate a fornire dati. Il servizio digitale corrispondente all'interfaccia di servizio è prevalentemente informativo (cf. Sezione 1).

L'utilizzo di SOAP è suggerito:

-   Quando la semantica HTTP non è sufficiente ad esprimere la logica applicativa ed è necessario usare un protocollo di messaging ulteriore con dei propri header;

-   Se la specifica applicazione richiede la creazione di interfacce di servizio principalmente *stateful*, cioè l'accesso ad informazioni di contesto o la gestione dello stato della conversazione [88]_. SOAP prevede estensioni (ad esempio relative al concetto di transazione) che con altri approcci (ad es., REST) devono essere costruite ad-hoc per la specifica applicazione. 

-   Nel caso si necessiti di processamento asincrono che non sia possibile implementare con semantiche HTTP;

-   Quando servono specifiche assicurazioni circa la QoS (quali quelle fornite dall'estensione WS-ReliableMessaging).

Quindi rispetto a quanto discusso nel Documento 1 sui paradigmi di cooperazione, questo suggerisce l'uso di SOAP nei casi di processo inter-PA e di composizione applicativa quando le operazioni componenti offrono delle logiche complesse.

La tabella seguente riporta alcuni aspetti tecnologici che devono essere tenuti in considerazione (le celle in cui è presente "-" indicano che l'aspetto in questione non è considerato e standardizzato, e quindi è a cura dello specifico progetto/applicazione indirizzarlo attraverso sviluppi ad-hoc)

+-----------------------+-----------------------+-----------------------+
|                       | **SOAP (WS-\*)**      | **REST**              |
+=======================+=======================+=======================+
| **Formato Payload**   | XML                   | Tutti (JSON nella     |
|                       |                       | maggior parte dei     |
|                       |                       | casi)                 |
+-----------------------+-----------------------+-----------------------+
| **Identificazione     | URI, WS-Addressing    | URI                   |
| delle operazioni**    |                       |                       |
+-----------------------+-----------------------+-----------------------+
| **Descrizione delle   | WSDL                  | RAML, OpenAPI         |
| interfacce di         |                       |                       |
| servizio**            |                       |                       |
+-----------------------+-----------------------+-----------------------+
| **Affidabilità**      | WS-ReliableMessaging  | \-                    |
+-----------------------+-----------------------+-----------------------+
| **Sicurezza**         | HTTPS, WS-Security    | HTTPS, JWT            |
+-----------------------+-----------------------+-----------------------+
| **Transazioni**       | WS-AtomicTransaction, | \-                    |
|                       | WS-BusinessActivity   |                       |
+-----------------------+-----------------------+-----------------------+
| **Composizione di     | WS-Choreography       | \-                    |
| interfacce di         |                       |                       |
| servizio**            | WS-BPEL               |                       |
+-----------------------+-----------------------+-----------------------+

In letteratura, talvolta si distingue la metodologia di progetto in approccio *contract-first* (SOAP) e *contract-less* (REST) [89]_. Utilizzando SOAP, lo sviluppo di un'interfaccia di servizio origina dalla definizione dell'interfaccia, mentre in REST l'interfaccia è definita, in quanto REST di fatto offre un template di interfaccia basata su operazioni CRUD, ma vanno identificate le risorse.

La differenza appare ininfluente nel caso di progettazione e realizzazione di sistemi nuovi, ma non in presenza di sistemi legacy. Quando l'interfaccia di servizio è vincolata dalla presenza di un sistema esistente, l'interfaccia di servizio è definita a posteriori rispetto all'implementazione (modalità questa che potrebbe essere indicata come *contract-last*) e quindi si desidera avere la massima flessibilità nel definirla, come nel caso di SOAP, e non essere vincolati ad un template predefinito, come in REST (si avrebbe un *mismatch*, in quanto è difficile in generale mappare risorse su procedure legacy). Quindi in questo caso il contract-first di SOAP
appare naturalmente semplificare il lavoro di modellazione e successiva realizzazione dell'interfaccia di servizio.

Nel caso invece di nuovi sistemi, la progettazione dell'interfaccia può essere effettuata sia in un'ottica contract-first che contract-less. In un'ottica contract-first, la specifica dell'interfaccia viene effettuata
a tavolino a partire dalle macro-operazioni che si vogliono offerte dal sistema finale. Nel caso di accesso basato su risorsa (in ottica ROA), essendo in realtà le operazioni da effettuare già predefinite
(operazioni CRUD), il tipo di progettazione è contract-less. Vanno però definite le risorse che il sistema deve esporre, quindi una qualche forma di progettazione preventiva all'implementazione è comunque
prevista (cioè, la specifica delle risorse).

Nel progetto di interfacce di servizio SOAP occorre definire, oltre alle macro-operazioni, anche le strutture XML per la rappresentazione dei dati. Le operazioni possono essere raggruppate in base a caratteristiche quali: area funzionale (o area di business), requisiti di sicurezza (ad es. meccanismi di autenticazione ed autorizzazione), oppure in base a fattori organizzativi quali la frequenza dei cambiamenti o pattern di gestione delle versioni. Il principio alla base di questo raggruppamento è quello di impattare il minor numero di fruitori quando avviene un cambiamento.

Nel progetto di interfacce di servizio REST invece occorre:

-   Identificare le risorse che l'interfaccia di servizio manipolerà. Queste risorse sono solitamente i concetti base che stanno dietro ad un processo (ad es., un ordine di acquisto).

-   Progettare gli URI seguendo i principi introdotti nella sezione relativa alla tecnologia REST.

-   Scegliere il tipo di operazione disponibile per ognuna delle URI.

-   Scegliere i collegamenti tra risorse da fornire nelle risposte. In quest'ottica l'approccio HATEOAS può risultare utile.

-   Progettare le strutture JSON per la rappresentazione dei dati.

Il ModI 2018, come discusso nella Sezione 1, prevede che la progettazione parta della definizione delle interfacce di servizio, indipendentemente dalla tecnologia di realizzazione. Nel modo REST, il principio secondo cui l'interfaccia di servizio (in questo caso l'API) deve essere il primo artefatto di progettazione, viene recentemente indicato come *API-first* [90]_ ed è largamente adottato da molte organizzazioni private, ed anche framework di interoperabilità nazionali come quello inglese [91]_. Emerge pertanto che indipendentemente dalla tecnologia, sia SOAP che REST supportano nel 2018 tale principio, anche se con accorgimenti tecnici differenti nella sua realizzazione.



.. [84] Cf. `https://tools.ietf.org/html/rfc7230 <https://tools.ietf.org/html/rfc7230>`_

.. [85] Cf. `https://tools.ietf.org/html/rfc7231 <https://tools.ietf.org/html/rfc7231>`_

.. [86] Cf. `https://tools.ietf.org/html/rfc7234 <https://tools.ietf.org/html/rfc7234>`_

.. [87] Cf. `https://tools.ietf.org/html/rfc7232 <https://tools.ietf.org/html/rfc7232>`_

.. [88] Come nel caso di processi amministrativi sia completamente automatizzati (short-running) sia con intervento umano o comunque long-running.

.. [89] Cf. Cesare Pautasso, Olaf Zimmermann, Frank Leymann: Restful web services vs. \"big\"\' web services: making the right architectural decision. WWW 2008: 805-814.

.. [90] Cf. `https://www.programmableweb.com/api-university/understanding-api-first-design <https://www.programmableweb.com/api-university/understanding-api-first-design>`_.
    In termini colloquiali, il principio può essere parafrasato in questi termini:

    - L'API è la prima interfaccia dell'applicazione

    - L'API viene prima dell'implementazione

    - L'API deve essere descritta (ed addirittura essere auto-descrittiva, se possibile e fattibile)

.. [91] Cf. `https://www.programmableweb.com/news/why-uks-government-data-service-takes-api-first-approach-to-datagovuk/elsewhere-web/2016/09/02 <https://www.programmableweb.com/news/why-uks-government-data-service-takes-api-first-approach-to-datagovuk/elsewhere-web/2016/09/02>`_
