# Rocketchat - Matrix bridge tester

**Note:** this setup is omptimized for a quick setup, not security in any capacity. Please don't use this in production.

Docker-compose largely based on [ruanbekkers' blog post](https://blog.ruanbekker.com/blog/2022/03/29/setup-matrix-and-element-chat-server/)

Also, note that as of writing Rocket.chat & Synapse/Matrix/Element work individually, but not the bridge.
```bash
# Create synapse config file
docker run -it --rm -v "${PWD}/synapse:/data" -e SYNAPSE_SERVER_NAME=example.org -e SYNAPSE_REPORT_STATS=yes matrixdotorg/synapse:latest generate
# Start services
docker-compose up --detach
# Create a user for Synapse
docker-compose exec synapse bash
register_new_matrix_user -c /data/homeserver.yaml http://localhost:8008
```

- Follow the tutorial [here](https://docs.rocket.chat/guides/administration/admin-panel/settings/federation/matrix-bridge/matrix-admin-guide/matrixbridge-configuration) to create the *registration file*.
- Add the registration file contents into ./synapse/app_service_1.yaml
- Add the following to synapse/homeserver.yaml
    ```yml
    app_service_config_files:
      - /data/app_service_1.yaml
    ```
- Run `docker-compose restart`

You can access Elements on http://localhost:80. Set the homeserver to `http://localhost:8008`.
You can access rocketchat on http://localhost:3000.

For my own findings, please see [Denperidge-Redpencil/Learning.md/things-to-check-out#update-findings](https://github.com/Denperidge-Redpencil/Learning.md/blob/main/Notes/things-to-check-out.md#update-findings).

## Notes that have to be properly integrated
- Bridge URL: expose 3300
- Create user with bot role (rocketchat)


