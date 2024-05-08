# gha-dispatch-ci

Used to trigger CI workflows on a schedule. This workflow allows multiple branches to have CI workflows run on a schedule, rather than just the default branch.

For modules that are to be scheduled once per week, the cron must be run two times per week on consecutive days. This is done because there is logic in action.yml to run one job for the latest patch branch on the previous major on "even" days of the week and the next minor branch for the current major on "odd" days of the week.

For builds that are to be scheduled once per day, specifically `silverstripe/installer` and `silverstripe/recipe-kitchen-sink`, the cron must be run two times per day on consecutive hours, because for these modules branches on the previous major version are run on "even" hours and branches on the current major version are run on "odd" hours.

### Disabling branches on previous major version

If the previous major version reaches the point in its life cycle where it is only receiving security fixes and no longer needs the cron job to run, it can be disabled by setting `RUN_BRANCHES_ON_PREVIOUS_MAJOR: "false"` in the `Check if should dispatch workflow` step in `actions.yml`.

Remember to set this to `true` when a new major version is released.

### Usage

**.github/workflows/dispatch-ci.yml**
```yml
name: Dispatch CI

on:
  # Every Tuesday,Wednesday at 1:00pm UTC
  schedule:
    - cron: '0 13 * * 2,3'

jobs:
  dispatch-ci:
    name: Dispatch CI
    # Only run cron on the silverstripe account
    if: (github.event_name == 'schedule' && github.repository_owner == 'silverstripe') || (github.event_name != 'schedule')
    runs-on: ubuntu-latest
    steps:
      - name: Dispatch CI
        uses: silverstripe/gha-dispatch-ci@v1
```
