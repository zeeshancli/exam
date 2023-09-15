DOCKER COMPOSE YAML
version: '3'
services:
  frontend:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./frontend:/usr/share/nginx/html
    networks:
      - mynetwork
  backend:
    image: node:14
    working_dir: /app
    volumes:
      - ./backend:/app
    environment:
      - NODE_ENV=production
    networks:
      - mynetwork

networks:
  mynetwork:
  docker-compose up -d
docker-compose down


  
DOCKER SWARM YAML
version: '3'
services:
  web:
    image: nginx:latest
    deploy:
      replicas: 3
    ports:
      - "80:80"
    networks:
      - mynetwork
  app:
    image: myapp:latest
    deploy:
      replicas: 2
    networks:
      - mynetwork
  db:
    image: postgres:latest
    deploy:
      replicas: 1
    environment:
      POSTGRES_PASSWORD: mysecretpassword
    networks:
      - mynetwork

networks:
  mynetwork:
docker stack deploy -c docker-compose.yml myapp

STATEFULSET YAML
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: my-stateful-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-stateful-app
  serviceName: "my-stateful-service"
  template:
    metadata:
      labels:
        app: my-stateful-app
    spec:
      containers:
      - name: my-app-container
        image: my-app-image:latest
        ports:
        - containerPort: 80
        volumeMounts:
        - name: data
          mountPath: /data
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "standard"
      resources:
        requests:
          storage: 10Gi
