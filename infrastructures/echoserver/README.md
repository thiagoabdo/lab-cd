# Echoserver Module

This module deploys a simple echoserver application that can be used for testing and debugging network connectivity in the cluster.

## Overview

The echoserver is a lightweight HTTP server that echoes back request information including:
- Request headers
- Request body
- Client IP address
- Protocol information
- Environment variables

## Components

- **Namespace**: `echoserver`
- **Deployment**: Single replica echoserver
- **Service**: ClusterIP service on port 80
- **HTTPRoute**: Gateway API route for external access

## Usage

This module is designed to be deployed via Flux CD and integrates with the existing Envoy Gateway infrastructure.

## Configuration

The echoserver deployment can be customized by modifying the deployment manifest. The default configuration uses:
- Image: `ealen/echo-server:latest`
- Resource limits: 100m CPU, 128Mi memory
- Single replica for development/testing purposes

## Access

Once deployed, the echoserver can be accessed through the Gateway at:
- HTTP: `http://echo.homelab.local`

The hostname can be customized by modifying the HTTPRoute manifest.