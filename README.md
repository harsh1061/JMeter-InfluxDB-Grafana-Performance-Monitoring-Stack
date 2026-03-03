# JmeterResultsSetup
jmeter-docker(InfluxDB + Grafana)

============================================================
JMETER-INFLUXDB-GRAFANA DOCKER COMMANDS
============================================================

--- 1. STARTING THE STACK ---
# Start all services in the background
docker-compose up -d

# Force a restart of a specific service (e.g., after editing JSON)
docker-compose restart grafana

--- 2. STOPPING & CLEANING ---
# Stop containers but keep data
docker-compose down

# Stop containers and DELETE ALL DATA (Volumes)
# Use this for a total fresh start if mappings fail
docker-compose down -v

--- 3. TROUBLESHOOTING & LOGS ---
# View live logs for InfluxDB (Check for JMeter connection errors)
docker-compose logs -f influxdb

# View live logs for Grafana (Check for provisioning/JSON errors)
docker-compose logs -f grafana

# Check if containers are running and see ports
docker-compose ps

--- 4. DATA VERIFICATION (INFLUXDB) ---
# List all buckets to ensure 'jmeter_results' exists
docker exec -it influxdb influx bucket list

# (If using InfluxQL) Check for V1 Database Mapping
docker exec -it influxdb influx v1 dbrp list

--- 5. FILE SYSTEM CHECKS ---
# Check if Grafana can see your provisioned dashboards inside the container
docker exec -it grafana ls /etc/grafana/provisioning/dashboards/
docker exec -it grafana ls /var/lib/grafana/dashboards/

--- 6. DOCKER SYSTEM MAINTENANCE ---
# Remove unused containers, networks, and images to save space
docker system prune -f
