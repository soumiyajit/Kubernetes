## Kubernetes Basic Installation

**- Considerations:**

- Installation guide for Ubuntu18.0 
- Container Runtime used is docker
- CNI used is calico
- HW required is minimum 6GB RAM and 2 cores


**1. Container runtime installation:**

	

	sudo apt-get update && sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common gnupg2

	curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key --keyring /etc/apt/trusted.gpg.d/docker.gpg add -

	sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

	sudo apt-get update && sudo apt-get install -y containerd.io=1.2.13-2  docker-ce=5:19.03.11~3-0~ubuntu-$(lsb_release -cs) docker-ce-cli=5:19.03.11~3-0~ubuntu-$(lsb_release -cs)
	
	sudo mkdir /etc/docker



	cat <<EOF | sudo tee /etc/docker/daemon.json
	
	{ 
	  "exec-opts": ["native.cgroupdriver=systemd"], 
	  "log-driver": "json-file", 
	  "log-opts": { 
	     "max-size": "100m" 
	  },
	
	  "storage-driver": "overlay2"
	
	} 
	EOF


	sudo mkdir -p /etc/systemd/system/docker.service.d

	sudo systemctl daemon-reload

	sudo systemctl restart docker

	sudo systemctl enable docker


**2. Installation of kubeadm, kubelet and kubectl**



	sudo apt-get update && sudo apt-get install -y apt-transport-https curl

	curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -


	cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
	
	deb https://apt.kubernetes.io/ kubernetes-xenial main
	
	EOF
	

	sudo apt-get update
	
	sudo apt-get install -y kubelet kubeadm kubectl
	
	sudo apt-mark hold kubelet kubeadm kubectl

**Note:Step 1 and Step2 to be done in all the control plane and worker nodes**

**3. Create the control plane:**

	kubeadm init

	mkdir -p $HOME/.kube
	
	sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
	
	sudo chown $(id -u):$(id -g) $HOME/.kube/config

	kubectl apply -f https://docs.projectcalico.org/v3.11/manifests/calico.yaml
	

**4. For adding worker. Nodes:**

	kubeadm join --token <token> <control-plane-host>:<control-plane-port> --discovery-token-ca-cert-hash sha256:<hash>
	
	
	
###Refer links:

- <a href="https://kubernetes.io/docs/setup/production-environment/container-runtimes/"  target="_top">https://kubernetes.io/docs/setup/production-environment/container-runtimes/</a>

- <a href="https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/" target="_top">https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/</a>

- <a href="https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/" target="_top">https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/</a>














