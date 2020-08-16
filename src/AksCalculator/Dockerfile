FROM microsoft/dotnet:sdk AS build-env
WORKDIR /app

# Copy csproj and restore as distinct layers
COPY *.sln ./
COPY AksCalculator/*.csproj ./AksCalculator/
RUN dotnet restore

# Copy everything else and build
COPY . ./
RUN dotnet publish -c Release -o out

# Build runtime image
FROM microsoft/dotnet:aspnetcore-runtime
WORKDIR /app
COPY --from=build-env ./app/AksCalculator/out .

WORKDIR /azp
COPY AgentScripts/install-agent.sh .
RUN chmod +x install-agent.sh


ENTRYPOINT ["dotnet", "AksCalculator.dll"]

CMD ["./install-agent.sh"]
