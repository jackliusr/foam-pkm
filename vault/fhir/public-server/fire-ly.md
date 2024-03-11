- https://server.fire.ly/?
  - query all
  ```bash
    curl -i -H "Accept: application/json" https://server.fire.ly/?_type=Observation,Condition
    #id will be different for each query, it cannot be used as cache key
  ```
