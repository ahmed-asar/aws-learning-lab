## Stage 1 - Single Server

### Architecture

                Internet 
                    |
                    v
            +---------------+
            |   Web Server  |
            |   App + DB    |
            +---------------+

### Problem with the previous architecture

- This is the starting point, so there is no previous architecture.

### How this architecture solves it

- Simple to deploy and manage.
- Low cost.
- Application and data are tightly coupled on a single machine, making deployment simple but reducing resilience.

### Problems

- Single point of failure.
- Limited CPU, RAM, and storage.
- Maintenance causes downtime.
- Cannot handle high traffic.


## Stage 2 - Two Servers Behind a Load Balancer

### Architecture

                Internet 
                    |
                    v
            +---------------+
            | Load Balancer |
            +---------------+
                    |
        +-----------+-----------+
        |                       |
        v                       v
 +-------------+         +-------------+
 | App Server1 |         | App Server2 |
 +-------------+         +-------------+

### Problem with the previous architecture

- One server means any failure takes the entire application offline.
- Traffic is limited by a single machine.
- Updates require downtime.

### How this architecture solves it

- Requests are distributed across multiple servers.
- Higher availability if one server fails.
- Increased capacity by adding more servers.
- Enables rolling deployments.

### Problems

- *Sessions may be stored on only one server.*
- User-uploaded files are not shared.
- Data synchronization becomes necessary.
- More infrastructure to manage.

## Stage 3 - Auto Scaling

### Architecture

                        Internet 
                            |
                            v
                    +---------------+
                    | Load Balancer |
                    +---------------+
                            |
        +-------------------+-------------------+
        |                   |                   |
        v                   v                   v
 +-------------+      +-------------+      +-------------+
 | App Server1 |      | App Server2 |      | App Server3 |
 +-------------+      +-------------+      +-------------+

### Problem with the previous architecture

- Fixed number of servers.
- During traffic spikes, the application may become overloaded.
- During quiet periods, servers sit idle and waste money.

### How this architecture solves it

- Automatically adds servers when traffic increases.
- Removes unnecessary servers when demand decreases.
- Better cost efficiency.
- Improved performance during peak loads.

### Problems

- New servers start empty.
- Uploaded files disappear when an instance is terminated unless stored elsewhere.
- Applications must be stateless.
- Scaling decisions and health checks require proper configuration.

## Stage 4 - Shared Storage

### Architecture

                        Internet 
                            |
                            v
                    +---------------+
                    | Load Balancer |
                    +---------------+
                            |
        +-------------------+-------------------+
        |                   |                   |
        v                   v                   v
 +-------------+      +-------------+      +-------------+
 | App Server1 |      | App Server2 |      | App Server3 |
 +------+------+      +------+------+      +------+------+
        |                    |                    |
        +---------+----------+----------+---------+
                             |
                             v
                    +------------------+
                    |  Shared Storage  |
                    | (NFS / EFS / S3) |
                    +------------------+

### Problem with the previous architecture

- Each server has its own local files.
- If a user uploads a file to one server, another server cannot access it.
- Auto Scaling causes files to be lost when instances are terminated.

### How this architecture solves it

- All servers read and write to the same storage.
- Files remain available regardless of which server handles the request.
- Newly created instances immediately have access to existing files.
- Supports Auto Scaling without losing shared data.

### Problems

- Shared storage removes bottlenecks from the application layer but may introduce bottlenecks at the storage layer.
- It introduces another component that must be highly available.
- Network latency is higher than local disk access.
- Permissions, locking, backups and storage costs must be managed.
- If the shared storage fails and there is no redundancy, all application servers lose access to their files. 