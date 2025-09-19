## SigNoz

Note that you need to modify the yaml file to specify a persistent storage class to use for SigNoz to store data.
```
helm install signoz signoz/signoz    --namespace dsync --create-namespace    --wait    --timeout 1h    -f signoz_values.yaml
```

After SigNoz is running, you can [import](https://signoz.io/docs/dashboards/import-dashboard/) the dashboard json into it.

## Temporal

Note that you need to modify the yaml file to specify a persistent storage class to use for Temporal to store data.
```
kubectl apply -n dsync -f temporal-dev.yaml
```