# Github Containers (jobs and services)

> Container services are for a single job (fresh container for job and then gets eliminated when the job ends).
> Every job must define their own service 

| Feature      | Job Container (`container:`)                                  | Service Container (`services:`)                                      |
|--------------|---------------------------------------------------------------|-----------------------------------------------------------------------|
| Role         | The "Home" where your steps execute.                         | The "Sidecar" that provides a dependency (DB, Cache).                |
| Usage        | Replaces the runner's default OS environment.                | Runs alongside your code in a shared network.                        |
| Networking   | Steps run inside this container.                             | Steps talk to it via a hostname (the service label).                 |

```sh
jobs:
  test-database:
    runs-on: ubuntu-latest
    # This job runs inside a Node container
    container: node:20 
    
    # These services are sidecars to the job
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: password
      redis:
        image: redis

    steps:
      - name: Check Connectivity
        # Because we are in a 'job container', we use the service label as the host
        run: |
          ping -c 1 postgres
          ping -c 1 redis
```

| If your Job uses...        | Access Service via... | Do you need `ports:`? | Why? | Key Exam Memory Trigger |
|----------------------------|-----------------------|------------------------|------|--------------------------|
| runs-on: ubuntu-latest     | localhost:<port>      | ✅ Yes                 | The runner maps the service container port to the host’s loopback interface. | No job container → you must expose ports to reach the service. |
| container: node:20         | <service_label>:<port>| ❌ No                  | Both containers are on the same user-defined Docker bridge network; DNS resolves the service label automatically. | Job container + service container → same Docker network → use label as hostname. |