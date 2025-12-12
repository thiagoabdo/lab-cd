# Envoy Gateway API Module

This module installs the Envoy Gateway API Gateway implementation.

## Components

- HelmRepository: Defines the source for Envoy Gateway charts
- HelmRelease: Installs the Envoy Gateway using the official Helm chart

## Installation

The module will automatically deploy the Envoy Gateway with default settings.

## Configuration

To customize the installation, modify the values in the HelmRelease resource.