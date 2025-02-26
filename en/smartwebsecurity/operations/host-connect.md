---
title: "How to connect a security profile in {{ sws-full-name }} to a virtual host"
description: "Follow this guide to connect a security profile in {{ sws-full-name }} to a virtual host."
---

# Connecting a security profile to a virtual host

{% list tabs group=instructions %}

- Management console {#console}

   1. In the [management console]({{ link-console-main }}), choose the folder where you want to connect a [security profile](../concepts/profiles.md) to an [{{ alb-full-name }}](../../application-load-balancer/) [virtual host](../../application-load-balancer/concepts/http-router.md#virtual-host).
   1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_smartwebsecurity }}**.
   1. Select the profile to connect to the host.
   1. Click ![plug](../../_assets/console-icons/plug-connection.svg) **{{ ui-key.yacloud.smart-web-security.overview.action_attach-to-host }}**.
   1. In the window that opens, select:
      * [**{{ ui-key.yacloud.smart-web-security.attach-dialog.label_balancer }}**](../../application-load-balancer/concepts/application-load-balancer.md).
      * [**{{ ui-key.yacloud.smart-web-security.attach-dialog.label_http-router }}**](../../application-load-balancer/concepts/http-router.md).
      * **{{ ui-key.yacloud.smart-web-security.attach-dialog.label_virtual-host }}**. You can connect the security profile to multiple virtual hosts at once.

         To connect the profile to another L7 load balancer, click **{{ ui-key.yacloud.smart-web-security.attach-dialog.action_add-balancer }}**.
   1. Click **{{ ui-key.yacloud.smart-web-security.attach-dialog.action_connect }}**. If the selected hosts are already connected to another security profile, confirm the connection.

      In the **{{ ui-key.yacloud.smart-web-security.overview.title_connected-to-the-hosts }}** tab, you will see the connected virtual hosts.

- CLI {#cli}

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   1. To view a list of current security profiles in the default folder, run this command:

      {% include [security-profile-list-command](../../_includes/smartwebsecurity/security-profile-list-command.md) %}

   1. To view a list of [HTTP routers](../../application-load-balancer/concepts/http-router.md) in the default folder, run this command:

      ```bash
      yc application-load-balancer http-router list
      ```

      Result:

      ```text
      +----------------------+-------------------+-------------+-------------+
      |          ID          |       NAME        | VHOST COUNT | ROUTE COUNT |
      +----------------------+-------------------+-------------+-------------+
      | ds7e9te73uak******** |  my-first-router  |           1 |           1 |
      +----------------------+-------------------+-------------+-------------+
      ```

   1. To view a list of [virtual hosts](../../application-load-balancer/concepts/http-router.md#virtual-host) for the selected HTTP router, run this command:

      ```bash
      yc application-load-balancer http-router get <HTTP_router_name_or_ID>
      ```

      Result:

      ```yaml
      id: ds7e9te73uak********
      name: my-first-router
      folder_id: b1gt6g8ht345********
      virtual_hosts:
        - name: test-virtual-host
          routes:
            - name: test-route
              http:
                match:
                  path:
                    prefix_match: /
                route:
                  backend_group_id: ds7a4niks9qv********
                  timeout: 60s
                  auto_host_rewrite: false
          route_options: {}
      created_at: "2024-08-05T08:34:03.973000654Z"
      ```

      Virtual host names are specified in the `virtual_hosts.name` parameter. The example above features only one virtual host: `test-virtual-host`.

   1. To connect a [security profile](../concepts/profiles.md) to a virtual host, run this command:

      ```bash
      yc application-load-balancer virtual-host update <virtual_host_name> \
         --http-router-name <HTTP_router_name> \
         --security-profile-id <security_profile_ID>
      ```

      Where:

      * `<virtual_host_name>`: Virtual host name from the previous step.
      * `--http-router-name`: HTTP router name. This is a required parameter. Instead of the HTTP router name, you can provide its ID in the `--http-router-id` parameter.
      * `--security-profile-id`: Security profile ID. This is a required parameter.

      Result:

      ```yaml
      done (1s)
      name: test-virtual-host
      routes:
        - name: test-route
          http:
            match:
              path:
                prefix_match: /
            route:
              backend_group_id: ds7a4niks9qv********
              timeout: 60s
              auto_host_rewrite: false
      route_options:
        security_profile_id: fev3s055oq64********
      ```

   For more information about the `yc application-load-balancer virtual-host update` command, see the [CLI reference](../../cli/cli-ref/managed-services/application-load-balancer/virtual-host/update.md).

- API {#api}

   {% include [api-host](../../_includes/smartwebsecurity/api-host.md) %}

{% endlist %}

{% include [auto-scaling-tip](../../_includes/smartwebsecurity/auto-scaling-tip.md) %}

### See also {#see-also}

* [{#T}](host-delete.md)
