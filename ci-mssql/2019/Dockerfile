FROM mcr.microsoft.com/mssql/server:2019-latest

LABEL maintainer="Aimeos <aimeos@aimeos.org>"

# Install SQL Server 2019 Full Text Search
RUN apt-get update
RUN apt-get install -y apt-transport-https curl gnupg
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
RUN curl https://packages.microsoft.com/config/ubuntu/18.04/mssql-server-2019.list -o /etc/apt/sources.list.d/mssql-server.list
RUN apt-get update
RUN apt-get install -y mssql-server mssql-server-fts
RUN apt-get clean &&  rm -rf /var/lib/apt/lists/*
