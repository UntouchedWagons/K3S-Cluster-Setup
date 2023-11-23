# https://github.com/NVIDIA/gpu-operator/issues/522#issuecomment-1545574686
# https://github.com/NVIDIA/nvidia-docker/issues/1616#issuecomment-1571404575
# On the node with the GPU
    sudo apt install -y gpg
    curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
    && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
        sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
        sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list \
    && sudo apt-get update
    sudo apt install -y nvidia-container-runtime
    sudo ln -s /sbin/ldconfig /sbin/ldconfig.real
    sudo reboot

# On the Kubernetes control spot
    helm repo add nvidia https://helm.ngc.nvidia.com/nvidia
    helm repo update
    helm install gpu-operator -n gpu-operator --create-namespace nvidia/gpu-operator --values ./nvidia/values.yaml

# Optional, lets you assign the same GPU to multiple pods
    kubectl apply -f nvidia/time-slicing-config-all.yaml
    kubectl patch clusterpolicy/cluster-policy -n gpu-operator --type merge -p '{"spec": {"devicePlugin": {"config": {"name": "time-slicing-config-all", "default": "any"}}}}'