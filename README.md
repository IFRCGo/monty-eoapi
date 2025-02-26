his chart is used for maintaning the deployment of the **Monty eoAPI**.
It is structured as follows:

```console
├── README.md (this file)
├── Chart.yaml (Main Chart descriptor)
├── components/ (components values.yaml templates)
│   └── eoapi-values.yaml (template values for [eoapi](https://github.com/developmentseed/eoapi-k8s/tree/main/helm-chart/eoapi))
|   └── eoapi-support-values.yaml (template values for [eoapi-support](https://github.com/developmentseed/eoapi-k8s/tree/main/helm-chart/eoapi-support))
├── templates/ (templates for the different application of the project)
│   │── project.yaml (template for argocd project)
│   └── eoapi-app.yaml (template for eoapi application)
|   └── eoapi-support-app.yaml (template for eoapi-support application)
└── values.yaml (main values for the project)
```

The bootstrap command to register the super app is:

```console
argocd app create monty-eoapi \
    --dest-namespace argocd \
    --dest-server https://kubernetes.default.svc \
    --repo https://github.com/IFRCGo/monty-eoapi.git \
    --revision main \
    --path . \
    --values environements/production/values.yaml \
    --sync-policy automated
```

or using one of the bootstrap app in `app-bootstraps` folder.

Please not that the command above is for the production environment. For other environments, please change the `--values` flag accordingly.