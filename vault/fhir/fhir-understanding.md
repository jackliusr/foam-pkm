- UseFhirController: false
- ComputeHash: SHA256, utf-8, replace "-" in the end result string
- FhirEngineOptions:  during the configiration, it might wire all the components together
- not used in v4.1.0. current implement in v4.0.0
- MergeResourcesHandler: 
- AddRepositoryHandlers:
- FhirResourceCachingHandler:
- FhirHandlerAttribute(string handlerName, HandlerCategory category, FhirInteractionType interaction, int sort)
- command to find methods which are used by ConfigurationProcessor.DependencyInjection for configurationSection
  ```bash
  rg -g '*.cs' "\(this IFhirEngineBuilder"
  ```
- SearchOptionsFactory:  create search options
  ```mermaid
  classDiagram
      note "SearchOptions, created from SearchOptionsFactory"
      SearchOptions : +int age
      SearchOptions : +String gender
      SearchOptions: +isMammal()
      SearchOptions: +mate()
  ```
- <>
  - FhirDataStore<PopHealthPatient>: 
    ```c#
    //TContext will be internal existing type PopHealthPatient 
    public static IFhirEngineBuilder AddFhirDataStoreHandler<TContext>(this IFhirEngineBuilder builder, Action<RepositoryHandlerSettings> configureOptions)
    ```
  - FhirDataStore<@PopHealthPatient@PopHealthPatientParent>: 
    ```c#
    //TContext will be runtime-generated class PopHealthPatient which inherit PopHealthPatientParent 
    public static IFhirEngineBuilder AddFhirDataStoreHandler<TContext>(this IFhirEngineBuilder builder, Action<RepositoryHandlerSettings> configureOptions)
    ```
  - FhirDataStore<@PopHealthPatient> and FhirDataStore<!PopHealthPatient>: 
    ```c#
    //TContext will be runtime-generated class PopHealthPatient which inherient 
    public static IFhirEngineBuilder AddFhirDataStoreHandler<TContext>(this IFhirEngineBuilder builder, Action<RepositoryHandlerSettings> configureOptions)
    ```
- additional service path in FhirEngine section: ["Handlers", "SystemPlugins", "Handlers:RepositoryHandlers:DbRepository"]
- ResourceRepositoryCacheService: new version of cache implementation, ResourceRepositoryCacheService, IRepositoryService<TResource>
- DetermineInteractionType
- localdb: only in windows; for linux or wsl, use sqlserver or sql server express
- ReferenceCheckOptions.CacheDurationInMinutes: 2 mins
- how repository add?
  - AddFhirDataStoreHandler
- AddRepositoryServiceAndHandlers: 
  - TRepositoryService, IRepositoryService, 
  - RepositoryOptions
- FhirHandlerActivator: based on FHIREngine configration options and  fhirSpecificationAccessor
  - GetHandlers
  - GetSearchService
  - GetDataService
- SqlCommandWrapper: IDisposable, IAsyncDisposable; add more control when to dispose sqlcommnad
- ResourceWrapper, ResourceElement, RawResource, ResourceRequest
  - ResourceWrapper: element, rawResource, request, searchIndexEntries, compartmentIndices, searchParamerHash
  - ResourceElement: Id,VersionId,InstanceType, LastUpdated
  - ResourceRequest: http method, url
  - RawResource: data, Format(json,xml, unknown), 
    - isMetaSet: Specifies whether the meta section in the serialized resource in Data is set correctly.We expect that for a RawResource resulting from an update, the version needs to be updated, so isMetaSet would be false. While on a RawResource resulting from a create, the version should be correct and isMetaSet would be true.
