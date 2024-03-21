- https://github.com/almostchristian/ConfigurationProcessor.DependencyInjection/tree/master
- ConfigurationProcessor.DependencyInjection usage:  [[ConfigurationProcessor.DependencyInjection-readme.md]]
- The library works by using reflection and scanning all currently loaded assemblies for extension methods for IServiceCollection
- https://github.com/serilog/serilog-settings-configuration/
  - hot-reload: [Serilog.Settings.Configuration only supports reloading a small number of settings at runtime (minimum level being the one I know of). I don't think there's any support for reloading sink arguments like these](https://github.com/serilog/serilog-settings-configuration/issues/346#issuecomment-1407259144)
- ConfigurationProcessor.Core.Implementation.CommonExtensions
  ```c#
  public static (string TypeName, TypeResolver[] Resolvers) ReadTypeName(
                this ResolutionContext resolutionContext,
                string name,
                IConfiguration ambientConfiguration)

  private static TypeResolver ReadGenericType(
    this ResolutionContext resolutionContext, 
    string typeName, 
    IConfiguration rootConfiguration, 
    IConfiguration ambientConfiguration)

  /// <summary>
  /// 
  /// </summary>
  public TypeResolver CreateTypeResolver(string typeName, 
      IConfiguration rootConfiguration, 
      IConfiguration ambientConfiguration)    
  ```
- ConfigLookup 
  ```c#
   System.ValueTuple<
            ConfigurationProcessor.Core.Implementation.TypeResolver[], 
            Microsoft.Extensions.Configuration.IConfigurationSection, 
            System.Collections.Generic.Dictionary<string, 
                (ConfigurationProcessor.Core.Implementation.IConfigurationArgumentValue ArgName, 
                  Microsoft.Extensions.Configuration.IConfigurationSection ConfigSection)
              >
          >;
  ###
- [ProcessConfiguration](https://github.com/almostchristian/ConfigurationProcessor.DependencyInjection/blob/dd470bd7a77168e7b7e3f2383ec041281816b41a/src/ConfigurationProcessor.Core/ConfigurationExtensions.cs#L32C30-L32C50)
  - ProcessConfiguration
    ```c#
    public static TContext ProcessConfiguration<TContext>(
            this IConfiguration configuration,
            TContext context, //IFhirEngineBuilder
            string configSection,
            string[]? contextPaths = null, //["Handlers", "SystemPlugins", "Handlers:RepositoryHandlers:DbRepository"]
            MethodFilterFactory? methodFilterFactory = null,
            MethodInfo[]? additionalMethods = null)
            where TContext : class
          => configuration.ProcessConfiguration(
              context,
              options =>
              {
                  options.ConfigSection = configSection; //FhirEngine
                  options.MethodFilterFactory = methodFilterFactory; 
                  options.AdditionalMethods = additionalMethods ?? Enumerable.Empty<MethodInfo>();
                  options.ContextPaths = contextPaths; // ["Handlers", "SystemPlugins", "Handlers:RepositoryHandlers:DbRepository"]
              });
    ```
  - [configuration.ProcessConfiguration](https://github.com/almostchristian/ConfigurationProcessor.DependencyInjection/blob/dd470bd7a77168e7b7e3f2383ec041281816b41a/src/ConfigurationProcessor.Core/ConfigurationExtensions.cs#L59)
    ```c#
      public static TContext ProcessConfiguration<TContext>(
            this IConfiguration configuration,
            TContext context, //IFhirEngineBuilder
            Action<ConfigurationReaderOptions> configureOptions)
            where TContext : class
          {
            if (configuration == null)
            {
                throw new ArgumentNullException(nameof(configuration));
            }

            return context.AddFromConfiguration(
                configuration,
                AssemblyFinder.Auto(),
                configureOptions);
          }
    ```
  - [AddFromConfiguration](https://github.com/almostchristian/ConfigurationProcessor.DependencyInjection/blob/dd470bd7a77168e7b7e3f2383ec041281816b41a/src/ConfigurationProcessor.Core/ConfigurationExtensions.cs#L76)
    ```c#
    internal static TConfig AddFromConfiguration<TConfig>(
      this TConfig builder,  //IFhirEngineBuilder
      IConfiguration rootConfiguration,
      AssemblyFinder assemblyFinder,
      Action<ConfigurationReaderOptions> configureOptions)
      where TConfig : class
    {
      var options = new ConfigurationReaderOptions();
      configureOptions?.Invoke(options);
      var configurationSection = rootConfiguration.GetSection(options.ConfigSection);

      var reader = new ConfigurationReader<TConfig>(rootConfiguration, configurationSection, assemblyFinder, options);

      foreach (var servicePath in options.ContextPaths ?? new string[] { string.Empty })
      {
          if (string.IsNullOrEmpty(servicePath))
          {
            reader.AddServices(builder, null, true, options);
          }
          else if (servicePath![0] == '^')
          {
            reader.AddServices(builder, servicePath.Substring(1), false, options);
          }
          else
          {
            reader.AddServices(builder, servicePath, true, options);
          }
      }

      return builder;
    }
    ```
  - ConfigurationReader.AddServices(TConfig builder, string? sectionName, bool getChildren, ConfigurationReaderOptions options)
    ```C#
    //TConfig IFhirEngineBuilder
     public void AddServices(TConfig builder, string? sectionName, bool getChildren, ConfigurationReaderOptions options)
      {
         var builderDirective = string.IsNullOrEmpty(sectionName) ? ConfigurationSection : ConfigurationSection.GetSection(sectionName);
         if (!getChildren || builderDirective.GetChildren().Any())
         {
            ResolutionContext.CallConfigurationMethods(
               typeof(TConfig),
               builderDirective,
               getChildren,
               null,
               options.MethodFilterFactory,
               (arguments, methodInfo) => methodInfo.InvokeWithArguments(builder, arguments));
         }
      }
    ```
  - CallConfigurationMethods
    ```c#
      public static void CallConfigurationMethods(
        this ResolutionContext resolutionContext,
        Type extensionArgumentType, //IFhirEngineBuilder
        IConfigurationSection directive,
        bool getChildren,
        IEnumerable<string>? exclude,
        MethodFilterFactory? methodFilterFactory,
        Action<List<object?>, MethodInfo> invoker)
      {
          var methods = resolutionContext.GetMethodCalls(directive, getChildren, exclude);
          foreach (var (methodName, (typeArgs, configSection, configArgs)) in methods.SelectMany(g => g.Select(x => (MethodName: g.Key, Config: x))))
          {
              var paramArgs = configArgs;
              CallConfigurationMethod(
                  resolutionContext,
                  extensionArgumentType,
                  methodName,
                  configSection,
                  methodFilterFactory,
                  typeArgs,
                  paramArgs,
                  null,
                  invoker);
          }
        }
    ```
  - **CallConfigurationMethod**
    ```c#
    public static void CallConfigurationMethod(
           this ResolutionContext resolutionContext,
           Type extensionArgumentType,
           string methodName,
           IConfigurationSection configSection,
           MethodFilterFactory? methodFilterFactory,
           TypeResolver[] typeArgs,
           Dictionary<string, (IConfigurationArgumentValue ArgName, IConfigurationSection ConfigSection)>? paramArgs,
           Func<List<object?>>? argumentFactory,
           Action<List<object?>, MethodInfo> invoker)
    ```
  - ResolutionContext
    ```c#
      ResolutionContext(
          AssemblyFinder assemblyFinder,
          IConfiguration rootConfiguration,
          IConfigurationSection appConfiguration,
          IEnumerable<MethodInfo> additionalMethods,
          Action<ExtensionMethodNotFoundEventArgs> onExtensionMethodNotFound,
          Action<string>? onExtensionMethodFound,
          params Type[] markerTypes
      )

      internal static ILookup<string, ConfigLookup> GetMethodCalls(
         this ResolutionContext resolutionContext,
         IConfigurationSection directive,
         bool getChildren = true,
         IEnumerable<string>? exclude = null)
    ```



[//begin]: # "Autogenerated link references for markdown compatibility"
[ConfigurationProcessor.DependencyInjection-readme.md]: ConfigurationProcessor.DependencyInjection-readme.md "ConfigurationProcessor.DependencyInjection"
[//end]: # "Autogenerated link references"


[//begin]: # "Autogenerated link references for markdown compatibility"
[ConfigurationProcessor.DependencyInjection-readme.md]: ConfigurationProcessor.DependencyInjection-readme.md "ConfigurationProcessor.DependencyInjection"
[//end]: # "Autogenerated link references"
