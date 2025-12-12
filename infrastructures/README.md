# Infrastructure Modules

This directory contains Flux infrastructure components organized by module:

Currently available modules:
- `envoy-gateway/` - Envoy Gateway API implementation
- `gateway-api-class/` - Default GatewayClass for Envoy Gateway

## Usage

Each module is deployed using Flux Kustomization resources that can be found in the `clusters/homelab/infrastructures/` directory. 
The Kustomization resources define the deployment order using the `dependsOn` field to ensure proper dependencies are met.

For example, the `gateway-api-class` module depends on the `envoy-gateway` module, which is expressed through the `dependsOn` field in the Kustomization resource.