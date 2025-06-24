# Xandeum Pod APT Repository

This repository contains the official Debian (`.deb`) packages for Pod, distributed via an APT repository.

## Quick Installation

Run the following commands to add the repository and install the `pod` package.

**1. Add the Repository:**

This command adds the Xandeum repository to your system's package sources. The `[trusted=yes]` flag bypasses the need for a GPG key.

```sh
echo "deb [trusted=yes] [https://xandeum.github.io/pod-apt-package/](https://xandeum.github.io/pod-apt-package/) stable main" | sudo tee /etc/apt/sources.list.d/xandeum-pod.list
