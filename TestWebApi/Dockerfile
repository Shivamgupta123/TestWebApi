FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build-env
WORKDIR /app

# COPY *.csproj .
COPY TestWebApi/TestWebApi.csproj ./
RUN dotnet restore
# RUN dotnet restore TestWebApi.csproj

COPY . ./

# RUN dotnet publish --no-restore -c Release -o out
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.1
WORKDIR /app
COPY --from=build-env /app/out .
ENTRYPOINT ["dotnet", "TestWebApi.dll"]
