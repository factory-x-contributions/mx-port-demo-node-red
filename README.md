<!--
SPDX-FileCopyrightText: Copyright (c) 2025 Software GmbH, Darmstadt, Germany
SPDX-FileContributor: Christian Winter (Software GmbH)

SPDX-License-Identifier: Apache-2.0
-->

MX Port Demo with Node-RED
==========================

*Illustrating the concept of MX Ports with Node-RED*

This project is a self-contained demonstration system for the
[MX Port concept](https://factory-x.org/wp-content/uploads/MX-Port-Concept-V1.00-1.pdf).
It is implemented with Node-RED and it serves two purposes:

1. The Node-RED flows in the project demonstrate the MX Port concept
   both functionally and visually. The MX Port components *Adapter*,
   *Converter* and *Gate* are mainly considered here.
   One of the two ports also comprises *Access Control* (but not
   Usage Control) while *Discovery* is not covered.

2. The project is a blueprint for IT/OT integration.
   Other heterogeneous sources can be integrated in the same way.
   The integration stack is implemented entirely in Node-RED.
   The stack begins with a Modbus device at the bottom
   and goes up to exposing an HTTP interface.
   
On top of the implemented stack (i.&thinsp;e. on top of the HTTP interface),
the integration stack can be extended by other applications.
However, Node-RED is not needed for that because relevant softwares
like AAS servers and connectors (EDC Connector and variants)
come with their own tools for integrating HTTP APIs as data sources
(only configuration required).
For example, the MX Port configurations “Hercules” and “Leo” are suitable for
extending this stack and exposing the device data to an MX data space.

If you need help with running this project in Node-RED, read the section
[Getting started with Node-RED projects](#getting-started-with-node-red-projects).


Teaser: What to find inside the project
---------------------------------------

The project has the following flow structure:

![Flow overview](doc/overview.svg)

The shown image depicts a vertical integration stack
from a machine at the bottom to some arbitrary application at the top.
Each box in the stack represents a system, which is modeled within Node-RED
except for the upper box, which is only commented, but not implemented.
The stack includes two MX Ports shown as grey boxes.
For viewing the inner structure of each box, open the project in Node-RED.

The monitoring application in the middle of the stack has a dashboard
as user interface, which is shown in the screenshot below:

![Monitoring dashboard](doc/dashboard.png)


Using the HTTP API
------------------

The HTTP API exposed by the second MX Port (the upper one in the stack
shown above) has a single endpoint:

```http
GET /factoryx/{device}/{property}
```

The endpoint is secured with HTTP Basic Auth.
Authenticate with username `user` and password `secret`.

> [!CAUTION]
> Please note that a proper deployment must be secured with TLS
> for protecting the credentials transmitted over the network.
> This can be achieved by [configuring TLS in the Node-RED server](
> https://nodered.org/docs/user-guide/runtime/securing-node-red#enabling-https-access)
> or by using a web server as reverse proxy.

This endpoint fetches measurements as time series data.

It has two *path parameters*:

* `device`: The device for which measurements shall be retrieved.
* `property`: The property of the device identifying the specific time series.

The implemented demonstration system only provides data for
`/factoryx/machine/temperature`.
Additionally, the path `/factoryx/otherMachine/someProperty` is valid,
but it provides no data (empty response).

The endpoint has two optional *query parameters*:

* `from`: A timestamp identifying the point in time
  from which the time series shall be returned.
  Any date-time string accepted by the [`Date.parse()`](
  https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/parse)
  method of JavaScript can be used, e.&thinsp;g. `from=2025-07-01T00:00:00Z`.
* `to`: A timestamp identifying the point in time
  until which the time series shall be returned.

If none of these parameters is provided,
the recent hour of measurements is returned.
If only `from` is provided, one hour of measurements
starting with that point in time is returned.
If only `to` is provided, the measurements of the hour
before that point in time is returned.

The returned time series is encoded as JSON array.
Each entry in the array is a JSON object.
Each such object has a `timestamp` attribute and
additional attributes depending on the specific property requested.
For example, a request to `/factoryx/machine/temperature`
is replied with a response of the following kind: 

```json
[{"timestamp": "2025-07-01T16:26:30.000Z", "temperature": 54.28}, …]
```

In case of an error, the response status code is either 404 or 500,
and the response object provides a description of the error.


Getting started with Node-RED projects
--------------------------------------

Basic knowledge of [Node-RED](https://nodered.org/)
and access to an installation of Node-RED is assumed.
However, running a Node-RED project available via Git
requires some additional configuration, which is explained below.

To work with Node-RED projects, the project feature must be enabled first
in your local, user-specific Node-RED configuration file
`~/.node-red/settings.js` by setting the entry
`$.'module.exports'.'editorTheme'.'projects'.'enabled'` to `true`.
Afterwards, creating, opening, and cloning projects can be done
from the Node-RED frontend main menu under “**Projects**”.
Note that Git version control is inherently integrated
into the project feature of Node-RED.

Dependencies of a project on additional Node-RED modules are managed
in the Node-RED frontend from within “**Project Settings**” menu
found under “**Projects**” in the main menu.
The dependencies configuration shown there corresponds
to the dependencies declared in the project’s `package.json` file.

Declared dependencies must be installed locally.
For this, the dependencies configuration menu shows an installation button
if needed (i.&thinsp;e. if not locally installed so far).
Additionally, managing all locally installed modules is done
with the Palette Manger accessible under “**Manage palette**”
in the Node-RED frontend main menu.

> [!TIP]
> See https://nodered.org/docs/user-guide/projects/
> for more information on working with projects in Node-RED.


Project credentials
-------------------

This project does not contain any secrets (passwords, keys)
for accessing external systems,
and hence it does not contain any credentials in its `flows_cred.json` file.
Therefore, that file itself has not been encrypted.

> [!TIP]
> Consequently, when importing this project,
> you leave the credentials key empty.

Note that the HTTP API exposed by this project is secured with HTTP Basic Auth,
and the login credentials for this API are managed in the file `.htpasswd`.
This file is not related to the project credentials file mentioned before.


Funding notes
-------------

This work was developed within the research project Factory-X supported by the
German Federal Ministry for Economic Affairs and Energy (BMWE) with funding
from the European Union based on the NextGenerationEU package.

![Funding logo](doc/funding.png)

**Disclaimer**: The views and opinions expressed in this work are solely
those of the authors and do not necessarily reflect the views of the BMWE,
the European Union, or the European Commission.
Neither the BMWE, nor the European Union, nor the European Commission
can be held responsible for them.


Legal notes
-----------

The following applies to this project “MX Port Demo with Node-RED”.

<!-- REUSE-IgnoreStart -->
<!--
REUSE shall ignore the following lines to avoid confusion of these
project-level copyright statements with the file-level copyright statements
at the top of this file.
-->
Copyright (c) 2025 Software GmbH, Darmstadt, Germany
<!-- REUSE-IgnoreEnd -->

This project is licensed under the Apache License, Version 2.0 (the “LICENSE”);
you may not use this software except in compliance with the LICENSE.
A copy of the LICENSE is provided in the file `LICENSE_Apache-2-0.txt`.
You may also obtain a copy of the LICENSE at
https://www.apache.org/licenses/LICENSE-2.0.

Unless required by applicable law or agreed to in writing, software
distributed under the LICENSE is distributed on an *“AS IS” BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND*, either express or implied.
See the LICENSE for the specific language governing permissions and
limitations under the LICENSE.

This project may comprise separate components provided by third-party
developers and contributors. Copyright notices and license texts for these
can be found in the file `THIRD-PARTY-LICENSES.txt` in this same directory or
in license files in the respective directories where a component may be stored.

Portions of this software which are copied from or based on third-party
software, but not isolated from the remaining software, are marked in place,
and the original versions are provided in the `lib` directory for reference.
The inclusion is credited in the file `NOTICE.txt`, and copyright notices and
license terms of the original versions are provided as stated above.

> [!CAUTION]
> **Logos, trademarks, etc. are not covered by the license of this project.
> These may only be used in accordance with the regulations
> of the respective owner.**
