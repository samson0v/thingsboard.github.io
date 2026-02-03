{% assign current_version = include.version %}
{% assign previous_version = include.previous_version %}

{% capture update_note %}
**NOTE:**
<br>
If you are upgrading from {{ previous_version }}, execution of the migration script is required.
[Versioning and Release Policy](/docs/{{ docsPrefix }}releases/release-policy/#thingsboard-versioning)
{% endcapture %}

1. Change the parameter `TB_VERSION` in the `.env` file.

  ```.env
  TB_VERSION={{ current_version }}
  ```

2. Execute the following commands:
    
    ```bash
    ./docker-stop-services.sh  
    ```
   
   {% include templates/warn-banner.md content=update_note %}
    ```bash
    ./docker-upgrade-tb.sh
    ```
   
    ```bash
    ./docker-start-services.sh
    ```

{: .copy-code}