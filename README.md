# Rocketchat - Matrix bridge tester

**Note:** this setup is omptimized for a quick setup, not security in any capacity. Please don't use this in production.

Docker-compose largely based on [ruanbekkers' blog post](https://blog.ruanbekker.com/blog/2022/03/29/setup-matrix-and-element-chat-server/)

```bash
# Create synapse config file
docker run -it --rm -v "${PWD}/synapse:/data" -e SYNAPSE_SERVER_NAME=example.org -e SYNAPSE_REPORT_STATS=yes matrixdotorg/synapse:latest generate
# Start services
docker-compose up --detach
# Create a user for Synapse
docker-compose exec -it synapse bash
register_new_matrix_user -c /data/homeserver.yaml http://localhost:8008
```

You can access Elements on http://localhost:80. Set the homeserver to `http://localhost:8008`.
You can access rocketchat on http://localhost:3000