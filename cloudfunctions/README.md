# Cloud Functions for Build Flakes

This directory holds the code that will be used in the terraform to create the
Cloud Functions. There will be 2 Cloud Functions

## Event handler
The event handler will receive all build messages from the cloud-builds Pub/Sub 
topic.  Its purpose is to parse the message and route to the next Cloud 
Function. It will:

1. Receive build message
2. If success, ignore
3. If labeled to skip, ignore
4. If failed, trigger corresponding Cloud Function

## Trigger-specific Cloud Function
Each trigger has a corresponding Cloud Function that will be triggered on failed
build jobs. Flakiness is configured by:
1. Consecutive threshold 
2. Nonconsecutive threshold
3. Timeframe (in days)


For example, a trigger can be configured with the following values:
- consecutive_threshold: 2
- nonconsecutive_threshold: 3 
- timeframe: 7
  
An issue will be filed in its GitHub repository in the following scenarios:
- it fails 2 times in  a row (consecutive threshold met)
- it fails 3 times in the last 7 days (nonconsecutive threshold met)
  
If an open issue already exists for the trigger, the existing one will be 
updated with the latest failure. 

If a closed issue exists for the trigger failure, it will re-open it with the 
latest failure only if the issue has been closed with the timeframe. For example 
if the issue for the last flake streak was closed 8 days ago, it will remain 
closed and a new one will be created.

When a new issue is created, it will have the following:
- Link to latest failed job
- Links to previous failures in the threshold
- Links to last successful job

When an existing issue is being updated, it will add on the following:
- Link to the latest failed job

## Acknowledging and silencing 
In case we are aware of a flaky test but do not want to be notified for a 
limited time... TBD 