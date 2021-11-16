[![Build Status](https://drone.kiwi-labs.net/api/badges/Diesel-Net/nexus/status.svg?ref=refs/heads/stable)](https://drone.kiwi-labs.net/Diesel-Net/nexus)

# nexus
[SonaType Nexus Repository Manager 3](https://help.sonatype.com/repomanager3) on docker swarm.


## Notes on the REST API

### Authentication
The API permits `Basic-Auth` so we just need to include the Authorization header with value `Basic username:password` where the part `username:password` is base64 encoded on every request.

e.g. `Authorization: Basic YWRtaW46bmV4dXM=`


The `-u` flag can be used with `curl` to auto-encode the correct Basic-Auth headers for you. This is a great way to play around with the API and get familiar. The command below illustrates how to change the admin password programatically (usually prompted on the first login through the WebUI).

```bash
curl -u admin:nexus3 'https://nexus.dev.diesel.net/service/rest/v1/security/users/admin/change-password' \
  -X 'PUT' \
  -H 'content-type: text/plain' \
  --data-raw 'newpass' \
  --compressed \
  --insecure \
  -v
```

### Creating Repositories
Below is an example of how to create a `hosted apt` repository.

```bash
curl -u admin:nexus3 'https://nexus.dev.diesel.net/service/rest/v1/repositories/apt/hosted' \
  -H 'accept: application/json' \
  -H 'content-type: application/json' \
  --data-raw $'{\n  "name": "internal-apt",\n  "online": true,\n  "storage": {\n    "blobStoreName": "default",\n    "strictContentTypeValidation": true,\n    "writePolicy": "allow_once"\n  },\n  "cleanup": {\n    "policyNames": [\n      "string"\n    ]\n  },\n  "component": {\n    "proprietaryComponents": true\n  },\n  "apt": {\n    "distribution": "bionic"\n  },\n  "aptSigning": {\n    "keypair": "string",\n    "passphrase": "string"\n  }\n}' \
  --compressed \
  --insecure \
  -v
```

### Cleanup Policies
`Cleanup Policies` CRUD operations seem to be missing from the Open API specification at the time of this writing. Maybe I missed it or they are adding it in a future release? Anywho, I was able to reverse ingineer the API calls from chrome dev tools and it is working with basic auth, per usual. Just note the slightly different path.


```bash
curl -u admin:nexus3 'https://nexus.dev.diesel.net/service/rest/internal/cleanup-policies' \
  -H 'accept: application/json, text/plain, */*' \
  -H 'content-type: application/json;charset=UTF-8' \
  --data-raw '{"name":"cleanupRoutine","notes":"Remove docker images older than 90 days","format":"docker","criteriaLastBlobUpdated":"90"}' \
  --compressed \
  --insecure \
  -v
```
