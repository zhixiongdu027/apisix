---
title: ext-plugin-post-resp
keywords:
  - APISIX
  - Plugin
  - ext-plugin-post-resp
description: This document contains information about the Apache APISIX ext-plugin-post-resp Plugin.
---

<!--
#
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
-->

## Description

The `ext-plugin-post-resp` Plugin is for running specific external Plugins in the Plugin Runner before executing the built-in Lua Plugins.

The `ext-plugin-post-resp` plugin will be executed after the request gets a response from the upstream.

After enabling this plugin, APISIX will use the [lua-resty-http](https://github.com/api7/lua-resty-http) library to make requests to the upstream, this results in:

- [proxy-control](./proxy-control.md) plugin is not available
- [proxy-mirror](./proxy-mirror.md) plugin is not available
- [proxy-cache](./proxy-cache.md) plugin is not available
- [mTLS Between APISIX and Upstream](../mtls.md#mtls-between-apisix-and-upstream) function is not available yet

See [External Plugin](../external-plugin.md) to learn more.

:::note

Execution of External Plugins will affect the response of the current request.

External Plugin does not yet support getting request context information.

External Plugin does not yet support getting the response body of an upstream response.

:::

## Attributes

| Name              | Type    | Required | Default | Valid values                                                    | Description                                                                                                            |
|-------------------|---------|----------|---------|-----------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| conf              | array   | False    |         | [{"name": "ext-plugin-A", "value": "{\"enable\":\"feature\"}"}] | List of Plugins and their configurations to be executed on the Plugin Runner.                                          |
| allow_degradation | boolean | False    | false   |                                                                 | Sets Plugin degradation when the Plugin Runner is not available. When set to `true`, requests are allowed to continue. |

## Enabling the Plugin

The example below enables the `ext-plugin-post-resp` Plugin on a specific Route:

```shell
curl -i http://127.0.0.1:9180/apisix/admin/routes/1  -H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT -d '
{
    "uri": "/index.html",
    "plugins": {
        "ext-plugin-post-resp": {
            "conf" : [
                {"name": "ext-plugin-A", "value": "{\"enable\":\"feature\"}"}
            ]
    },
    "upstream": {
        "type": "roundrobin",
        "nodes": {
            "127.0.0.1:1980": 1
        }
    }
}'
```

## Example usage

Once you have configured the External Plugin as shown above, you can make a request to execute the Plugin:

```shell
curl -i http://127.0.0.1:9080/index.html
```

This will reach the configured Plugin Runner and the `ext-plugin-A` will be executed.

## Disable Plugin

To disable the `ext-plugin-post-resp` Plugin, you can delete the corresponding JSON configuration from the Plugin configuration. APISIX will automatically reload and you do not have to restart for this to take effect.

```shell
curl http://127.0.0.1:9180/apisix/admin/routes/1  -H 'X-API-KEY: edd1c9f034335f136f87ad84b625c8f1' -X PUT -d '
{
    "uri": "/index.html",
    "upstream": {
        "type": "roundrobin",
        "nodes": {
            "127.0.0.1:1980": 1
        }
    }
}'
```
