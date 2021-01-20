# Challenge 1

1. Create a docker network

   `docker network create openhacknet`

   - To check whether the network was created successfully `docker network ls`

1. Run the docker SQL Server 2017 image to create a local :

   `docker run --network openhacknet -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password123!' -p 1433:1433 --name sql1 -h sql1 -itd mcr.microsoft.com/mssql/server:2017-latest`

1. Access the local sql server and create a the required db

   `CREATE DATABASE mydrivingDB;`

1. Seed the sql db

   `docker run --network openhacknet -e 'SQLFQDN=sql1' -e 'SQLUSER=sa' -e 'SQLPASS=Password123!' -e 'SQLDB=mydrivingDB' openhack/data-load:v1`

1. Run the POI container within the poi dir

   `docker run --network openhacknet -d -p 8080:80 --name poi -e 'SQL_PASSWORD=Password123!' -e 'SQL_SERVER=sql1' -e 'ASPNETCORE_ENVIRONMENT=Local' -e 'SQL_USER=sa' tripinsights/poi:1.0`

1. Access the poi container

   `docker exec -it poi 'sh'`

1. Test the poi to sql connection:

   `curl -i -X GET 'http://localhost:80/api/poi`

## Additional resources:

- <https://docs.microsoft.com/en-us/sql/azure-data-studio/quickstart-sql-server?view=sql-server-ver15>
- <https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-linux-2017&preserve-view=true&pivots=cs1-bash#pullandrun2017>
- <https://www.c-sharpcorner.com/article/sql-server-2017-docker-container-and-web-api-in-net-core/>
