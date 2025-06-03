# See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
WORKDIR /src

# Copy csproj and restore as distinct layers
COPY ["ProjectTemplateDotnet.Api.csproj", "ProjectTemplateDotnet.Api.csproj"]
RUN dotnet restore "ProjectTemplateDotnet.Api.csproj"

# Copy everything else and build app
COPY . .
WORKDIR "/src/."
RUN dotnet build "ProjectTemplateDotnet.Api.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "ProjectTemplateDotnet.Api.csproj" -c Release -o /app/publish

# Build runtime image
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "ProjectTemplateDotnet.Api.dll"]