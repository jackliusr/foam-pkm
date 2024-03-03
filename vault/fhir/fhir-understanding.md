- UseFhirController: false
- builder.AddFhirEngineServer(DefaultConfigSection section): extension method
        AddFhirEngineServer(Assembly, section)
        R5: IFhirEngineBuilder AddFhirEngineServer(this IServiceCollection services, Assembly callingAssembly, IConfiguration configuration, string configSection)
            AddFhirEngineServer(this IServiceCollection services, IConfiguration configuration, Action<IFhirEngineBuilder> configureServices, string configSection = DefaultConfigSection)
                 AddFhirEngineServerInternal
                    AddFhirEngineServerCore
                 AddFhirEngineEndpoints
- MapFhirEngine
  ```c#
        endpoints.MapPost(ApplyRootPath(KnownRoutes.ResourceType), ProcessWithBodyType)
            .WithName(RouteNames.CreateResource)
            .AcceptsFhirResource()
            .ProducesFhirResponse(StatusCodes.Status201Created);
    ```
 >	Synapxe.FhirWebApi1.dll!Synapxe.FhirWebApi1.Handlers.AppointmentDataFhirHandler.ValidateNoAppointmentConflictAsync(Hl7.Fhir.Model.Appointment appointment, System.Threading.CancellationToken cancellationToken) Line 65	C#
 	Ihis.FhirEngine.Core.dll!Ihis.FhirEngine.Core.Handlers.Builder.DelegateFhirHandler<System.__Canon, System.__Canon>.TryExecuteDelegate.AnonymousMethod__1(System.__Canon i, Ihis.FhirEngine.Core.IFhirContext c, System.Threading.CancellationToken ct)	Unknown
 	Ihis.FhirEngine.Core.dll!Ihis.FhirEngine.Core.Handlers.Builder.BaseDelegateFhirHandler<System.__Canon, System.__Canon>.ProcessAsync.AnonymousMethod__0(Ihis.FhirEngine.Core.IFhirContext ctx, System.Threading.CancellationToken ct)	Unknown
 	Ihis.FhirEngine.Core.dll!Ihis.FhirEngine.Core.Handlers.NullFhirHandlerInvoker.InvokeAsync(System.Func<Ihis.FhirEngine.Core.IFhirContext, System.Threading.CancellationToken, System.Threading.Tasks.Task> asyncAction, Ihis.FhirEngine.Core.IFhirHandlerMetadata handlerMetadata, Ihis.FhirEngine.Core.IFhirContext context, int? index, System.Threading.CancellationToken cancellationToken)	Unknown
 	Ihis.FhirEngine.Core.dll!Ihis.FhirEngine.Core.Handlers.Builder.BaseDelegateFhirHandler<Synapxe.FhirWebApi1.Handlers.AppointmentDataFhirHandler, System.Func<Synapxe.FhirWebApi1.Handlers.AppointmentDataFhirHandler, Hl7.Fhir.Model.Appointment, System.Threading.CancellationToken, System.Threading.Tasks.Task>>.ProcessAsync(Ihis.FhirEngine.Core.IFhirContext context, System.Threading.CancellationToken cancellationToken)	Unknown
 	Ihis.FhirEngine.Core.dll!Ihis.FhirEngine.Core.Handlers.FhirHandlerExceptionWrapper.ProcessAsync(Ihis.FhirEngine.Core.IFhirContext context, System.Threading.CancellationToken cancellationToken)	Unknown
 	Ihis.FhirEngine.Core.dll!Ihis.FhirEngine.Core.Handlers.FhirHandlerProcessor.ProcessAsync(Ihis.FhirEngine.Core.IFhirContext context, System.Threading.CancellationToken cancellationToken)	Unknown
 	Ihis.FhirEngine.WebApi.Core.dll!Ihis.FhirEngine.WebApi.Core.Endpoint.FhirBaseResult.ExecuteAsync(Microsoft.AspNetCore.Http.HttpContext httpContext)	Unknown
 	Microsoft.AspNetCore.Http.Extensions.dll!Microsoft.AspNetCore.Http.RequestDelegateFactory.ExecuteResultWriteResponse(Microsoft.AspNetCore.Http.IResult result, Microsoft.AspNetCore.Http.HttpContext httpContext)	Unknown
