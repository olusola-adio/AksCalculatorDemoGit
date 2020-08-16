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

# Install dependencies for Azure DevOps agent
RUN apt-get update 
# Required to install libicu55 on Ubuntu versions > 16.04, the base image of owasp/zap2docker-stable at the time of writing is later than 16.04
RUN apt-get install software-properties-common
RUN add-apt-repository "deb http://security.ubuntu.com/ubuntu xenial-security main"

RUN apt-get install --no-install-recommends \
    ca-certificates \
    jq \
    git \
    iputils-ping \
    libcurl3 \
    libicu55 \
    libunwind8 \
    netcat
# curl install returns broken package error if installed alongside other packages
RUN apt-get install --no-install-recommends curl
# Finished installing dependencies for Azure DevOps agent

WORKDIR /azp
COPY AgentScripts/install-agent.sh .
RUN chmod +x install-agent.sh


#ENTRYPOINT ["dotnet", "AksCalculator.dll"]

CMD ["./install-agent.sh"]
