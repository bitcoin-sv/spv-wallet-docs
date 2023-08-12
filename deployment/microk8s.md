# Deploying the BUX Stack Using a MicroK8s Cluster

In order to demonstrate the components working together to form a BSV wallet, a guide for deploying BUX using a Micro Kubernettes cluster is detailed here.

At a high level there are only a few steps:

1. Fork the [k8sconfig repository](https://github.com/bitcoin-sv/bux-k8s-config) such that you can link it to your own project, updating details as required.
2. Spin up a VPS - these instructions were built config was tested using a Contabo host, instructions for AWS and other well known hosting providers will follow soon™.
3. Create some DNS records for subdomains and a paymail service.
4. Find & Replace `DOMAIN_NAME_TLD` with your own domain.
5. Run the install script, and wait a few minutes for everything to get up and running.
6. Navigate to `https://cd.your-domain.tld` and login with the username and password echoed out by the install script to checkout the dashboard.

[Details](./details.md) of the k8sconfig repo structure and some explanation of what each config file is doing follow.
