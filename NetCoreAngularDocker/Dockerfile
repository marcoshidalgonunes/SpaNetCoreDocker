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
COPY ["NetCoreAngularDocker.csproj", "."]
RUN dotnet restore "./NetCoreAngularDocker.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "NetCoreAngularDocker.csproj" -c Release -o /app/build

FROM build AS publish
WORKDIR /clientapp
RUN npm run build -- --prod
RUN npm run build:ssr -- --prod
RUN npm start
COPY . .
WORKDIR "/src/."
RUN dotnet publish "NetCoreAngularDocker.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "NetCoreAngularDocker.dll"]