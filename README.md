# ClickHouse Deployment with Hot-Cold Configuration

This Helm chart enables you to deploy ClickHouse in a Kubernetes environment with a hot-cold configuration. ClickHouse is set up to automatically move data from the "hot" storage to the "cold" storage based on a `move_factor` threshold of 20%.

## Deployment Instructions

### 1. Clone and install clickhouse

Clone the Git repository containing the ClickHouse Helm chart.

```bash
cd clickhouse
helm install clickhouse . 
kubectl get all
```
![alt text](<Screenshot from 2024-05-06 13-24-57.png>)




### 4. go inside the ClickHouse Pod


```bash
kubectl exec -it <pod_name>  /bin/bash
```



### 5. Monitor Storage Usage

Check the disk usage of the "hot" and "cold" storage volumes within the ClickHouse container.

```bash
du -h /mnt/clickhouse/hot/
du -h /mnt/clickhouse/cold/
```

![alt text](<Screenshot from 2024-05-06 13-26-55.png>)


### 6. Interact with ClickHouse Client

Use the ClickHouse client to interact with the ClickHouse server.

```bash
clickhouse-client
```

### 7. Create ClickHouse Table

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

### 8. Insert Test Data

Insert test data into the ClickHouse table to trigger data movement from "hot" to "cold" storage.

```sql
insert into dz_test select number, number, '2023-01-01' from numbers(1e11)
```

## Testing Data Movement

- **Data Movement Trigger:**
  
  ClickHouse automatically starts moving data from "hot" storage to "cold" storage when the free disk space ratio drops below 20% (`move_factor = 0.2`). Monitor the disk usage to observe this behavior.

To verify data movement from "hot" to "cold" storage as demonstrated in the images below:


---

**Screenshots:**

### Before Data Insertion (Checking Disk Usage)

<img width="1037" alt="Screenshot 2024-05-05 at 9 44 49 PM" src="https://github.com/ashwaq06/Ashwaq-Datazip-Assignement/assets/80192952/94bb3105-3e5e-4708-9328-c163518aef39">

### During Data Insertion (Hot Storage Usage)

<img width="855" alt="Screenshot 2024-05-05 at 6 21 22 PM" src="https://github.com/ashwaq06/Ashwaq-Datazip-Assignement/assets/80192952/272f9d70-97c5-45eb-98e6-9c39c6edf910">

<img width="1393" alt="Screenshot 2024-05-05 at 9 47 07 PM" src="https://github.com/ashwaq06/Ashwaq-Datazip-Assignement/assets/80192952/26749848-7631-4838-951e-e8bc8f3882e7">

### After Data Movement to Cold Storage

<img width="1459" alt="Screenshot 2024-05-05 at 9 49 27 PM" src="https://github.com/ashwaq06/Ashwaq-Datazip-Assignement/assets/80192952/c95d0674-5bfb-4277-96e0-85e73eaadaa2">
# Ashutosh-Datazip-Assignment
