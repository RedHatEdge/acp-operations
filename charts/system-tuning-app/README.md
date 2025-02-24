# System Tuning App
This chart is a "meta-chart" that simply creates an application in ArgoCD, the declarative state management application on an ACP, for the system-tuning chart.

Once applied, a new application tile should appear, and the declarative state management tool (ArgoCD) will take over application and reconsiliation of the system-tuning application.

## Applying the Chart
Simply use helm to apply this meta-chart:
```
helm install -f /path/to/values.yaml system-tuning-app charts/system-tuning-app/
```

## Values Passing
When applying this chart, values will be loaded in to the application definition when being created:
```yaml
...
spec:
    helm:
      values: |
{{- toYaml .Values | nindent 8 }} # Add all .Values to application definition
```

## Self-Healing
By default, self-healing will be set to `true` when using this meta chart.
