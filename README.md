# Sink-Connector

This setup is going to demonstrate how to receive events from MySQL database and stream them down to a PostgreSQL database using Kafka, Debezium, JDBC







                   +-------------+
                   |             |
                   |    MySQL    |
                   |             |
                   +------+------+
                          |
                          |
                          |
          +---------------v------------------+
          |                                  |
          |           Kafka Connect          |
          |  (Debezium, JDBC connectors)     |
          |                                  |
          +---------------+------------------+
                          |
                          |
                          |
                          |
                  +-------v--------+
                  |                |
                  |   PostgreSQL   |
                  |                |
                  +----------------+






# Start the application
export DEBEZIUM_VERSION=2.0


docker compose -f docker-compose-jdbc.yaml up --build



# Start PostgreSQL connector
curl -i -X POST -H "Accept:application/json" -H  "Content-Type:application/json" http://localhost:8083/connectors/ -d @jdbc-sink.json



# Start MySQL connector
curl -i -X POST -H "Accept:application/json" -H  "Content-Type:application/json" http://localhost:8083/connectors/ -d @source.json



Check contents of the MySQL database:

docker compose -f docker-compose-jdbc.yaml exec mysql bash -c 'mysql -u $MYSQL_USER -p$MYSQL_PASSWORD inventory -e "select * from customers"'

Verify that the PostgreSQL database has the same content:

docker compose -f docker-compose-jdbc.yaml exec postgres bash -c 'psql -U $POSTGRES_USER $POSTGRES_DB -c "select * from customers"'



Insert a new record into MySQL;

docker compose -f docker-compose-jdbc.yaml exec mysql bash -c 'mysql -u $MYSQL_USER  -p$MYSQL_PASSWORD inventory'

insert into customers values(default, 'Amit', 'Manjhi', 'a.manjhi@example.com');



Verify that PostgreSQL contains the new record:

docker compose -f docker-compose-jdbc.yaml exec postgres bash -c 'psql -U $POSTGRES_USER $POSTGRES_DB -c "select * from customers"'




![mysql](https://user-images.githubusercontent.com/42512407/223345681-70ef9f53-ab1b-4f54-b28f-05089cbfd84d.jpg)






![postgres](https://user-images.githubusercontent.com/42512407/223346044-63588eb0-cf0c-468a-a839-ddc5d6406008.jpg)