- ReflectionUtil
  - CompileGenericMethodWithReplacement
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
- [ProcessConfiguration](https://github.com/almostchristian/ConfigurationProcessor.DependencyInjection/blob/dd470bd7a77168e7b7e3f2383ec041281816b41a/src/ConfigurationProcessor.Core/ConfigurationExtensions.cs#L32)
  -    public static TContext ProcessConfiguration<TContext>(
       this IConfiguration configuration,
       TContext context, //IFhirEngineBuilder
       string configSection,
       string[]? contextPaths = null,
       MethodFilterFactory? methodFilterFactory = null,
       MethodInfo[]? additionalMethods = null)
       where TContext : class
      => configuration.ProcessConfiguration(
          context,
          options =>
          {
             options.ConfigSection = configSection;
             options.MethodFilterFactory = methodFilterFactory;
             options.AdditionalMethods = additionalMethods ?? Enumerable.Empty<MethodInfo>();
             options.ContextPaths = contextPaths;
          });
  ```c#
  //string[] candidateMethodNameSuffixes = ["Handler", "Handlers"]
  //VersionedMethodFilter: specific versioned methods: R4, R
  // [Github](https://github.com/almostchristian/ConfigurationProcessor.DependencyInjection/blob/dd470bd7a77168e7b7e3f2383ec041281816b41a/src/ConfigurationProcessor.Core/ConfigurationExtensions.cs#L32)
  ProcessConfiguration(
                this IConfiguration configuration,
                builder, //IFhirEngineBuilder, TContext context,
                sectionName, //FhirEngine
                additionalServicePaths.Where(x => x != null).ToArray()!, //{ "Handlers", "SystemPlugins", "Handlers:RepositoryHandlers:DbRepository" }
                MethodFilterFactories.WithSuffixes(VersionedMethodFilter, candidateMethodNameSuffixes),
                SurrogateConfigurationMethods.AddHandlers); //all methods started with Add in SurrogateConfigurationMethods
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
  - 
- key functions:
  - IFhirEngineBuilder AddFhirEngineServer(this WebApplicationBuilder, string configSection = "FhirEngine" )
    - IFhirEngineBuilder AddFhirEngineServer(this WebApplicationBuilder builder, Assembly callingAssembly, string configSection)
      - IFhirEngineBuilder AddFhirEngineServer(this IServiceCollection services, 
         Assembly callingAssembly, IConfiguration configuration, string configSection)
        - IFhirEngineBuilder AddFhirEngineServer(this IServiceCollection services, IConfiguration configuration, Action<IFhirEngineBuilder> configureServices, string configSection = DefaultConfigSection) 
          - AddFhirEngineServerInternal
              - ForAssemblySpecification: Configure<FhirEngineOptions>
              - AddDistributedMemoryCache & DefaultTaggedDistributedCache
              - DistributedCacheFhirResourceCache
          - AddFhirEngineEndpoints
  - IEndpointRouteBuilder MapFhirEngine(this IEndpointRouteBuilder endpoints)
    - IEndpointRouteBuilder MapFhirEngine(this IEndpointRouteBuilder endpoints, string? rootPath)
    - MapGet/Post/Methods
      - TResult Process<TResult>(ObjectFactory factory, HttpContext context, ActivitySource activitySource)
        - ** the returned FhirGetResult, or FhirResult, their BindAsync and ExecuteAsync will be called by asp.net core pipeline **
        - factory: 
          - ActivatorUtilities.CreateFactory(typeof(FhirGetResult), System.Type.EmptyTypes);
          - ActivatorUtilities.CreateFactory(typeof(FhirResult), System.Type.EmptyTypes);
        - factory(context.RequestServices, null)//factor is delegate ObjectFactory(IServiceProvider serviceProvider, object?[]? arguments); context.RequestServices: IServiceProvider
          - FhirResult.ExecuteAsync
          - FhirHandlerProcessor.ProcessAsync
```diff
diff --git a/sample/TestFhirEngine/wwwroot/index.html b/sample/TestFhirEngine/wwwroot/index.html
index 32a8380055..e1919e39f4 100644
--- a/sample/TestFhirEngine/wwwroot/index.html
+++ b/sample/TestFhirEngine/wwwroot/index.html
@@ -11,9 +11,10 @@
 </head>
 <body>
     <rapi-doc spec-url="/swagger/v1/swagger.json"
-              server-url="https://pophealth.healthdxp.com"
+              server-url="https://localhost:5001"
               show-header="false"
               theme="dark"
+             api-headers='{"X-Ihis-SourceApplication": "CCDP"}'
               schema-style="table"
               render-style="focused"
               show-method-in-nav-bar="as-colored-block">
```
