# e4k-dockers
Docker files to ease building Embeddinator-4000 on Windows

## To build the images, clone this repo then run:

```
cd xamarin-vs19-windows-docker
docker build -t dbo/xamarin-vs19:latest -m 4GB .

cd ../e4k-windows-docker
docker build -t dbo/e4k-vs19:latest -m 4GB .
```

## To execute the Embeddinator-4000 on a given C# DLL and generate Java glue code:

1. Create a working directory (e.g. "C:\my_working_dir\") containing your library (Example.dll in the following example).

2. Execute the following docker command:

```
docker run -v C:\my_working_dir\:C:\docker_working_dir\ -it dbo/e4k-vs19:latest -v -gen Java -p Windows -c -t shared -o C:/docker_working_dir/output C:/docker_working_dir/Example.dll
```
