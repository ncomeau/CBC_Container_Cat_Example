#!/usr/bin/dash -e

echo 'Altering ~/.vimrc editing controls'
echo 'set nocompatible' >> ~/.vimrc
echo 'set backspace=indent,eol,start' >> ~/.vimrc
echo '----------------------------------------------------------'

echo 'Adding kubectl alias to .bashrc...'
echo 'alias kubectl=microk8s.kubectl' >> ~/.bashrc
echo 'source <(microk8s.kubectl completion bash)' >> ~/.bashrc
echo '----------------------------------------------------------'

echo 'Installing docker.io and git...'
apt-get -y install docker.io git
echo '----------------------------------------------------------'

echo 'Get cat gif web app...'
git clone https://github.com/prakhar1989/docker-curriculum.git
echo '----------------------------------------------------------'

echo 'Creating Docker file...'
cat << EOL > docker-curriculum/flask-app/Dockerfile
FROM python:3

# set a directory for the app
WORKDIR /usr/src/app

# copy all the files to the container
COPY . .

# install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# define the port number the container should expose
EXPOSE 5000

# run the command
CMD ["python", "./app.py"]
EOL
echo '----------------------------------------------------------'

echo 'Restarting microk8s services...'
systemctl restart snap.microk8s.daemon-kubelet.service
systemctl restart snap.microk8s.daemon-controller-manager.service
systemctl restart snap.microk8s.daemon-control-plane-kicker.service

echo '----------------------------------------------------------'
echo 'Starting Docker...'
systemctl start docker

echo '----------------------------------------------------------'
echo 'building Docker image...'
docker build -t k8-se-demo/catnip docker-curriculum/flask-app/

echo '----------------------------------------------------------'
echo 'Running the Docker image...'
docker run -p 8888:5000 k8-se-demo/catnip &

echo '----------------------------------------------------------'
echo 'Enabling the k8 registry...'
microk8s enable registry
echo -n ' Waiting for the registry to be enabled'
while [ -z $(microk8s.status -a registry | grep enabled) ]; do echo -n '.'; sleep 1; done
echo

echo '----------------------------------------------------------'
echo 'Tagging the Docker image for the k8 registry...'
docker images | grep "^k8-se-demo/catnip" | awk '{ system("docker tag " $3 " localhost:32000/" $1 ":registry") }'

echo '----------------------------------------------------------'
echo 'Pushing the Docker images...'
docker push localhost:32000/k8-se-demo/catnip; while [ $? != 0 ]; do sleep 3; docker push localhost:32000/k8-se-demo/catnip; done

echo '----------------------------------------------------------'
echo 'Restarting Docker...'
systemctl restart docker

echo '----------------------------------------------------------'
echo 'Creating dir for demo yamls in current dir, called cat_demo'
mkdir cat-demo


echo '----------------------------------------------------------'
echo 'Creating bad-cat-deployment.yaml...'
cat << EOL > cat-demo/bad-cat-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bad-cat-deployment
  labels:
    app: cat
spec:
  selector:
    matchLabels:
      app: cat
  replicas: 2
  template:
    metadata:
      labels:
        app: cat
    spec:
      containers:
      - name: cat
        image: localhost:32000/k8-se-demo/catnip:registry
        ports:
        - containerPort: 5000
---
# https://kubernetes.io/docs/concepts/services-networking/service/#defining-a-service
kind: Service
apiVersion: v1
metadata:
  name: cat-service
spec:
  selector:
    app: cat
  ports:
  - protocol: TCP
    port: 8888
    targetPort: 5000
    nodePort: 31000
  type: NodePort
EOL

echo '----------------------------------------------------------'
echo 'Creating good-cat-deployment.yaml...'
cat << EOL > cat_demo/good-cat-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: good-cat-deployment
  labels:
    app: cat
spec:
  selector:
    matchLabels:
      app: cat
  replicas: 2
  template:
    metadata:
      labels:
        app: cat
    spec:
      containers:
      - name: cat
        image: localhost:32000/k8-se-demo/catnip:registry
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
        ports:
        - containerPort: 5000
---
# https://kubernetes.io/docs/concepts/services-networking/service/#defining-a-service
kind: Service
apiVersion: v1
metadata:
  name: cat-service
spec:
  selector:
    app: cat
  ports:
  - protocol: TCP
    port: 8888
    targetPort: 5000
    nodePort: 31000
  type: NodePort
EOL

echo '----------------------------------------------------------'
echo 'Creating best-cat-deployment.yaml...'
cat << EOL > cat_demo/best-cat-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: best-cat-deployment
  labels:
    app: cat
spec:
  selector:
    matchLabels:
      app: cat
  replicas: 2
  template:
    metadata:
      labels:
        app: cat
    spec:
      containers:
      - name: cat
        image: localhost:32000/k8-se-demo/catnip:registry
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
        ports:
        - containerPort: 5000
---
# https://kubernetes.io/docs/concepts/services-networking/service/#defining-a-service
kind: Service
apiVersion: v1
metadata:
  name: best-cat-service
spec:
  selector:
    app: cat
  ports:
  - protocol: TCP
    port: 8888
    targetPort: 5000

EOL


echo '----------------------------------------------------------'
echo 'Completed creation of all needed demo yamls'

echo '----------------------------------------------------------'
echo 'Creating feline namespace for testing'
microk8s.kubectl create namespace feline

echo '----------------------------------------------------------'
echo 'Populating namespace with dummy workload, for policy creation'
microk8s.kubectl apply -f https://raw.githubusercontent.com/octarinesec/evaluation-assets/master/guardrail/dummy-workload.yaml -n feline

echo '----------------------------------------------------------'
echo 'Creating deletion script, for ease in demo replication'

cat << EOL > cat_demo/demo_cleanup.sh
microk8s.kubectl delete deployments -n feline --all
microk8s.kubectl delete service -n feline --all
EOL

chmod ugo+wrx cat_demo/demo_cleanup.sh

echo 'Reference demo documentation for how and when to run said script'


echo '----------------------------------------------------------'
echo 'Now run the following command, and see demo doc for next steps:'
echo 'source  ~/.bashrc'




