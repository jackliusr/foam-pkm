# hapifhir

## interceptor framework

- Interceptor – A class such as the example above that has one or more Hook methods on it. An optional marker annotation called @Interceptor exists and can be used, but is not required.

-  Hook – An individual interceptor method that is invoked in response to a specific action taking place in the HAPI FHIR framework. Hook methods must be annotated with the @Hook annotation.

- Pointcut – A pointcut is a specific point in the HAPI FHIR processing pipeline that is being intercepted. Each Hook Method must declare which pointcut it is intercepting. All available pointcuts are defined by HAPI FHIR in the Pointcut enum.

- Hook Params – Every Pointcut defines a list of parameters that may be passed to a Hook Method for a given Pointcut. For example, the definition of the SERVER_INCOMING_REQUEST_PRE_HANDLED pointcut used in the example above defines 4 parameter types. A hook method for that parameter type can include any/all of these parameter types in its parameter list. The example above is only using one.

### Server Interceptors

- Server
- Resource Provider

### Pointcuts 

#### Plain Server Pointcuts
![](https://hapifhir.io/hapi-fhir/docs/interceptors/server_pointcuts/interceptors-server-pipeline.svg)

#### Storage / JPA Server Pointcuts
![Storage / JPA Server Pointcuts](https://hapifhir.io/hapi-fhir/docs/interceptors/server_pointcuts/interceptors-server-jpa-pipeline.svg)

## Performance and Caching
![Query Cache](https://smilecdr.com/docs/images/fhir_repository-query_cache.svg)


