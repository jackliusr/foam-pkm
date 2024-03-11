- font: https://github.com/microsoft/cascadia-code
- sticky scroll
- Local source of nuget packages doesn't exist: 
  -  changes
    ```xml
    <!-- add into packageSources tag  --/>
    <add key="repositoryPath" value="C:\DIM\.nuget" />
    ```
    ```batch
    REM In environment variables edit system variable NUGET_PACKAGES and set new destination
    SET NUGET_PACKAGES=C:\DIM\.nuget\packages
    nuget config -set repositoryPath=C:\DIM\.nuget\packages

    REM C:\Users\JackLiuShurui(Synapx\.nuget\packages\grpc.tools\2.55.1\build\_protobuf\Google.Protobuf.Tools.targets
    ```
  - refererence
    -  [Config file locations and uses](https://learn.microsoft.com/en-us/nuget/consume-packages/configuring-nuget-behavior#config-file-locations-and-uses):
    -  [config section](https://learn.microsoft.com/en-us/nuget/reference/nuget-config-file#config-section)
      - First the NuGetDefaults.Config file. You will find this in %ProgramFiles(x86)%\NuGet\Config.
      - The computer-level file.
      - The user-level file. You will find this in %APPDATA%\NuGet\nuget.config.
      - Any file named nuget.config beginning from the root of your drive up to the directory where nuget.exe is called.
      - The config file you specify in the -configfile option when calling nuget.exe

  - Unexpected error “Your project file doesn’t list ‘win’ as a “RuntimeIdentifier”” occurs when building the solution:
    - This could happen in case you upgrade a project to new SDK Project style, but then later you checkout a old commit with old project type and then it is required to delete obj/bin folder
      - clean project/solution
  - Severity	Code	Description	Project	File	Line	Suppression State
Error	MSB6003	The specified task executable "C:\Users\JackLiuShurui(Synapx\.nuget\packages\grpc.tools\2.55.1\tools\windows_x64\protoc.exe" could not be run. This program is blocked by group policy. For more information, contact your system administrator	SampleService.Grpc	C:\Users\JackLiuShurui(Synapx\.nuget\packages\grpc.tools\2.55.1\build\_protobuf\Google.Protobuf.Tools.targets	292	

  - [Microsoft.NuGet.targets(198,5): Error:  : Your project does not reference ".NETFramework,Version=v4.7.2" framework. Add a reference to ".NETFramework,Version=v4.7.2" in the "TargetFrameworks" property of your project file and then re-run NuGet restore.](https://stackoverflow.com/questions/52833741/your-project-does-not-reference-netframework-version-v4-6-2-framework-add-a)
    - [delete bin and obj folders](https://stackoverflow.com/a/53322616)
