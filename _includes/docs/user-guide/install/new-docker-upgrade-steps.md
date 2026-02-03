{% assign current_version = include.version %}
{% assign previous_version = include.previous_version %}

{% capture update_note %}
**NOTE:**
<br>
If you are upgrading from {{ previous_version }}, execution of the migration script is required.
[Versioning and Release Policy](/docs/{{ docsPrefix }}releases/release-policy/#thingsboard-versioning)
{% endcapture %}


1. Change the version of the `thingsboard/tb-node` in the `docker-compose.yml` file to the **{{ current_version }}**.

2. Execute the following commands:
    
    ```bash
    docker pull thingsboard/tb-node:{{ current_version }}
    docker compose stop thingsboard-ce
    ```
   
   {% include templates/warn-banner.md content=update_note %}
    ```bash
    docker compose run --rm -e UPGRADE_TB=true thingsboard-ce 
    ```
   
    ```bash
    docker compose up -d
    ```   