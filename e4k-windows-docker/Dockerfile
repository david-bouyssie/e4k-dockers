# escape=`

FROM dbo/xamarin-vs19:latest

### Download and install Mono x64
ADD https://download.mono-project.com/archive/6.10.0/windows-installer/mono-6.10.0.104-x64-0.msi mono-6.10.0.104-x64-0.msi
RUN cmd /S /C msiexec /i mono-6.10.0.104-x64-0.msi /quiet
RUN del mono-6.10.0.104-x64-0.msi

### Download and install MinGit
ADD https://github.com/git-for-windows/git/releases/download/v2.12.2.windows.2/MinGit-2.12.2.2-64-bit.zip MinGit.zip
RUN powershell Expand-Archive C:\MinGit.zip -DestinationPath c:\MinGit;
RUN del MinGit.zip

### Configure MinGit in the system
RUN set PATH=%PATH%;C:\MinGit\cmd\
RUN setx /M PATH "%PATH%;C:\\MinGit\\cmd\\"
RUN powershell Set-ItemProperty -Path 'HKLM:\\SYSTEM\\CurrentControlSet\\Control\\Session Manager\\Environment\\' -Name Path -Value $env:PATH

### Move to dev folder
RUN mkdir dev
WORKDIR C:/dev

### Download Embeddinator-4000 from git repo
RUN git clone https://github.com/david-bouyssie/Embeddinator-4000.git
WORKDIR C:/dev/Embeddinator-4000
RUN git submodule update --recursive --init
RUN git submodule update --remote .\\external\\CppSharp

### Download and execute nuget to retrieve packages
ADD https://dist.nuget.org/win-x86-commandline/v4.7.3/nuget.exe nuget.exe
RUN nuget.exe restore

### Build the solution
RUN "msbuild.exe Embeddinator-4000.sln /property:Configuration=Debug /p:Platform=x86" || EXIT 0

# Define the entry point for the docker container.
# This entry point starts the developer command prompt (configured for x64 build) and launches Embeddinator-4000.exe.
ENTRYPOINT [ `
    "C:\\Program Files (x86)\\Microsoft Visual Studio\\2019\\Community\\VC\\Auxiliary\\Build\\vcvars64.bat", "&&", `
    "C:\\dev\\Embeddinator-4000\\build\\lib\\Debug\\Embeddinator-4000.exe"  `
]

