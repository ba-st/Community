# How to run again a failing check

When one of the checks run during a PR is failing

![Check Failed][check-failed]

you can run it again clicking on **Details** and in the following screen

![Re-Run Jobs][re-run]

clicking on **Re-run jobs**. Sadly, for jobs using a matrix configuration there's no way to only run one of the combinations it will always re-run the whole job again.

[check-failed]:images/check-failed.png
[re-run]:images/re-run-jobs.png
