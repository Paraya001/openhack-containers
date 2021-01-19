# Challenge 1

1. run the sql container:

   `docker run --network openhacknet -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password123!' -p 1433:1433 --name sql1 -h sql1 -itd mcr.microsoft.com/mssql/server:2017-latest`

1. seed the sql db

   `docker run --network openhacknet -e 'SQLFQDN=sql1' -e 'SQLUSER=sa' -e 'SQLPASS=Password123!' -e 'SQLDB=mydrivingDB' openhack/data-load:v1`

1. run the POI container

   `docker run --network openhacknet -d -p 8080:80 --name poi -e 'SQL_PASSWORD=Password123!' -e 'SQL_SERVER=sql1' -e 'ASPNETCORE_ENVIRONMENT=Local' -e 'SQL_USER=sa' tripinsights/poi:1.0`

1. access the poi container

   `docker exec -it poi 'sh'`

1. test the sql poi connection:

   `curl -i -X GET 'http://localhost:80/api/poi`
