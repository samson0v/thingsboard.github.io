{% assign current_version = include.version %}
{% assign previous_version = include.previous_version %}

{% capture update_note %}
If you are upgrading from {{ previous_version }}, execution of the migration script is [required](/docs/{{ docsPrefix }}releases/release-policy/#thingsboard-versioning):
{% endcapture %}

1. Change the parameter `TB_VERSION` in the `.env` file.

  ```.env
  TB_VERSION={{ current_version }}
  ```

2. Execute the following commands:
    
 ```bash
 ./docker-stop-services.sh  
 ```
 {: .copy-code}  

 {% include templates/warn-banner.md content=update_note %}

 ```bash
 ./docker-upgrade-tb.sh
 ```
 {: .copy-code}  

 ```bash
 ./docker-start-services.sh
 ```
 {: .copy-code}