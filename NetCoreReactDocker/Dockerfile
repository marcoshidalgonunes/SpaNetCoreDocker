#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS base
WORKDIR /app
EXPOSE 80
RUN apt-get update
RUN apt-get install -y nodejs
RUN apt-get install -y npm

FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /clientapp
COPY ["package.json", "package-lock.json*", "./"]
RUN npm install
COPY . .
WORKDIR /src
COPY ["NetCoreReactDocker.csproj", "."]
RUN dotnet restore "./NetCoreReactDocker.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "NetCoreReactDocker.csproj" -c Release -o /app/build

FROM build AS publish
WORKDIR /clientapp
RUN npm run build
COPY . .
WORKDIR "/src/."
RUN dotnet publish "NetCoreReactDocker.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "NetCoreReactDocker.dll"]