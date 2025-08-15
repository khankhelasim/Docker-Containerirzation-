# Docker Swarm Secrets Management - Step-by-Step

## Task 1: Initialize Docker Swarm

```bash

docker swarm init

docker node ls

**Task 2: Create Basic Secrets**

echo "mySecretPassword123" | docker secret create db_password -

echo "admin_user" | docker secret create db_username -

docker secret ls

**Task 3: Inspect Secret**

docker secret inspect db_password

docker secret inspect db_password --pretty

**Task 4: Deploy MySQL with Secrets**

docker service create \

  --name mysql_db \

  --secret db_username \

  --secret db_password \

  --env MYSQL_ROOT_PASSWORD_FILE=/run/secrets/db_password \

  --env MYSQL_USER_FILE=/run/secrets/db_username \

  --env MYSQL_PASSWORD_FILE=/run/secrets/db_password \

  --env MYSQL_DATABASE=testdb \

  --publish 3306:3306 \

  mysql:8.0

docker service ls

docker service ps mysql_db

**Task 5: View Secrets in Container**

CONTAINER_ID=$(docker ps --filter "name=mysql_db" --format "{{.ID}}")

docker exec $CONTAINER_ID ls -la /run/secrets/

**Task 6: Create Secrets from Files**

mkdir -p ~/lab_secrets

cat > ~/lab_secrets/db_config.json << EOF
{
  "host": "mysql_db",
  "port": 3306,
  "database": "testdb",
  "ssl_mode": "required",
  "connection_timeout": 30
}
EOF

echo "api_key_abc123xyz789" > ~/lab_secrets/api_key.txt

docker secret create db_config ~/lab_secrets/db_config.json

docker secret create api_key ~/lab_secrets/api_key.txt

Task 7: Deploy Web App with Multiple Secrets

docker service create \

  --name web_app \

  --secret db_username \

  --secret db_password \

  --secret db_config \

  --secret api_key \

  --env DB_USERNAME_FILE=/run/secrets/db_username \

  --env DB_PASSWORD_FILE=/run/secrets/db_password \

  --env DB_CONFIG_FILE=/run/secrets/db_config \

  --env API_KEY_FILE=/run/secrets/api_key \

  --publish 8080:80 \

  nginx:alpine

**Task 8: Scale Service**

docker service scale web_app=3

docker service ps web_app

**Task 9: Update Secrets**

echo "api_key_new_version_456" | docker secret create api_key_v2 -

docker service update \

  --secret-rm api_key \

  --secret-add api_key_v2 \

  web_app

**Task 10: Cleanup**

docker service rm web_app custom_app mysql_db

sleep 10

docker secret rm db_password api_key

docker secret ls
