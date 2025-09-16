__This project has been archived and replaced by the following repos.__

- [Itential Automation Platform (IAP)](https://github.com/itential/iap-helm)
- [Itential Automation Gateway 4](https://github.com/itential/iag4-helm)
- [Itential Automation Gateway 5](https://github.com/itential/iag5-helm)

# Helm charts for Itential Automation Platform and Itential Automation Gateway
This repo contains helm charts for running Itential Automation Platform and Itential Automation Gateway in Kubernetes. This repo contains two charts: iap and iag.

## Itential Automation Platform (IAP)
The chart will not install any dependencies of the IAP application. The chart assumes that those are running, configured, and bootstrapped with all necessary data. The application is installed using a Kubernetes statefulset. It also includes persistent volume claims, ingress, and other Kubernetes objects suitable to run the application.

### Volumes
| Name              | Type                    | Description                                                                                                            |
|-------------------|-------------------------|------------------------------------------------------------------------------------------------------------------------|
| config-volume     | Configmap               | Configuration properties for the IAP properties.json file. This is the main config file for the IAP application. |
| iap-logs-volume   | Persistent Volume Claim | A persistent volume claim to mount a directory to write IAP log files to                                               |
| iap-assest-volume | Persistent Volume Claim | A persistent volume claim to mount a directory that includes adapters and apps                                         |

### Usage
`helm install iap ./iap`
This will install IAP according to how its configured in the values.yaml file ("latest").
`helm install iap ./iap --set image.tag=2023.2.7`
This will install IAP with the "2023.2.7" image.
`helm install iap ./iap --sete propertiesJson.dbUrl="mongodb+srv://<some-username>:<some-password>@<some-mongo-url>"`
This will install IAP using the mongo connection string provided.

### How to construct the iap-asset-volume
This volume is intended to store the applications and adapters. Its contents will reflect a customer's unique usage of IAP and contain all of the adapters and custom applications required. There is an expectation in the container of the structure of the files in this volume. It should look like this:
```
.
└── node_modules/
    ├── @itentialopensource/
    │   ├── opensource-adapter1
    │   └── opensource-adapter2
    ├── @itential/
    │   └── app-artifacts
    └── @customer-namespace/
        ├── custom-adapter1
        └── custom-adapter2
```
This will be correctly translated inside the container to the appropriate directories for IAP to understand.

### How to add TLS/SSL certificates to the container
To add additional TLS or SSL certificates to the container created by Kubernetes, add any certificates to the `files/certificates` directory in either the IAP or IAG projects. Any certificates that are placed in that directory will be added to the container at `/etc/ssl/certs`.

## Itential Automation Gateway (IAG)
The application is installed using a Kubernetes statefulset. It also includes persistent volume claims, ingress, and other Kubernetes objects suitable to run the application.

### Volumes
| Name            | Type                    | Description                                                                                                              |
|-----------------|-------------------------|--------------------------------------------------------------------------------------------------------------------------|
| config-volume   | Configmap               | Configuration properties for the IAG properties.yaml file. This is the main config file for the IAG application.         |
| iag-data-volume | Persistent Volume Claim | This represents the claim for the persistence for the sqlite data required by IAG.                                       |
| iag-code-volume | Persistent Volume Claim | This represents the claim for the location of all the customer authored scripts, ansible code, and other customizations. |

### Usage
`helm install iag ./iag`
This will install IAG according to how its configured in the values.yaml file ("latest").
`helm install iag ./iag --set image.tag=2023.2.7`
This will install IAG with the "2023.2.7" image.

### How to construct the iag-code-volume
This volume is intended to store any custom assets that the user is bringing to IAG. Its contents will reflect a customer's unique usage of IAG. There is an expectation in the container of the structure of the files in this volume. It should look like this:
```
.
├── ansible/ # custom ansible assets
│   ├── collections
│   ├── inventory
│   ├── modules
│   ├── playbooks
│   └── roles
├── venv/ # custom python virtual environments
├── nornir/
│   ├── conf
│   ├── inventory
│   └── modules
└── scripts/ # custom script assets
```

### How to add TLS/SSL certificates to the container
To add additional TLS or SSL certificates to the container created by Kubernetes, add any certificates to the `files/certificates` directory in either the IAP or IAG projects. Any certificates that are placed in that directory will be added to the container at `/etc/ssl/certs`.
