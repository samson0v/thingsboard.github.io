{% assign current_version = include.version %}
{% assign previous_version = include.previous_version %}

{% capture update_note %}
If you are upgrading from {{ previous_version }}, execution of the migration script is [required](/docs/{{ docsPrefix }}releases/release-policy/#thingsboard-versioning):
{% endcapture %}

1. Change the version of the `thingsboard/tb-pe-node` and `thingsboard/tb-pe-web-report` in the `docker-compose.yml` file to the **{{ current_version }}**.

2. Execute the following commands:

  ```bash
  docker pull thingsboard/tb-pe-node:{{ current_version }}
  docker pull thingsboard/tb-pe-web-report:{{ current_version }}
  docker compose stop thingsboard-pe
  ```
  {: .copy-code}

  {% include templates/warn-banner.md content=update_note %}

  ```bash
  docker compose run --rm -e UPGRADE_TB=true thingsboard-pe
  ```
  {: .copy-code}

  ```bash
  docker compose up -d
  ```
  {: .copy-code}