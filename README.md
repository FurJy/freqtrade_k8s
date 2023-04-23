# freqtrade_k8s
TESTED ON UBUNTU 22.04.2 LTS

---SETUP---
1) Install k8s
sudo snap install microk8s --classic
sudo usermod -a -G microk8s $USER
sudo chown -f -R $USER ~/.kube
su - $USER

2) Install docker
  apt update
  apt install docker.io docker-compose

3) Prepare a default freqtrade container
(by that we make a sample of user_data for next pods (containers) and pull freqtrade image for k8s to use)
  mkdir /etc/freqtrade
  cd /etc/freqtrade
  curl https://raw.githubusercontent.com/freqtrade/freqtrade/stable/docker-compose.yml -o docker-compose.yml
  docker-compose pull
  docker-compose run --rm freqtrade create-userdir --userdir user_data
  docker-compose run --rm freqtrade new-config --config user_data/config.json

4) Run a k8s pod
Copy freqtrade-deployment.yaml and freqtrade-service.yaml into /etc/freqtrade
sudo microk8s kubectl apply -f freqtrade-service.yaml,freqtrade-deployment.yaml
---PROFIT---

---HOW IT WORKS---
1) k8s yaml files are based on default freqtrade docker-compose.yaml file; so it works almost the same way
2) we copy user_data folder and select it in freqtrade-deployment.yaml as a volume (hostPath)
SELECTING ONLY CONFIG.JSON DOESN'T WORK, FREQTRADE REQUIRES SOME MORE FILES; SO WE COPY A FOLDER
3) kubernetes uses a NodePort to expose an application port. NodePort ports pool starts from 30000.


