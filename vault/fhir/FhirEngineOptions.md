- how to get fhirengine options
  - follow class definition of FhirEngineOptions
  - call path and .Configure<> methods:  .Configure<FhirEngineOptions>(!string.IsNullOrEmpty(configSection) ? configuration.GetSection(configSection) : configuration)
- DefaultHandlers: capability statement
  ```mermaid
  flowchart TD
    AddDefaultHandlers-->AddMetadataHandler
    AddMetadataHandler-->AddMergeHandler
  ```
- additional service path: ["Handlers", "SystemPlugins", "Handlers:RepositoryHandlers:DbRepository"]
- <@>: [text](https://github.com/almostchristian/ConfigurationProcessor.DependencyInjection/blob/dd470bd7a77168e7b7e3f2383ec041281816b41a/tests/ConfigurationProcessor.DependencyInjection.UnitTests/ConfigurationBuilderTestsBase.cs#L1301)
- ResourceCacheOptions
  ```mermaid
  classDiagram
      class ResourceCacheOptions{
          +bool AllowCacheDisable 
          +CacheCompressionMode CompressionMode
          +bool DisableCompression: //Obsolete, use CompressionMode
          +bool? Enabled
          +Action<IFhirEngineBuilder>? RegisterServices
          +TimeSpan? RelativeExpiration: //Obsolete,Use ResourceCacheDuration
          +Func<IFhirContext, TimeSpan> RelativeExpirationFactory
          +TimeSpan? ResourceCacheDuration
          +string[]? ResourceCacheTypes:  //Obsolete "Use ResourceTypes"
          +TaggingConfiguration Tagging
      }
      class TaggingConfiguration{
        Dictionary<string, string[]> IdentifierSearchParamAlias
        Dictionary<string, string[]> AppendSearchParam
      }
      class CacheCompressionMode{
          <<enumeration>>
          None = 0,
          GZipOptimal = 10,
          GZipFastest,
          GZip = GZipOptimal,
          DeflateOptimal = 20,
          DeflateFastest,
          Deflate = DeflateOptimal,
          BrotliOptimal = 30,
          BrotliFastest,
          Brotli = BrotliOptimal,
      }

      ResourceCacheOptions *-- TaggingConfiguration
      ResourceCacheOptions *-- CacheCompressionMode
  ```
- FhirEngineOptions
  - Handlers
    - RepositoryHandlers
      - ApiRepository:  [ "TestFhirEngine.Handlers.AppointmentDataFhirHandler", ...]
      - DbRepository
    - WorkflowHandlers: 
      - key classes: 
        - ResourceWorkflowSettings: Dictionary<string, Dictionary<string, List<IConfigurationSection>>>
        - HandlerOptionsProvider: convert workflow handler settings to internal model 
          ```c#
           IReadOnlyDictionary<(string Name, 
                string ResourceType, 
                HandlerCategory? Category, 
                FhirInteractionType? Interaction, 
                string? Operation), 
                IEnumerable<IConfigurationSection>> handlerConfigurations;
          ```
      - sample: C:\DIM\repos\FhirEngine\sample\TestFhirEngine\fhirengine.json
        ```json
        "Appointment": {
          "PreCRUD-Create":  //HnadlerCategory-InteractiveTYpe-Operations
           [
            {
              "Name": "ValidateAppointmentCreate"
            }
          ],
          "DDD":  //HnadlerCategory, or InteractiveTYpe, or Operations. for operations, start with $
          [
            {
              "Name": "ValidateAppointmentCreate"
            }
          ]
        ```
- IFhirHandlerClassSettings
  - string[]? AcceptedTypes
  - string? ActivityNameTemplate
  - FhirInteractionType[]? AllowedInteractions;  
    ```c#
    enum FhirInteractionType {
      NotDefined = 0,
      Create,
      Delete,
      HistorySystem,
      HistoryType,
      HistoryInstance,
      Patch,
      Read,
      SearchSystem,
      SearchType,
      SearchCompartment,
      Update,
      Vread,
      Metadata,
      OperationSystem,
      OperationType,
      OperationInstance,
      Batch,
      Transaction
      }
    ```
  - Action<IServiceCollection, RepositoryHandlerSettings>? ConfigureServices
  - string? ConnectionString
  - virtual Type? ContextType
  - string? CorrelationIdTagSystem
  - bool? DisableAudit
  - bool DisableHealthCheck; //Obsolete; use UnavailableHealthStatus instead
  - bool DisableRawRepository
  - string[]? ExcludeTypes
  - string? HandlerNameTemplate
  - Func<Type?, Type>? HandlerWrapperFactory
  - bool HardDelete
  - string? Id
  - bool? IncludeStandardSearchParams
  - bool MustCompleteTransaction
  - IRepositoryHandlerProvider? Provider
  - object? ProviderSettings
  - Type? ProviderType
  - bool SkipHandlerRegistration
  - bool? SupportsResourceChangeCapture
  - bool? SupportsTransaction
  - string[]? Tags
  - HealthStatus? UnavailableHealthStatus; //enum HealthStatus{Unhealthy, Degraded, Healthy}
  - bool ValidateAuditEventReferences
  - bool? Warmup
- FhirEngineOptions: based on class
  - AllowedVersions: ISet<FhirSpecification>
    - Unspecified: 0
    - R4
    - R5
  - CorrelationIdHeaderName: delegate to SystemPlugins.CorrelationId
  - DefaultVersion: FhirSpecification
  - DisableAutoTransaction: 
  - DisableHandlerAutoRegistration: boolean, default true,
  - DisableHandlerParameterCache: 
  - EnableHandlerAutoRegistration
  - EnableUnconfiguredHandlers: When set to true, all registered non-CRUD handlers are enabled even if it is unconfigured in the workflow handlers
  - InitializationRetryWaitDuration: 
  - OtherFormats: string[], constraint on formats? json, xml etc?
  - Path: path fhirengine will match to
  - PrincipalClaims: string[]
  - PublicConformanceResources: When set to true, the metadata endpoint does not require authorization
  - ShowExceptionDetails: When set to true, FhirEngine returns the stack trace for unhandled exceptions.
  - SearchConfiguration: delegate to SystemPlugins.SearchConfiguration
  - SystemPluginSettings
    - CorrelationId: string
    - HealthCheck: HealthCheckSettings
      - Enabled: boolean
      - Path: default "/health", optional
    - SearchConfiguration: FhirEngineSearchOptions
      - BlockFilterlessSearch: boolean
      - DefaultIncludeCountPerSearch: the default value for included search results. 100
      - DefaultItemCountPerSearch: default value 10
      - DefaultTotalParameter: string?, Gets or sets the default total parameter. none | accurate | estimate
      - MaxItemCountPerSearch: 100
      - PreferStrictSearchHandling: boolean, true;
      - UseTypedElementIndexer: boolean
  
  - UseFhirController: boolean; use FhirControllers?
  - UseLegacyAuditLogger: boolean; if use LegacyAuditLogger AuditLogger
  - UseLegacyHandlerRegistration: 
  - UseV1HandlerConditionVerifier



