### 1. Solr - wprowadzenie
Apache Solr jest aplikacją sieciową zbudowaną na silniku wyszukiwania Apache Lucene (którego używa np. Twitter w swojej szukajce). Lucene to tylko biblioteka, która działą pod maską Solr. 

Sam Solr wprowadza usprawnienia do Lucene, takie jak np.:

- API w XML i JSON
- Indeksowanie zapytań
- Cache
- Wyszukiwanie geolokacyjne
- Replikację
- Interfejs webowy dla admina

Solr to jest WAR. Może być zapakowany w dowolny servlet, np. Tomcat.

Warto dodać, że Solr ze względu swojego wieku (został napisany 2004, pięć lat po Lucene) ma wiele wrapperów i bibliotek obsługujących jego API językach skryptowych: Python, Ruby, PHP, etc.

### 2. Instalacja
Apache Solr potrzebuje Javy 7 lub nowszej. 
Mamy opcję instalacji z repozytorium maszyny (jako solr-common na Debianowych dystrybucjach lub odpowiednie alternatywy), na której instalujemy oprogramowanie lub bezpośrednio ze strony Solr: http://lucene.apache.org/solr/guide/7_6/installing-solr.html

Paczkę należy rozpakować i jesteśmy gotowi do uruchomienia oprogramowania.

### 3. Uruchomienie Solr
Założywszy, że mamy zainstalowaną Javę, która spełnia wymagania oprogramowania, Solr możemy uruchomić po prostu przez:

```
bin/solr start
```

Platforma przeprowadzi inicjalizację i po chwili powinniśmy być w stanie zobaczyć interfejs sieciowy pod adresem:

```angular2
http://localhost:8983/solr/#/
```

### 4. Koncepcja rdzeni
Aby umieć dokonywać indeksowania i wyszukiwania, Solr potrzebuje 1 lub więcej rdzeni. Rdzeń to nic innego jak instancja Apache Lucene. Solr w takim wypadku reprezentuje API, które wewnętrznie komunikuje się z Lucene i tworzy rdzenie, które z kolei są w stanie indeksować frazy. Rdzenie tworzymy przez:

bin/solr create -c examplecore

Historycznie, Lucene posiadało tylko jeden rdzeń (jako singleton), z czasem jednak stało się możliwe zakładanie wielu rdzeni, które mogą komunikować się ze sobą.


### 5. Operacje
Wszystkie operacje wykonujemy przez wywołanie bin/solr i przekazanie odpowiednich argumentów do programu.

Najprostsze komendy to version, healthcheck i assert, które odpowiednio sprawdzają wersję i obecny status instancji Solr na danej maszynie.

Ponadto, Solr ze swoim interfejsem webowym może obsługiwać autentykacje (Basic). Basic Authentication jest włączany lub wyłączany przez bin/solr auth enable lub disable.

Solr jest także w stanie tworzyć backupy.

### 5.1. Dodawanie danych do indeksu
Możliwe jest zaindeksowanie dokumentów w formacie XML, JSON, a także CSV. Solr posiada dwie drogi indeksowania dokumentów - przez narzędzie bin/post, które znajduje się w plikach dokumentów lub przez API sieciowe, które posiada Solr. 

bin/post to proste narzędzie powłoki, któremu podajemy w argumentach, co może zostać zaindeksowane, np.:

```angular2
bin/post -c gettingstarted *.xml
```

Oznacza podanie pliku w formacie XML do zaindeksowania przez rdzeń gettingstarted.

bin/post zlokalizuje instancję Solr i przekaże jej argumenty. Dane, które są podane do zaindeksowania odpowiadają prostemu 
schematowi, który opisuje id i pola, które ma dany dokument.
Alternatywnie, indeksowanie można przeprowadzić przez interfejs sieciowy (np. curl localhost:8983/solar/collection/update).

Metody: index, update, delete (różne typy - delete i update dla pojedynczego id, ale także dla zapytania).

W przypadku używania interfejsu sieciowego, używamy nagłówka content-type dla określenia formatu danych. 

Instancja Solr i jej interfejs oprogramowania jest domyślnie dostępna dla innych maszyn, jednak istnieje możliwość 
autentykacji (Basic Auth). Alternatywą byłoby użycie tabel IP do zastrzeżenia portu dla localhost.

### 5.2 Wyszukiwanie

Wyszukiwanie ma bardzo wiele opcji w Solr i ten raport skupi się na najważniejszych z nich. 

Kiedy użytkownik wpisuje frazę wyszukiwania, w interfejsie sieciowym, Solr wewnętrznie uruchamia tzw. RequestHandler,
który z kolei wywołuje parser. Dane wejściowe do parsera, oprócz zapytania tekstowego, to także parametry, dzięki którym
zapytanie parsera może zostać zmodyfikowane. Parametry mogą modyfikować zakres wyników, a także sposób, w jaki dane są 
prezentowane - np., jakie pola zaindeksowanych dokumentów zwrócić.

Podstawowa składnia wyszukiwania jest następująca:

```angular2
http://localhost:8983/solr/#/core/query
```

Gdzie core to rdzeń, który stworzyliśmy, a query to zapytanie. Parametry wyszukiwania wprowadzamy w URL (lub GET),
gdzie zostaną sparsowane i zaaplikowane do wyników, które otrzymujemy.

### 6. Alternatywy - krótkie porównanie

Największymi konkurentami dla Solr są: Algolia i Elasticsearch

###### Algolia
Algolia to hostowane rozwiązanie, które twierdzi, że jest szybsze od Lucene. Oferta outsourcingu wyszukiwania
zaczyna się od $35 miesięcznie i z pudełka daje support dla fuzzy search, inteligentne podkreślanie rezultatów,
i jest znacznie szybsze od Lucene (200x szybsze). Aloglia gwarantuje 99.9% uptime i jest to oferta,
która mierzy w średnie i większe firmy, które chcą zaoszczędzić na zasobach devops. Jako największą zaletę podaje to,
że rozwiązanie, które oferuje, jest już gotowe. Rozwiązania oparte na Lucene zawsze będą potrzebowały teamu, który
je obsłuży.

###### Elasticsearch
Elasticsearch to drugi największy konkurent Solr. Pod pewnymi względami przypomina Solr - także posiada restowe API,
obsługuje JSON i posiada nieco bogatsze wsparcie środowiskowe pod względem języków skryptowych (Ruby, Python, PHP, etc.).
Porównując Elasticsearch z Solr, Elasticsearch jest nieco uboższy i o wiele bardziej zorientowany na JSON niż Solr. 
Różnice pomiędzy tymi dwoma silnikami nie wydają się aż tak znaczące, poza wsparciem dla programistóœ - Solr jest głównie
skierowany dla programistów Javy. ES historycznie wypłynął na fali Rails i Node i środowiska z tym związanego. 
