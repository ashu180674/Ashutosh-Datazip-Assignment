#     Ashutosh-Datazip-Assignment 

ClickHouse automatically move data from the "hot" storage to the "cold" storage based on a `move_factor` threshold of 20%.

### Prerequisite
- Helm should be available locally.
- Minikube/Kind kuberenetes Cluster should be up and running.


### Clone and install clickhouse

Clone the Git repository containing the ClickHouse Helm chart.

```bash
cd clickhouse
helm install clickhouse . 
kubectl get all
```
![alt text](<Screenshot from 2024-05-06 13-24-57.png>)




### go inside the ClickHouse Pod


```bash
kubectl exec -it <pod_name>  /bin/bash
```



###  Monitor Storage Usage

Check the disk usage of the "hot" and "cold" storage volumes within the ClickHouse container.

```bash
du -h /mnt/clickhouse/hot/
du -h /mnt/clickhouse/cold/
```

![alt text](<Screenshot from 2024-05-06 13-26-55.png>)


### Interact with ClickHouse Client

Use the ClickHouse client to interact with the ClickHouse server.

```bash
clickhouse-client
```

### Create ClickHouse Table

Create a sample table (`dz_test`) in ClickHouse with the specified schema and storage settings.

```sql
CREATE TABLE dz_test
(
    `B` Int64,
    `T` String,
    `D` Date
)
ENGINE = MergeTree
PARTITION BY D
ORDER BY B
SETTINGS storage_policy = 'hot_cold_policy';
```

![alt text](<Screenshot from 2024-05-06 13-29-44.png>)

###  Insert Test Data

Insert test data into the ClickHouse table to trigger data movement from "hot" to "cold" storage.

```sql
insert into dz_test select number, number, '2023-01-01' from numbers(1e11)
```

## Testing Data Movement

- **Data Movement Trigger:**
  
  ClickHouse automatically starts moving data from "hot" storage to "cold" storage when the free disk space ratio drops below 20% (`move_factor = 0.2`). Monitor the disk usage to observe this behavior.

To verify data movement from "hot" to "cold" storage as demonstrated in the images below:


---



- During the Data Insertion (Hot Storage Usage)
![Screenshot from 2024-05-06 13-44-51](https://github.com/ashu180674/Ashutosh-Datazip-Assignment/assets/105533911/1ee0fcc6-9191-4106-9272-fbb097c4d65d)


- After the Data Movement to Cold Storage

<img width="1459" alt="Screenshot 2024-05-05 at 9 49 27 PM" src="https://github.com/ashwaq06/Ashwaq-Datazip-Assignement/assets/80192952/c95d0674-5bfb-4277-96e0-85e73eaadaa2">
# Ashutosh-Datazip-Assignment
