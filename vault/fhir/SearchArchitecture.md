# Search architecture and implementation

## Extraction

SearchIndexer  will do all the extraction
![image info](https://raw.githubusercontent.com/microsoft/fhir-server/main/docs/images/Search/extraction-process.png)

1. CompositeSearchValue
2. DateTimeSearchValue
3. NumberSearchValue
4. QuantitySearchValue
5. ReferenceSearchValue
6. StringSearchValue
7. TokenSearchValue: System, code, and text will be stored as string values.
8. UriSearchValue

##  Persistence
the search values will be persisted separately from the original resource in an array format so that search can be done efficiently.

### Persistence format

## Search

ExpressionParser and Translate expressions into the corresponding query

## Reference

https://github.com/microsoft/fhir-server/blob/main/docs/SearchArchitecture.md[Search architecture and implementation]
