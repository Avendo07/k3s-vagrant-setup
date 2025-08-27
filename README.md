## K3S Ansible Vagrant Setup for Local System
Vagrant and ansible setup to configure a k3s setup with 3 VM nodes created using Vagrant

6443 port should be open for the local system to access the cluster
OR use `kubectl --insecure-skip-tls-verify get pods --all-namespaces` to bypass it