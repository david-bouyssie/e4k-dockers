# escape=`

### See:
### - https://stackoverflow.com/questions/41278108/is-it-possible-to-install-visual-studio-in-a-windows-container
### - https://github.com/lukas-lansky/visual-studio-docker-build/blob/master/vs.Dockerfile
### - https://docs.microsoft.com/fr-fr/visualstudio/install/create-an-offline-installation-of-visual-studio?view=vs-2019
FROM mcr.microsoft.com/windows/servercore:ltsc2019
#FROM microsoft/dotnet-framework:4.7.2-sdk

### Download the Visual Studio bootstrapper
ADD https://aka.ms/vs/16/release/vs_community.exe "C:/Users/ContainerAdministrator/AppData/Local/Temp/vs_community.exe"

### Install Visual Studio with the desired components (NetCrossPlat -> Xamarin&OpenJDK, PortableLibrary)
### See: https://docs.microsoft.com/en-us/visualstudio/install/workload-component-id-vs-professional?view=vs-2019#mobile-development-with-net
RUN "%TEMP%\vs_community.exe" --add Microsoft.VisualStudio.Workload.NetCrossPlat `
    --add Microsoft.VisualStudio.Component.PortableLibrary `
    --add Microsoft.VisualStudio.Component.VC.Tools.x86.x64 `
    --add Microsoft.VisualStudio.Component.Windows10SDK.18362 `
    --quiet --wait --norestart --noUpdateInstaller

### Check Visual Studio installation errors
RUN powershell -Command "if ($err = dir $Env:TEMP -Filter dd_setup_*_errors.log | where Length -gt 0 | Get-Content) { throw $err }"

### Cleanup Visual Studio installation
RUN `
    rmdir /S /Q "%ProgramFiles(x86)%/Microsoft Visual Studio/Installer/" `
    && powershell Remove-Item -Force -Recurse "$Env:TEMP/*" `
    && rmdir /S /Q "%ProgramData%/Package Cache/"

### Set JAVA_HOME
RUN set JAVA_HOME=C:/Program Files/Android/jdk/microsoft_dist_openjdk_1.8.0.25/
RUN setx /M JAVA_HOME "C:/Program Files/Android/jdk/microsoft_dist_openjdk_1.8.0.25/"

### Add VS19 MSBuild to PATH
RUN set PATH=%PATH%;C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/MSBuild/Current/Bin/
RUN setx /M PATH "%PATH%;C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/MSBuild/Current/Bin/"

### Install .Net Framework Targeting Packs
RUN powershell " `
    $ErrorActionPreference = 'Stop'; `
    $ProgressPreference = 'SilentlyContinue'; `
    @('4.0', '4.5.2', '4.6.2', '4.7.2', '4.8') `
    | %{ `
        Invoke-WebRequest `
            -UseBasicParsing `
            -Uri https://dotnetbinaries.blob.core.windows.net/referenceassemblies/v${_}.zip `
            -OutFile referenceassemblies.zip; `
        Expand-Archive referenceassemblies.zip -Force -DestinationPath \"${Env:ProgramFiles(x86)}/Reference Assemblies/Microsoft/Framework/.NETFramework\"; `
        Remove-Item -Force referenceassemblies.zip; `
    }"

# Define the entry point for the docker container.
# This entry point starts the developer command prompt and launches the PowerShell shell.
ENTRYPOINT ["C:\\Program Files (x86)\\Microsoft Visual Studio\\2019\\Community\\Common7\\Tools\\VsDevCmd.bat", "&&", "powershell.exe", "-NoLogo", "-ExecutionPolicy", "Bypass"]

