# Build stage
FROM --platform=${BUILDPLATFORM} mcr.microsoft.com/dotnet/sdk:7.0 AS build
ARG TARGETPLATFORM
ARG BUILDPLATFORM

RUN echo "Building on $BUILDPLATFORM for $TARGETPLATFORM"

# Set working directory
WORKDIR /source

# Copy NuGet config first (must disable IPv6, add nuget.org explicitly)
COPY NuGet.config .

# Copy project file and restore dependencies
COPY *.csproj ./
RUN dotnet restore --configfile NuGet.config --disable-parallel --verbosity minimal

# Copy remaining source code and publish
COPY . .
RUN dotnet publish -c Release -o /app --self-contained false --no-restore

# Final runtime image
FROM mcr.microsoft.com/dotnet/runtime:7.0
WORKDIR /app
COPY --from=build /app .
ENTRYPOINT ["dotnet", "Worker.dll"]

