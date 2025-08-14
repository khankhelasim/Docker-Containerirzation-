# Steps to Set Up Multi-Network Containers

## 1. Create custom networks
- Create a network for frontend communication (`frontend-network`)
- Create a network for backend communication (`backend-network`)

## 2. Run backend container
- Launch MySQL container attached only to the backend network
- Set a root password and database name

## 3. Run frontend container
- Launch Nginx container attached only to the frontend network

## 4. Run application container
- Attach the application container to both `frontend-network` and `backend-network`
- This allows the app container to talk to both MySQL and Nginx

## 5. Test connectivity
- From the app container, ping frontend container by name
- From the app container, ping backend container by name
- Try accessing MySQL from the app container

## 6. Cleanup
- Stop and remove containers
- Remove custom networks
=============================================================

**Task 1: Create Custom Bridge Network
1.1 — Check Default Networks**

docker network ls

**1.2 — Create First Custom Bridge Network**

docker network create --driver bridge webapp-network

docker network ls

**1.3 — Create Custom Network with Specific Subnet**

docker network create \
  --driver bridge \
  --subnet=172.20.0.0/16 \
  --ip-range=172.20.240.0/20 \
  --gateway=172.20.0.1 \
  backend-network


**1.4 — Create Multiple Networks**

docker network create frontend-network

docker network create database-network

docker network ls

**Task 2: Launch Containers Within Custom Networks**

**2.1 — Deploy in webapp-network**

docker run -d --name web-server --network webapp-network nginx:alpine

docker run -d --name web-client --network webapp-network alpine:latest sleep 3600


**2.2 — Multi-Container Setup**

docker run -d --name mysql-db \
  --network backend-network \
  -e MYSQL_ROOT_PASSWORD=rootpass \
  -e MYSQL_DATABASE=webapp \
  mysql:8.0

docker run -d --name app-server \
  --network backend-network \
  nginx:alpine

docker network connect frontend-network app-server


**2.3 — Verify Network Assignments**

docker ps

docker inspect web-server | grep -A 10 "Networks"

**Task 3: Verify Connectivity**

**3.1 — Same Network Communication**

docker exec -it web-client sh
ping web-server
wget -qO- http://web-server
exit


**3.2 — Cross-Network Isolation Test**

docker run -d --name frontend-app --network frontend-network alpine:latest sleep 3600

docker exec frontend-app ping mysql-db


**3.3 — Multi-Network Communication**

docker exec app-server ping mysql-db

docker run --rm --network frontend-network alpine:latest ping -c 3 app-server

**Task 4: Inspect Networks**

**4.1 — Inspect Custom Network**

docker network inspect webapp-network


**4.2 — Filtered Info**

docker network inspect webapp-network --format='{{.IPAM.Config}}'

docker network inspect webapp-network --format='{{range .Containers}}{{.Name}} {{.IPv4Address}}{{end}}'


**4.3 — Compare Network Types**

docker network inspect bridge

docker network inspect backend-network

echo "Default bridge network:"

docker network inspect bridge --format='{{.IPAM.Config}}'

echo "Custom backend network:"

docker network inspect backend-network --format='{{.IPAM.Config}}'

**Task 5: Host Networking**

**5.1 — Create Host Network Container**

docker run -d --name host-nginx --network host nginx:alpine


**5.2 — Compare**

docker inspect host-nginx | grep -A 5 "NetworkMode"

docker run --rm --network host -p 8080:80 nginx:alpine &

curl localhost:8080

docker stop $(docker ps -q --filter ancestor=nginx:alpine)


**5.3 — Use Cases**

docker run --rm --network host alpine:latest ip addr show

docker run --rm --network bridge alpine:latest ip addr show

**Task 6: Troubleshoot Networking**

**6.1 — No Network**

docker run -d --name isolated-app --network none alpine:latest sleep 3600

docker exec isolated-app ping 8.8.8.8


**6.2 — Disconnect**

docker network disconnect frontend-network app-server

docker network inspect frontend-network --format='{{range .Containers}}{{.Name}}{{end}}'

docker run --rm --network frontend-network alpine:latest ping -c 2 app-server


**6.3 — Reconnect**

docker network connect frontend-network app-server

docker network inspect frontend-network --format='{{range .Containers}}{{.Name}} {{.IPv4Address}}{{end}}'

docker run --rm --network frontend-network alpine:latest ping -c 2 app-server


**6.4 — Advanced Checks**

docker exec web-server ip route

docker exec web-server ip addr show

docker exec web-server nslookup web-client

sudo iptables -t nat -L DOCKER

**Task 7: Cleanup**

**7.1 — Remove Containers**

docker stop $(docker ps -q)

docker rm $(docker ps -aq)


**7.2 — Remove Networks**

docker network rm webapp-network backend-network frontend-network database-network

docker network ls


**7.3 — Prune Networks**

docker network prune -f

docker network ls
