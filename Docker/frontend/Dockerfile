FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /AnimalsMvc
COPY AnimalsMvc/*.csproj ./
RUN dotnet restore
COPY AnimalsMvc/. ./
RUN dotnet publish -c Release -o /app

FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY --from=build /app .
ENV ASPNETCORE_URLS="http://*:8080"
ENV ASPNETCORE_ENVIRONMENT=Production
EXPOSE 8080
ENTRYPOINT ["dotnet", "AnimalsMvc.dll"]