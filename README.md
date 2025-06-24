# Xandeum Pod APT Repository

## 🛠️ Installation Instructions

To install the `pod` binary on any Debian/Ubuntu-based system:

```bash
# Step 1: Enable HTTPS transport
sudo apt-get install -y apt-transport-https ca-certificates

# Step 2: Add the Xandeum APT repository
echo "deb [trusted=yes] https://xandeum.github.io/pod-apt-package/ stable main" | sudo tee /etc/apt/sources.list.d/xandeum-pod.list

# Step 3: Update package index
sudo apt-get update

# Step 4: Install the pod binary
sudo apt-get install pod
