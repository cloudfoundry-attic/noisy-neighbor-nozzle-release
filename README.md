Noisy Neighbor Nozzle
=====================

This is a Loggregator Firehose nozzle. It keeps track of the log rate for
applications.

### Deploy to Bosh Lite

Ensure your CF deployment has a [client configured][firehose-details] with the `doppler.firehose`
scope.

```
bosh create-release
bosh -e lite upload-release
bosh -d noisy-neighbor-nozzle deploy manifests/noisy-neighbor-nozzle.yml \
  -v uaa_client_id=CLIENT_ID \
  -v uaa_client_secret=CLIENT_SECRET \
  -v system_domain=bosh-lite.com
```

To view log rates:

```
bosh -d noisy-neighbor-nozzle ssh nozzle/0
curl localhost:8080/state
```

You can use [jq][jq-github] to format the response, which will result in the
following:

```
[
  {
    "counts": {
      "d99bcfb3-50f5-4836-9cea-ac1fb769b073/1": 966,
      "d99bcfb3-50f5-4836-9cea-ac1fb769b073/0": 1186,
      "24885497-ecfa-4aaa-b4c5-965c457f7670/0": 2048,
      "295b7483-17ae-4063-85d6-c1f9d162bb92/0": 2,
      "d505ddb7-3b48-43b7-945a-444c19db6e15/": 1,
      "d505ddb7-3b48-43b7-945a-444c19db6e15/0": 1255,
      "d505ddb7-3b48-43b7-945a-444c19db6e15/1": 93,
      "d505ddb7-3b48-43b7-945a-444c19db6e15/2": 1252,
      "d505ddb7-3b48-43b7-945a-444c19db6e15/3": 1253,
      "d505ddb7-3b48-43b7-945a-444c19db6e15/4": 1125
    },
    "timestamp": 1510249620
  },
  {
    "counts": {
      "d99bcfb3-50f5-4836-9cea-ac1fb769b073/1": 1084,
      "24885497-ecfa-4aaa-b4c5-965c457f7670/0": 2339,
      "295b7483-17ae-4063-85d6-c1f9d162bb92/0": 2,
      "d505ddb7-3b48-43b7-945a-444c19db6e15/0": 1251,
      "d505ddb7-3b48-43b7-945a-444c19db6e15/1": 147,
      "d505ddb7-3b48-43b7-945a-444c19db6e15/2": 248,
      "d505ddb7-3b48-43b7-945a-444c19db6e15/3": 1252,
      "d505ddb7-3b48-43b7-945a-444c19db6e15/4": 1252,
      "d99bcfb3-50f5-4836-9cea-ac1fb769b073/0": 1178
    },
    "timestamp": 1510249680
  }
]
```

The key for each count is `application-guid/instance-index`, the value is the
number of logs received from the firehose for the configured rate (default is
1 minute).

[firehose-details]: https://github.com/cloudfoundry/loggregator-release#consuming-the-firehose
[jq-github]:        https://github.com/stedolan/jq
