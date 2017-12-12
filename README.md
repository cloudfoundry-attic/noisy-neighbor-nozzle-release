Noisy Neighbor Nozzle
[![slack.cloudfoundry.org][slack-badge]][loggregator-slack]
[![CI Badge][ci-badge]][ci-pipeline]
=====================

This is a Loggregator Firehose nozzle. It keeps track of the log rate for
applications and reports to [Datadog](datadog). On a set interval (default to
1 minute) the accumulator will collect log counts from the nozzles and send
those counts to [Datadog](datadog).

### How it works.

The noisy neighbor nozzle consists of two components, a nozzle and an
accumulator.

The nozzle will read logs (excluding router logs by default) from the
Loggregator firehose keeping counts for the number of logs received for each
application. The nozzle stores the last 60 minutes worth of this data in an
in-memory cache.

Every minute the accumulator will sum the application log rates from all the
nozzles for a single minute and report those totals to Datadog.

### Scaling

The nozzle can be scaled horizontally. We recommend having the same number of
nozzles as you have Loggregator Traffic Controllers.

Currently there should only ever be one accumulator.

### Deploy to Bosh Lite

Ensure your CF deployment has a [client configured][firehose-details] with the
`doppler.firehose` scope and authority as well as the `uaa.resource`
authority.

```
bosh create-release
bosh -e lite upload-release
bosh -d noisy-neighbor-nozzle deploy manifests/noisy-neighbor-nozzle.yml \
  -v datadog_api_key=DATADOG_API_KEY \
  -v uaa_client_id=CLIENT_ID \
  -v uaa_client_secret=CLIENT_SECRET \
  -v system_domain=bosh-lite.com
```

[firehose-details]:  https://github.com/cloudfoundry/loggregator-release#consuming-the-firehose
[slack-badge]:       https://slack.cloudfoundry.org/badge.svg
[loggregator-slack]: https://cloudfoundry.slack.com/archives/loggregator
[ci-badge]:          https://loggregator.ci.cf-app.com/api/v1/pipelines/loggregator/jobs/noisy-neighbor-nozzle-tests/badge
[ci-pipeline]:       https://loggregator.ci.cf-app.com/
[datadog]:           https://datadoghq.com
