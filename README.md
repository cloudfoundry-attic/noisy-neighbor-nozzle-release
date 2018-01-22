Noisy Neighbor Nozzle Release
[![slack.cloudfoundry.org][slack-badge]][loggregator-slack]
[![CI Badge][ci-badge]][ci-pipeline]
=====================

This is the [BOSH](bosh) release for the
[Noisy Neighbor Nozzle][noisy-neighbor-nozzle].

For more information on how the nozzle works and configuration, see the README
for the [Noisy Neighbor Nozzle][noisy-neighbor-nozzle].

## Deploy to Bosh Lite

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

### Example UAA Client
```
noisy-neighbor-nozzle:
  authorities: oauth.login,doppler.firehose,uaa.resource,cloud_controller.admin_read_only
  authorized-grant-types: client_credentials,refresh_token
  override: true
  scope: doppler.firehose,oauth.approvals
  secret: <secret>
```

[bosh]:              https://bosh.io
[datadog]:           https://datadoghq.com
[ci-badge]:          https://loggregator.ci.cf-app.com/api/v1/pipelines/loggregator/jobs/noisy-neighbor-nozzle-bump-submodule/badge
[ci-pipeline]:       https://loggregator.ci.cf-app.com/teams/main/pipelines/loggregator/jobs/noisy-neighbor-nozzle-bump-submodule
[slack-badge]:       https://slack.cloudfoundry.org/badge.svg
[firehose-details]:  https://github.com/cloudfoundry/loggregator-release#consuming-the-firehose
[loggregator-slack]: https://cloudfoundry.slack.com/archives/loggregator
[noisy-neighbor-nozzle]:         https://code.cloudfoundry.org/noisy-neighbor-nozzle
