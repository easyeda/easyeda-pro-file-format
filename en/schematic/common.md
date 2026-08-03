# Common Format

## Document Header

```json
{ "type": "DOCHEAD" }||{ "docType": "SCH_PAGE", "uuid": "UUID", "client": "clientID" }|
```

```json
{ "type": "DOCHEAD" }||{ "docType": "SYMBOL", "uuid": "UUID", "client": "clientID" }|
```

-   type: "DOCHEAD", document header identifier.
-   docType: document type, "SCH_PAGE": schematic, "SYMBOL": symbol.
-   uuid: document unique ID, unique within the project.
-   client: terminal identifier for eventual consistency.

## Canvas Configuration

Editor additional information, used for data analysis and other functions. Currently occupied fields:

```json
{ "type": "CANVAS", "ticket": 1 }||
{
  "originX":0,
  "originY":0,
}|
```

1. type: "CANVAS", canvas configuration identifier.
2. ticket logical clock.
3. originX canvas origin X.
4. originY canvas origin Y.
