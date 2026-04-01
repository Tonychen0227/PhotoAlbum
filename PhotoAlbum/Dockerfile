# Stage 1: Build
FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
WORKDIR /src/PhotoAlbum

# Copy project file and restore dependencies (layer cache optimisation)
COPY ["PhotoAlbum/PhotoAlbum.csproj", "./"]
RUN dotnet restore "PhotoAlbum.csproj"

# Copy the rest of the source and publish
COPY PhotoAlbum/ ./
RUN dotnet publish "PhotoAlbum.csproj" -c Release -o /app/publish

# Stage 2: Runtime
FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS final
WORKDIR /app

# Run as non-root user for security
RUN addgroup --system appgroup && adduser --system --ingroup appgroup appuser
USER appuser

COPY --from=build /app/publish .

# Expose HTTP port
EXPOSE 8080

# Set ASP.NET Core to listen on port 8080 (non-root default)
ENV ASPNETCORE_URLS=http://+:8080
ENV ASPNETCORE_ENVIRONMENT=Production

ENTRYPOINT ["dotnet", "PhotoAlbum.dll"]
