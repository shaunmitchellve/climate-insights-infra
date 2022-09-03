# Climate Insights - Infrastructure

This repo has the pakcages needed to setup the Climage Insights Infrastructure. It utilizes the Kubernetes Config Controller / Connector service in order to setup the Google Cloud infra. For easy creation of KCC on Google Cloud see  [Pubsec Devlarative Toolkit](https://github.com/GoogleCloudPlatform/pubsec-declarative-toolkit).

The packages are:

## Base

- 2 VPC Subnets (NOTE: The VPC needs to be setup manually as per your requirments)
  - First subnet is used for the GKE cluster, it's primary CIDR range is for the GKE nodes and the two secondary ranges are for the GKE pods and services.
  - The second subnet is for the internal load balancer to, internal HTTPS load balancing for GKE uses Network Endpoints Groups (NEGs) which proxies requests for the GKE services. (https://cloud.google.com/load-balancing/docs/l7-internal#proxy-only_subnet)
- GCS storage bucket that can be a target for any outputs while calling the application
- A GCE bastion host that uses IAP and OSLogin for secure access, the bastion host is granted access to the GKE control plane for administration purposes. No outside access to the control plane is granted in this solution for security reasons.
- Memorystore for redis instance is created for Climate Insights caching

## Priate GKE Standard
- Creates a private GKE Standard cluster (3 nodes) by default with BQ GKE Metering, Pub/sub notifications setup

## Private GKE Autopilot
- Create a private GKE Autopilot cluster with BQ GKE Metering, Pub/sub notifications setup

## Install

These packages were created to be installed manually using `kpt` or using the Google Cloud [Pubsec Devlarative Toolkit](https://github.com/GoogleCloudPlatform/pubsec-declarative-toolkit) `arete` cli.

### kpt
NOTE: This assumes you have setup KCC and have the correct kubectl context.

- Run `kpt pkg get https://github.com/shaunmitchellve/climate-insights-infra/base@main` to get the base install package
- Modify the `interface.yaml` file to meet your Google Cloud environment. NOTE: The VPC must be created manually / exist prior to install
- Run `kpt live init`
- Run `kpt live apply`

### arete
NOTE: This assumes you have setup KCC and have the correct kubectl context.

- Run `arete solution get https://github.com/shaunmitchellve/climate-insights-infra --branch=main --sub-folder=base`
- Run `arete solution list` and confirm that the solution was added to arete
- Run `arete solution deploy climate-insights-full-base-install`

arete will not prompt you for some information. These solutions are not setup to prompt for all configurable items, if you want to change all the default values then modify the `interface.yaml` file in your `~/.arete/climate-insights-full-base-install` folder (or the climate insights GKE folders if you are installing those modules) prior to running the `arete solution deploy` command.