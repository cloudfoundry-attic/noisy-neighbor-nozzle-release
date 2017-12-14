Noisy Neighbor Nozzle Release
[![slack.cloudfoundry.org][slack-badge]][loggregator-slack]
[![CI Badge][ci-badge]][ci-pipeline]
=====================

This is a Loggregator Firehose nozzle. It keeps track of the log rate for
applications and reports to [Datadog](datadog).

## How it works.

The noisy neighbor nozzle consists of three components, nozzle, accumulator and
datadog-reporter.

The nozzle will read logs (excluding router logs by default) from the
Loggregator firehose keeping counts for the number of logs received for each
application. The nozzle stores the last 60 minutes worth of this data in an
in-memory cache.

The accumulator acts as a proxy for all the nozzles. When the accumulator
receives an HTTP request it will forward the same request to all the nozzles.
The accumulator then takes to rates from all the nozzles and sums them together,
responding with the total rates.

The datadog-reporter is an optional component. When deployed, it will request
rates from the accumulator every minute and report the top 50 noisiest
applications to [Datadog](datadog)

## Scaling

The nozzle can be scaled horizontally. We recommend having the same number of
nozzles as you have Loggregator Traffic Controllers.

The accumulator and datadog-reporter should only be deployed with a single
instance.

## Deployment

### Deploy to Bosh Lite

Ensure your CF deployment has a [client configured][firehose-details] with the
`doppler.firehose` scope and authority as well as the `uaa.resource`
and `cloud_controller.admin_read_only` authorities.

```
bosh create-release
bosh -e lite upload-release
bosh -d noisy-neighbor-nozzle deploy manifests/noisy-neighbor-nozzle.yml \
  -v datadog_api_key=DATADOG_API_KEY \
  -v uaa_client_id=CLIENT_ID \
  -v uaa_client_secret=CLIENT_SECRET \
  -v system_domain=bosh-lite.com
```

[firehose-details]:      https://github.com/cloudfoundry/loggregator-release#consuming-the-firehose
[slack-badge]:           https://slack.cloudfoundry.org/badge.svg
[loggregator-slack]:     https://cloudfoundry.slack.com/archives/loggregator
[ci-badge]:              https://loggregator.ci.cf-app.com/api/v1/pipelines/loggregator/jobs/noisy-neighbor-nozzle-bump-submodule/badge
[ci-pipeline]:           https://loggregator.ci.cf-app.com/teams/main/pipelines/loggregator/jobs/noisy-neighbor-nozzle-bump-submodule
[datadog]:               https://datadoghq.com
[noisy-neighbor-nozzle]: https://code.cloudfoundry.org/noisy-neighbor-nozzle
