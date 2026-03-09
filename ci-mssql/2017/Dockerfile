FROM mcr.microsoft.com/mssql/server:2017-latest

USER root

LABEL maintainer="Aimeos <aimeos@aimeos.org>"

# Install SQL Server 2017 Full Text Search
RUN apt-get install -y apt-transport-https curl gnupg
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
RUN curl https://packages.microsoft.com/config/ubuntu/16.04/mssql-server-2017.list -o /etc/apt/sources.list.d/mssql-server.list
RUN apt-get update && apt-get install -y mssql-server-fts
RUN apt-get clean &&  rm -rf /var/lib/apt/lists/*

USER mssql