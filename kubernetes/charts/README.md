## Start workers

You can add shared files (transformer.yml, for example) into dsync/config_files directory. They will be made available to all workers under the `/mnt/config` path.

```
helm install --namespace dsync test dsync/ -f dsync/values-worker.yaml
```

## Start the workflow (runner job)

```
helm install --namespace dsync test-wf1 dsync/ -f dsync/values-runner.yaml
```

## Observability

After the workflow has been started, you should be able to see:
1. The progress report on the runner service :8080
2. Workflow being executed in Temporal (temporal-dev:8233)
3. Metrics and logs in SigNoz (signoz:8080)