# Camel-k : Kamelet config experimentations

https://camel.apache.org/camel-k/1.12.x/kamelets/kamelets-user.html#_configuration

These examples uses mariadb.

##

Mariadb installation:

```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-mariadb bitnami/mariadb --version 11.5.6 -f mariadb/values.yaml
```


```sh
kubectl run my-mariadb-client --rm --tty -i --restart='Never' --image  docker.io/bitnami/mariadb:10.6.12-debian-11-r16 --namespace default --command -- bash
mysql -h my-mariadb.default.svc.cluster.local -uroot -p my_database
```

In mariadb:
```
CREATE TABLE Customers (
    CustomerID int,
    LastName varchar(255),
    FirstName varchar(255),
    Address varchar(255),
    City varchar(255)
);
INSERT INTO Customers VALUES (1, "LN", "FN", "AD", "CT");
```

### kubernetes configmaps/secrets

```sh
# default secret
kubectl create secret generic mariadb-source \
    --from-literal=password=root \
    --from-literal=username=root \
    --from-literal=serverName=my-mariadb \
    --from-literal=databaseName=my_database \
    --from-literal='query=SELECT count(*) FROM Customers'
kubectl label secret mariadb-source camel.apache.org/kamelet=mariadb-source

# custom secret containing all the parameters with a different the query
kubectl create secret generic mariadb-source.integrationcustom \
    --from-literal=password=root \
    --from-literal=username=root \
    --from-literal=serverName=my-mariadb \
    --from-literal=databaseName=my_database \
    --from-literal='query=SELECT * FROM Customers'
kubectl label secret mariadb-source.integrationcustom camel.apache.org/kamelet=mariadb-source camel.apache.org/kamelet.configuration=integrationcustom


# custom secret containing only a different the query
kubectl create secret generic mariadb-source.pipecustom \
    --from-literal='query=SELECT * FROM Persons'
kubectl label secret mariadb-source.pipecustom camel.apache.org/kamelet=mariadb-source camel.apache.org/kamelet.configuration=pipecustom
```


## Integrations

### Basic usage

kamel run basic.groovy

### Custom usage

kamel run custom.groovy