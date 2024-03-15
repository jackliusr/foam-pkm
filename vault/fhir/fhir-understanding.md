- UseFhirController: false
- ComputeHash: SHA256, utf-8, replace "-" in the end result string
- FhirEngineOptions:  during the configiration, it might wire all the components together
- not used in v4.1.0. current implement in v4.0.0
- MergeResourcesHandler: 
- AddRepositoryHandlers:
- FhirResourceCachingHandler:
- SearchOptionsFactory:  create search options
  ```mermaid
  classDiagram
      note "SearchOptions, created from SearchOptionsFactory"
      SearchOptions : +int age
      SearchOptions : +String gender
      SearchOptions: +isMammal()
      SearchOptions: +mate()
  ```
- ResourceRepositoryCacheService: new version of cache implementation, ResourceRepositoryCacheService, IRepositoryService<TResource>
- DetermineInteractionType
- localdb: only in windows; for linux or wsl, use sqlserver or sql server express
- ReferenceCheckOptions.CacheDurationInMinutes: 2 mins
- Verifer
  ```C#
  FhirPathFilterConditiong
  ClaimsFilterConditiong
  PathQueryFilterConditiong
  RolesFilterConditiong
  HeadersFilterConditiong
  NullFilterConditiong
  ContextItemKeyExistsFilterConditiong

  CachedFhirHandlerConditionVerifiers
  ConditionalFhirHandlerFilter
  ```
- builder.AddFhirEngineServer(DefaultConfigSection section): extension method
        AddFhirEngineServer(Assembly, section)
        R5: IFhirEngineBuilder AddFhirEngineServer(this IServiceCollection services, Assembly callingAssembly, IConfiguration configuration, string configSection)
            AddFhirEngineServer(this IServiceCollection services, IConfiguration configuration, Action<IFhirEngineBuilder> configureServices, string configSection = DefaultConfigSection)
                 AddFhirEngineServerInternal
                    AddFhirEngineServerCore
            AddFhirEngineEndpoints
            AddFromConfiguration(configSection, new[] { "Handlers", "SystemPlugins", "Handlers:RepositoryHandlers:DbRepository" })
              IFhirEngineBuilder AddFromConfiguration(this IFhirEngineBuilder builder, string sectionName, string[] additionalServicePaths)
                AddFromConfiguration(this IFhirEngineBuilder builder, string sectionName, string[] additionalServicePaths, string[] candidateMethodNameSuffixes)
                  **ProcessConfiguration**
- ProcessConfiguration: 
  ```c#
  //string[] candidateMethodNameSuffixes = ["Handler", "Handlers"]
  //VersionedMethodFilter: specific versioned methods: R4, R
  ProcessConfiguration(
                this IConfiguration configuration,
                builder, //IFhirEngineBuilder, TContext context,
                sectionName, //FhirEngine
                additionalServicePaths.Where(x => x != null).ToArray()!, //{ "Handlers", "SystemPlugins", "Handlers:RepositoryHandlers:DbRepository" }
                MethodFilterFactories.WithSuffixes(VersionedMethodFilter, candidateMethodNameSuffixes),
                SurrogateConfigurationMethods.AddHandlers); //all methods started with Add
  AddFromConfiguration(IConfiguration, AssemblyFinder,Action<ConfigurationReaderOptions> configureOptions)
  internal static TConfig ConfigurationExtensions.AddFromConfiguration<TConfig>(
          this TConfig builder,
          IConfiguration rootConfiguration,
          AssemblyFinder assemblyFinder,
          Action<ConfigurationReaderOptions> configureOptions)
          where TConfig : class
  ConfigurationReader<TConfig>
    ResolutionContext.CallConfigurationMethods(
        typeof(TConfig),
        builderDirective,
        getChildren,
        null,
        options.MethodFilterFactory,
        (arguments, methodInfo) => methodInfo.InvokeWithArguments(builder, arguments));
  ResolutionContext(
          AssemblyFinder assemblyFinder,
          IConfiguration rootConfiguration,
          IConfigurationSection appConfiguration,
          IEnumerable<MethodInfo> additionalMethods,
          Action<ExtensionMethodNotFoundEventArgs> onExtensionMethodNotFound,
          Action<string>? onExtensionMethodFound,
          params Type[] markerTypes)
          CallConfigurationMethods
          CallConfigurationMethod
  FhirEngineBuilderRegistrationExtensions         
  ```                  
- MapFhirEngine
  ```c#
        endpoints.MapPost(ApplyRootPath(KnownRoutes.ResourceType), ProcessWithBodyType)
            .WithName(RouteNames.CreateResource)
            .AcceptsFhirResource()
            .ProducesFhirResponse(StatusCodes.Status201Created);
    ```
