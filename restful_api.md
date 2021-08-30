## Restful API

Wanneer je een API gaat maken heb je het vaak over een Restful API. Deze draait om dezelfde techniek van een HTTP request en een HTTP response.

_HTTP Request_
- URL
- Method
- Header
- Body

_HTTP Response_
- Status code
- Header
- Body

### HTTP methods

We maken in een restful API gebruik van verschillende HTTP methods: GET, PUT, POST, DELETE. Met deze vier methodes kun je al je operaties op je database uitvoeren. GET is opvragen (select). PUT is creëren (create, een insert opdracht). POST is een update. Met DELETE kun je een record weghalen.

Door gebruik te maken van de verschillende HTTP methods heb je de mogelijkheid om de data in je database server aan te spreken.

### URI

De URL (of URI) die je gebruikt is de manier waarop je aanwijst wat je wilt gaan doen. Stel je hebt klanten en je wilt klanten toevoegen, dan zeg je `/klanten`, dan doe je een `PUT` en in de body geef je de informatie die je daar in wilt zetten.

Ander voorbeeld. Stel je houdt je bezig met boeken. Je doet als eerste een `GET` naar `/books` (dit is het adres van de API) en dan krijg je alle boeken terug die erin zitten.

| GET        | /books           | Read many  |
| ------------- |:-------------:| -----:|
	
Een POST gebruik je wanneer je een boek wilt toevoegen.

| POST        | /books           | Create  |
| ------------- |:-------------:| -----:|

Je kun GET ook gebruiken om 1 specifieke boek op te halen. Dan moet je een id meegeven.

| GET        | /books/{id}           | Read one  |
| ------------- |:-------------:| -----:|

PUT gebruik je om een update te doen van een bestaand boek.

| PUT        | /books/{id}           | Update  |
| ------------- |:-------------:| -----:|

PATCH is een partial update, net iets anders dan een PUT.

| PATCH        | /books/{id}           | Partial update  |
| ------------- |:-------------:| -----:|

Je kan een boek verwijderen met DELETE.

| DELETE        | /books/{id}           | Delete  |
| ------------- |:-------------:| -----:|

### Status code

Wat je terugkrijgt in je response is de status code (of het gelukt is of niet).

Dit zijn de meest voorkomende.

__200 OK__ <br/>
General success status code. This is the most common code. Used to indicate success.

__201 CREATED__ <br/>
Successful creation occurred (via either POST or PUT). Set the Location header to contain a link to the newly-created resource (on POST). Response body content may or may not be present.

__204 NO CONTENT__ <br/>
Indicates success but nothing is in the response body, often used for DELETE and PUT operations.

__400 BAD REQUEST__ <br/>
General error for when fulfilling the request would cause an invalid state. Domain validation errors, missing data, etc. are some examples.

__401 UNAUTHORIZED__ <br/>
Error code response for missing or invalid authentication token.

__403 FORBIDDEN__ <br/>
Error code for when the user is not authorized to perform the operation or the resource is unavailable for some reason (e.g. time constraints, etc.).

__404 NOT FOUND__ <br/>
Used when the requested resource is not found, whether it doesn't exist or if there was a 401 or 403 that, for security reasons, the service wants to mask.

__405 METHOD NOT ALLOWED__ <br/>
Used to indicate that the requested URL exists, but the requested HTTP method is not applicable. For example, POST _/users/12345_ where the API doesn't support creation of resources this way (with a provided ID). The Allow HTTP header must be set when returning a 405 to indicate the HTTP methods that are supported. In the previous case, the header would look like "Allow: GET, PUT, DELETE".

__409 CONFLICT__ <br/>
Whenever a resource conflict would be caused by fulfilling the request. Duplicate entries, such as trying to create two customers with the same information, and deleting root objects when cascade-delete is not supported are a couple of examples.

__500 INTERNAL SERVER ERROR__ <br/>
Never return this intentionally. The general catch-all error when the server-side throws an exception. Use this only for errors that the consumer cannot address from their end.

De volledige lijst kun je hier terug vinden: <a href="https://www.restapitutorial.com/httpstatuscodes.html" target="_blank">httpstatuscodes</a>.

## Bronnen

- <a href="https://restcookbook.com" target="_blank">restcookbook.com</a>
- <a href="https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/" target="_blank">best-practices-for-rest-api-design</a>
- <a href="https://dzone.com/articles/top-rest-api-best-practices" target="_blank">top-rest-api-best-practices</a>
- <a href="https://docs.microsoft.com/en-us/azure/architecture/best-practices/api-design" target="_blank">api-design</a>
- <a href="https://github.com/microsoft/api-guidelines" target="_blank">api-guidelines</a>
- <a href="https://mathieu.fenniak.net/the-api-checklist/" target="_blank">the-api-checklist/</a>
