## System Components

1. Temporal - durable workflow orchestration
2. SigNoz - observability

## Dsynct

1. A set of *workers* that execute tasks (assigned by Temporal) for a particular migration or replication workflow
2. A *runner* job that submits a workflow for execution and exposes a progress Web report over HTTP