- SearchQueryExtensions
- SearchExtensions
- Callstack of handlers
  ```
  Synapxe.FhirWebApi1.dll!Synapxe.FhirWebApi1.Handlers.AppointmentDataFhirHandler.ValidateNoAppointmentConflictAsync(Hl7.Fhir.Model.Appointment appointment, System.Threading.CancellationToken cancellationToken) Line 65	C#
      Ihis.FhirEngine.Core.dll!Ihis.FhirEngine.Core.Handlers.Builder.DelegateFhirHandler<System.__Canon, System.__Canon>.TryExecuteDelegate.AnonymousMethod__1(System.__Canon i, Ihis.FhirEngine.Core.IFhirContext c, System.Threading.CancellationToken ct)	Unknown
      Ihis.FhirEngine.Core.dll!Ihis.FhirEngine.Core.Handlers.Builder.BaseDelegateFhirHandler<System.__Canon, System.__Canon>.ProcessAsync.AnonymousMethod__0(Ihis.FhirEngine.Core.IFhirContext ctx, System.Threading.CancellationToken ct)	Unknown
      Ihis.FhirEngine.Core.dll!Ihis.FhirEngine.Core.Handlers.NullFhirHandlerInvoker.InvokeAsync(System.Func<Ihis.FhirEngine.Core.IFhirContext, System.Threading.CancellationToken, System.Threading.Tasks.Task> asyncAction, Ihis.FhirEngine.Core.IFhirHandlerMetadata handlerMetadata, Ihis.FhirEngine.Core.IFhirContext context, int? index, System.Threading.CancellationToken cancellationToken)	Unknown
      Ihis.FhirEngine.Core.dll!Ihis.FhirEngine.Core.Handlers.Builder.BaseDelegateFhirHandler<Synapxe.FhirWebApi1.Handlers.AppointmentDataFhirHandler, System.Func<Synapxe.FhirWebApi1.Handlers.AppointmentDataFhirHandler, Hl7.Fhir.Model.Appointment, System.Threading.CancellationToken, System.Threading.Tasks.Task>>.ProcessAsync(Ihis.FhirEngine.Core.IFhirContext context, System.Threading.CancellationToken cancellationToken)	Unknown
      Ihis.FhirEngine.Core.dll!Ihis.FhirEngine.Core.Handlers.FhirHandlerExceptionWrapper.ProcessAsync(Ihis.FhirEngine.Core.IFhirContext context, System.Threading.CancellationToken cancellationToken)	Unknown
      Ihis.FhirEngine.Core.dll!Ihis.FhirEngine.Core.Handlers.FhirHandlerProcessor.ProcessAsync(Ihis.FhirEngine.Core.IFhirContext context, System.Threading.CancellationToken cancellationToken)	Unknown
      Ihis.FhirEngine.WebApi.Core.dll!Ihis.FhirEngine.WebApi.Core.Endpoint.FhirBaseResult.ExecuteAsync(Microsoft.AspNetCore.Http.HttpContext httpContext)	Unknown
      Microsoft.AspNetCore.Http.Extensions.dll!Microsoft.AspNetCore.Http.RequestDelegateFactory.ExecuteResultWriteResponse(Microsoft.AspNetCore.Http.IResult result, Microsoft.AspNetCore.Http.HttpContext httpContext)	Unknown
  ```
- Cache tag of resource: "{resourceType}/{id}"
-  DefaultConfigSection: FhirEngine
- configuration main class: 
  - SurrogateConfigurationMethods: 
- key functions:
  - IFhirEngineBuilder AddFhirEngineServer(this WebApplicationBuilder, string configSection = "FhirEngine" )
    - IFhirEngineBuilder AddFhirEngineServer(this WebApplicationBuilder builder, Assembly callingAssembly, string configSection)
      - IFhirEngineBuilder AddFhirEngineServer(this IServiceCollection services, 
         Assembly callingAssembly, IConfiguration configuration, string configSection)
        - IFhirEngineBuilder AddFhirEngineServer(this IServiceCollection services, IConfiguration configuration, Action<IFhirEngineBuilder> configureServices, string configSection = DefaultConfigSection) 
          - AddFhirEngineServerInternal
              - AddDistributedMemoryCache & DefaultTaggedDistributedCache
              - DistributedCacheFhirResourceCache
          - AddFhirEngineEndpoints
  - IEndpointRouteBuilder MapFhirEngine(this IEndpointRouteBuilder endpoints)
    - IEndpointRouteBuilder MapFhirEngine(this IEndpointRouteBuilder endpoints, string? rootPath)
    - MapGet/Post/Methods
      - TResult Process<TResult>(ObjectFactory factory, HttpContext context, ActivitySource activitySource)
        - factory: 
          - ActivatorUtilities.CreateFactory(typeof(FhirGetResult), System.Type.EmptyTypes);
          - ActivatorUtilities.CreateFactory(typeof(FhirResult), System.Type.EmptyTypes);
        - factory(context.RequestServices, null) 
          - FhirResult.ExecuteAsync
          - FhirHandlerProcessor.ProcessAsync
