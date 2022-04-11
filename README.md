mSupply Superset proof of concept

## Setup

Refer to https://superset.apache.org/docs/installation/installing-superset-using-docker-compose

1. Setup database in local Postgres
   1. `psql -c "create database superset;"`
   2. `psql -c "create user superset with encrypted password 'superset';"`
   3. `psql -c "grant all privileges on database superset to superset;"`
2. Update .env
   1. Depending on local OS, how you connect to the host from inside docker changes, update docker/.env-msupply entry DATABASE_HOST
   
## Start

1. `docker-compose -f docker-compose-msupply.yml pull`
2. `docker-compose -f docker-compose-msupply.yml up`
3. Open localhost:8088

## Configure TupaiaApp access

1. Create Role
   1. Settings > List Roles > New
   2. Name: TupaiaAppRole
   3. Permissions: [can read on Chart, all datasource access on all_datasource_access]
   4. Note: all datasource access means access to the one and only database where the data is stored. Without this permission fetching data via the API below is not allowed.
2. Create User
   1. Settings > List Users > New
   2. Name: TupaiaApp
   3. Username: TupaiaApp
   4. Password: TupaiaApp
   5. Email: igor@beyondessential.com.au
   6. Is Active: Yes
   7. Roles: [TupaiaAppRole]
3. Create example chart
   1. ...
4. Test access via API
   1. First request get access key:
       ```
       POST localhost:8088/api/v1/security/login
       {
       "password": "TupaiaApp",
       "provider": "db",
       "refresh": true,
       "username": "TupaiaApp"
       }
       ```
   2. Second request fetch chart data:
       ```
       GET localhost:8088/api/v1/chart/133/data/
       Authorization: Bearer <access key from above>
       ```
       returns
       ```
       {
         "result": [{
           ...
           "data": [{
             "country_code": "AFG",
             "SP_RUR_TOTL_ZS": 73.718,
             "SP_RUR_TOTL": 23315165.0
           },
           {
             "country_code": "ASM",
             "SP_RUR_TOTL_ZS": 12.736,
             "SP_RUR_TOTL": 7060.0
           }, ...],
         }]
       }
       ```