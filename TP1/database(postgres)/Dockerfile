FROM postgres:17.2-alpine

ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd

COPY 01_Init_Tables.sql 02_Insert_Data.sql /docker-entrypoint-initdb.d/
