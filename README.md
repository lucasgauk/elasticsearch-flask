# MONITORING FLASK DOCKER CONTAINERS
## Elasticsearch
1. Follow [these](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html) instructions to install Elasticsearch.
2. Go to `/etc/elasticsearch/` and edit the elasticsearch.yml. Replace any instances of `localhost` with `0.0.0.0`.
3. Follow [these](https://www.elastic.co/guide/en/kibana/current/deb.html) instructions to install Kibana.
4. Like before, go to `/etc/kibana/` and edit the kibana.yml file. Replace all instances of `localhost` with `0.0.0.0`. 
5. If kibana is installed, you can go to `<your IP>:5601/app/apm` to get instructions on how to install apm-server. 
6. Again, go to `/etc/apm-server/` and edit the apm-server.yml file. Replace all instances of `localhost` with `0.0.0.0`.
7. Enable apm kibana dashboards with `apm-server setup --dashboards`.
8. Use the tools at `<your IP>:5601/app/apm` to verify that the apm server is correctly installed. 
9. Use systemctl to restart all three services (elasticsearch.service, kibana.service, apm-server.service).

## Demo Web App
1. Clone this repository. 
2. Edit the python app to point to the correct apm-server instance.
3. Run the following commands to build and publish the webapp.

```
sudo docker build --tag=elasticwebapp .
sudo docker tag elasticwebapp <docker username>/elasticwebapp:v1
sudo docker push <docker username>/elasticwebapp:v1
```

4. Edit the docker-compose.yml file to locate the right webapp to run. Run the following to start the webapp, Redis, and a simple
visualizer to view your docker containers.

```
sudo docker stack deploy <stack name> --compose-file <path to docker compose.yml>
```

5. Verify that Redis, your Flask webapp, and the visualizer are all running properly.
6. Open `<your IP>:5601/app/apm` again and use the built in tools to verify that your Flask app is sending information to the apm-server.

## Metricbeat
1. Run the following command to install Metricbeat.

```
sudo apt-get install metricbeat
```

2. Navigate to `/etc/metricbeat/` and edit the metricbeat.yml file. In the kibana section, edit the host to match the IP of your kibana instance. In the outputs section, edit the outputs.elasticsearch hosts to point to your elasticsearch IP. Restart the metricbeat.service.
3. Navigate to `/etc/metricbeat/modules.d` and run `cp docker.yml.disabled docker.yml` to enable docker exporting. 
4. Restart the metricbeat service with `sudo systemctl restart metricbeat.service`.
5. Enable dasbhoards for kibana with `metricbeat setup --dashboards`.
6. Verify that your Kibana instance is receiving information from Metricbeat.
7. Note that any other instances with metricbeat installed will automatically be added to the same dashboard!

You can now monitor the request volume and response time of your Flask app, along with basic information about the docker instance that's running it. 


