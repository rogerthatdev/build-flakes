# build-flakes


This project is a terraform module that will spring up a system that will 
file GitHub issues for Cloud Build triggers based on flakiness. The module
will take as input:
- project
- trigger names in current project
- parameters for the flakiness configuration

It will create:
- a pubsub subscription to the cloud-builds Pub/Sub topic
- an event handler Cloud Function
- a Cloud Function for each specified trigger with parameters specifying 
  desired behavior
- any other required resources (IAM, service, accounts, secrets, etc).

This module can be deployed once per project and take care of multiple triggers.

## Notes
The following `gcloud` command retrieves the createTime and status of the last 
100 jobs for the trigger `java-nightly`:

```
gcloud builds list --format="value(createTime, status)" --filter "substitutions.TRIGGER_NAME='java-nightly'" --limit 100
```

Create a pubsub subscription with an endpoint url to a cloud function.


A cloud function that is triggered on a pubsub message
it will check the for a trigger name and fier off a flake bug if 
the test has failed 3 times consecutively (by default)

