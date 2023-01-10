# Rocketchat - Matrix bridge tester

**Note:** this setup is omptimized for a quick setup, not security in any capacity. Please don't use this in production.

- This repo is meant to test out bridging Rocketchat with Matrix through two different methods
  - The [official bridge](https://docs.rocket.chat/guides/administration/admin-panel/settings/federation/matrix-bridge) (currently not functional)
  - The third party [matterbridge](https://github.com/42wim/matterbridge) (currently functional)
- Docker-compose largely based on [ruanbekkers' blog post](https://blog.ruanbekker.com/blog/2022/03/29/setup-matrix-and-element-chat-server/)

## General setup
This setup is needed for 
```bash
# Create synapse config file
docker run -it --rm -v "${PWD}/synapse:/data" -e SYNAPSE_SERVER_NAME=example.org -e SYNAPSE_REPORT_STATS=yes matrixdotorg/synapse:latest generate
# Start services
docker-compose up --detach
# Create a user for Synapse
docker-compose exec synapse bash
register_new_matrix_user -c /data/homeserver.yaml http://localhost:8008
```

You can access Elements on http://localhost:80. Set the homeserver to `http://localhost:8008`.
You can access rocketchat on http://localhost:3000.

## Official bridge

- Follow the tutorial [here](https://docs.rocket.chat/guides/administration/admin-panel/settings/federation/matrix-bridge/matrix-admin-guide/matrixbridge-configuration) to create the *registration file*.
- Add the registration file contents into ./synapse/app_service_1.yaml
- Add the following to synapse/homeserver.yaml
    ```yml
    app_service_config_files:
      - /data/app_service_1.yaml
    ```
- Run `docker-compose restart`
- Bridge URL: expose 3300

## Matterbridge
- Create *dedicated* users to relay messages
  - Rocketchat: Create a user through the rocketchat GUI
    - Ensure the user has the bot role
    - Make note of the email you used & the password
  - Matrix: Create a user through the following command, make note of the username & password
    ```
    docker-compose exec synapse bash
    register_new_matrix_user -c /data/homeserver.yaml http://localhost:8008
    ```
- Modify the matterbridge.toml (see also the [Wiki](https://github.com/42wim/matterbridge/wiki/) for additional information)
  - `[rocketchat.myrocketchat]`: Change Login & Password to the email & password
  - `[matrix.mymatrix]`: Change Login & Password to the name & password
  - `[[gateway]]`: You can create as many as wanted!
    - Change the gateway name to be unique
    - Change the matrix channel to `#roomaddress:matrixdomain.tld`
      - You can find this by navigating to the room through Element and checking your URL. Example: http://localhost/#/room/**#supports:example.org**.
      - Room address is *not* necessarily the same as the room name, these are seperate things).
    - Change the rocketchat channel to `#roomname`
    - You can change `inout` to `in` or `out` for one way communication 


Note: when changing the URLs in matterbridge.toml: use the docker service names, NOT localhost.

For my own findings, please see [Denperidge-Redpencil/Learning.md/things-to-check-out#update-findings](https://github.com/Denperidge-Redpencil/Learning.md/blob/main/Notes/things-to-check-out.md#update-findings).