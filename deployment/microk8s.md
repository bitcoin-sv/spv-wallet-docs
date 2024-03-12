# Deploying the SPV Wallet Stack Using a MicroK8s Cluster

In order to demonstrate the components working together to form a BSV wallet, a guide for deploying SPV Wallet using a Micro Kubernettes cluster is detailed here.

At a high level there are only a few steps:

1. Fork the [k8sconfig repository](https://github.com/bitcoin-sv/k8s-config) such that you can link it to your own project, updating details as required.
2. Spin up a VPS.
3. Create some DNS records.
4. Setup the VPS by running a script.
5. Copy and paste an ssh key to your forked repo.
6. Deploy the SPV Wallet stack by running a script.
7. Navigate to `https://cd.your-domain.tld` and login with the username and password echoed out by the install script to checkout the dashboard.

Detailed instructions are available to follow in the [README of the example repo](https://github.com/bitcoin-sv/k8s-config).
