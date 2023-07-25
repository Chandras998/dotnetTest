# Start from a base image with known vulnerabilities
FROM debian:jessie AS build-env
WORKDIR /app

# Update repository sources to use the Debian Archive
RUN sed -i '/jessie-updates/d' /etc/apt/sources.list
RUN sed -i '/security.debian.org/d' /etc/apt/sources.list
RUN sed -i 's/http:\/\/deb.debian.org/http:\/\/archive.debian.org/g' /etc/apt/sources.list
RUN echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/99no-check-valid-until

# Install a package with known vulnerabilities
RUN apt-get update && apt-get install -y --allow-unauthenticated openssl libssl1.0.0

# Install necessary components for .NET Core SDK
RUN apt-get install -y --allow-unauthenticated curl libunwind8 gettext apt-transport-https

# Download and install .NET Core SDK
RUN curl -sSL https://download.microsoft.com/download/3/A/3/3A80B3FF-9FAB-4B42-B6FD-099A58846C3B/dotnet-sdk-5.0.100-linux-x64.tar.gz | tar zxf - -C /usr/share/dotnet 

# Add dotnet to PATH
ENV PATH=$PATH:/usr/share/dotnet

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
