# Start from a base image with known vulnerabilities
FROM debian:jessie AS build-env
WORKDIR /app

# Install a package with known vulnerabilities
RUN apt-get update && apt-get install -y openssl libssl1.0.0

# Copy csproj and restore as distinct layers
COPY *.csproj ./
RUN dotnet restore

# Copy everything else and build
COPY . ./
RUN dotnet publish -c Release -o out

# Build runtime image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build-env /app/out .
ENTRYPOINT ["dotnet", "MyWebApp.dll"]
