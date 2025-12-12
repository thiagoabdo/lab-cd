# Gateway API Class Module

This module installs a default GatewayClass resource for the Gateway API using Envoy as the implementation.

## Components

- GatewayClass: Defines Envoy Gateway as the controller for Gateway API resources

## Dependencies

This module depends on the `envoy-gateway` module. The Envoy Gateway controller must be installed before this GatewayClass can be used.

## Usage

This module is deployed using Flux Kustomization resources with a dependency on the envoy-gateway module. 
The dependency is defined in the Kustomization resource using the `dependsOn` field to ensure proper deployment order.

The GatewayClass resource specifies `gateway.envoyproxy.io` as the controller, which matches the Envoy Gateway deployment.