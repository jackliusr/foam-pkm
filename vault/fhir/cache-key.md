### Cache Key

Given cache is empty, I call IDataService.GetAsync with ResourceKey(ResourceType, id, null), then data should be in cached in the correct cache key format {ResourceType}/{Id},  data from cache should be same with data before the cache. Key resource types to test: Patient, Claim, CarePlan
  
   
Given cache is empty, I call IDataService.GetAsync with ResourceKey(ResourceType, id, VersionId), then data should be in cached in the correct cache key format {ResourceType}/{Id}/_history/{VersionId}, data from cache should be same with data before the cache.. 

Given cache is empty, I call ISearchService.SearchAsync with a query [{"param1": "value1" },{"param2","value2"}], then the result is cached with key "{ResourceType}?param1=value1&param2=value2" (order of parameters in the key is sorted), data from cache should be same with data before the cache.;

Given cache is empty, I call ISearchService.SearchCompartmentAsync with the query, then the result is cached with key "{CompartmentType}/{CompartmentId}/{ResourceType}?param1=value1&param2=value2" (order of parameters in the key is sorted), data from cache should be same with data before the cache;

Given above four cases and correct resource are cached, I call the same operations again, then results should from cache instead of data store.
