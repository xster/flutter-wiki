# Waiting for Tree to Go Green bot

_Note: links to source files are permalinks to master as of this writing; be sure to check the actual checked in code and update the wiki if it has changed._

[https://github.com/flutter/cocoon](Cocoon) has a request handler called [check-for-waiting-pull-requests](https://github.com/flutter/cocoon/blob/681a725decbca3dbf40046c6bebd65d4e07eaf8d/app_dart/lib/src/request_handlers/check_for_waiting_pull_requests.dart).  Sending a GET request will cause it to run a [GraphQL query](https://github.com/flutter/cocoon/blob/681a725decbca3dbf40046c6bebd65d4e07eaf8d/app_dart/lib/src/request_handlers/check_for_waiting_pull_requests_queries.dart) to get a list of pull requests in `flutter/flutter` and `flutter/engine` that are labeled with `waiting-for-tree-to-go-green`.  The label name is configured [here](http://shortn/_ISqzjn7WJK) (sorry, googlers only). You can run the [query](https://github.com/flutter/cocoon/blob/681a725decbca3dbf40046c6bebd65d4e07eaf8d/app_dart/lib/src/request_handlers/check_for_waiting_pull_requests_queries.dart#L6-L44) yourself in the [GraphQL Explorer](https://developer.github.com/v4/explorer/) to see what it returns.

A cron job ([source](https://github.com/flutter/cocoon/blob/681a725decbca3dbf40046c6bebd65d4e07eaf8d/app_dart/cron.yaml#L22-24) | [dashboard, googlers only](http://shortn/_QgBbGGtZUQ)) makes the GET request every 5 minutes.

The core logic is that pull requests [can be merged if]((https://github.com/flutter/cocoon/blob/681a725decbca3dbf40046c6bebd65d4e07eaf8d/app_dart/lib/src/request_handlers/check_for_waiting_pull_requests.dart#L232):

- CI has passed
- at least one approved review has been added (does not need to contain the phrase `LGTM`, but _must_ be an actual approval review via the GitHub UI)
- no changes have been requested

If these conditions are met, it sends an additional query to actually merge the pull request.

It will remove the label under the [following conditions](https://github.com/flutter/cocoon/blob/681a725decbca3dbf40046c6bebd65d4e07eaf8d/app_dart/lib/src/request_handlers/check_for_waiting_pull_requests.dart#L235):

- No approved reviews on the pull request
- At least one changes requested review on the pull request

The label can be re-applied after any change request reviews have been dismissed and at least one approval has been granted.