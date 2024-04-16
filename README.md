# gha-dispatch-ci

Used to trigger CI workflows on a schedule. This workflow allows multiple branches to have CI workflows run on a schedule, rather than just the default branch.

For modules that are to be scheduled once per week, the cron must be run two times per week on consecutive days. This is done because there is logic in action.yml to run one job on the CMS 4 branches on "even" days of the week and CMS 5 branches on "odd" days of the week.

For builds that are to be scheduled once per day, specifically `silverstripe/installer` and `silverstripe/recipe-kitchen-sink`, the cron must be run two times per day on consecutive hours, because for these modules CMS 4 branches are run on "even" hours and CMS 5 branches are run on "odd" hours.

### Usage

**.github/workflows/dispatch-ci.yml**
```yml
name: Dispatch CI

on:
  # Every Tuesday,Wednesday at 1:00pm UTC
  schedule:
    - cron: '0 13 * * 2,3'

permissions: {}

jobs:
  dispatch-ci:
    name: Dispatch CI
    # Only run cron on the silverstripe account
    if: (github.event_name == 'schedule' && github.repository_owner == 'silverstripe') || (github.event_name != 'schedule')
    runs-on: ubuntu-latest
    permissions:
      contents: read
      actions: write
    steps:
      - name: Dispatch CI
        uses: silverstripe/gha-dispatch-ci@v1
```
