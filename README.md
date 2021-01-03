# Deploy Konvoy with Kaptain Addon

## Initialize and Deploy

### Verify Konvoy is working
`konvoy --version`

If this is a new install, it may take a minute or two for the Docker layers to download and decompress.  You should see output that looks like the following:
```
{
    "Version": "v1.6.1",
    "BuildDate": "Fri Dec 18 17:17:12 UTC 2020"
}
```

### Initialize Your Deployment
In this exampple, we will be provisioning the Konvoy cluster on AWS.
```
konvoy init --provisioner=aws
```
Assuming all goes as planned, you should see output that looks like the following:
```
Created configuration file successfully!
```

### Modify the `cluster.yaml` File
 Make the following changes to the `cluster.yaml` file that was created during the init step.  Examples of cluster.yaml are included in this repo in the files `cluster.before` and `cluster.after`.

#### Enable Istio (Change False to True)
```
kind: ClusterConfiguration
  addons:
    - configRepository: https://github.com/mesosphere/kubernetes-base-addons
      configVersion: stable-1.18-3.0.1
      addonsList:
        - name: istio # Istio is currently in Preview
          enabled: true
```

#### Disable Kommander (Change True to False)
```
kind: ClusterConfiguration
  addons:
    - configRepository: https://github.com/mesosphere/kubeaddons-kommander
      configVersion: stable-1.18-1.2.0
      addonsList:
        - name: kommander
          enabled: false
```

#### Add the Addon for Kaptain components
```
kind: ClusterConfiguration
  addons:
    - configRepository: https://github.com/mesosphere/kubeaddons-kaptain
      configVersion: stable-1.18-0.6.0
      addonsList:
        - name: kaptain
          enabled: true
        - name: knative
          enabled: true
        - name: kubeflow-nfs
          enabled: true
```

### Run the Deployment
```
konvoy up -y
```
After about 30-45 minutes, assuming everything went well, and your account has the proper permissions and quotas, you should see output that looks like the following:
```
Navigate to the URL below to access various services running in the cluster.
  https://aa20e9fca38c84ca4a72c71ffc157aa9-1176865152.us-west-2.elb.amazonaws.com/ops/landing
And login using the credentials below.
  Username: serene_hypatia
  Password: tAnWw4S5kwiyKsrmAexbFLEhM83vPNAywU1Ul41YKOdVtPMoU1hH2lxrJfUapds3

If the cluster was recently created, the dashboard and services may take a few minutes to be accessible.
```

### Access the Konvoy Operations Portal
Use URL and credentials provided at the end of the install process to access the Konvoy Ops Portal.  If you no longer have them, you can use the following command to rtetriecve them:
```
konvoy get ops-portal
```

Look around the Operations Portal.  Kaptain is designed to take advantage of the inherent User Access Control, Metrics, alerting, and Logging provided by the Konvoy Operations Portal


## Retrieve the Kaptain Browser URL

You have two options to retrieve the Kaptyain URL.
* If you ARE familiar with the kubernetes cli `KUBECTL` and have it installed, you can find the Kaptain Browser UI using the following commands: 
  * Retrieve Kaptain UI via KUBECTL

* If you ARE NOT familiar with the Kubernetes CLI `KUBECTL`, or do not have it installed on your local machine, we can extract the Kaptain browser UI from the Kubernetes Dashboard provided via the Konvoy Operations Portal using the following directions:
  * Kaptain UI via the Kubernetes Dashboard

## Retrieve Kaptain UI URL Via KUBECTL

## Merge your kubeconfig
Merge the newly created `admin.conf` with you local kubeconfig (this requires that the Kubernetes CLI `kubectl` is installed and running on your host).
```
konvoy apply kubeconfig
```

## Access the Kaptain User Interface

```
$ kubectl get services -n kubeflow | grep kubeflow-ingressgateway
```
You should see something like the following:
```
kubeflow-ingressgateway   LoadBalancer  10.0.60.198  a60ae5bac77814fc2ac59c61df9cddf7-1779517899.us-west-2.elb.amazonaws.com   15021:30340/TCP,80:32221/TCP,443:30635/TCP,15443:30959/TCP  22m
```

Copy the Load Balancer address that was created.  In the above example, it is the following:
 `a60ae5bac77814fc2ac59c61df9cddf7-1779517899.us-west-2.elb.amazonaws.com`

### Open the Kaptain Web Page UI
Place the copied address from the previous step into address bar, prepended with `https://`.  In this example, it will look like the following:
 `https://a60ae5bac77814fc2ac59c61df9cddf7-1779517899.us-west-2.elb.amazonaws.com`

when prompted, accept any security warnings, and add your credentials







konvoy down -y







