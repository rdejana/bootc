# Bootable Containers
Containrs have and contiue to reshape how applications a developed, deployed, and managed.  With this in mind, bootable containers are emerging as a means of using container concepts in support of building full fledged VMs.  With bootable containers, virtual machines can be described declaratively, using the familiar Dockerfile (though I'll try to call it Containerfile). By leveraging bootable containers, VMs become as portable and manageable as a containerized app, bridging traditional OS management with modern DevOps practices.

## Requirements
This repo will be MacOS on Apple Silicon centric and use the following software for building the images and running the VM

- Podman Desktop with bootc extension
- VMWare Fusion

In addition, access to an image registry (e.g. quay.io) is required.

## Installation
TBD


## Getting started
- Start Podman Desktop.
- Clone this repository.
- Change to the directory `bootc`.

## Examples
- [Example1](example1/README.md) - Build your first bootable container based VM.
- [Example2](example2/README.md) - Build a bootable container from the CLI.

