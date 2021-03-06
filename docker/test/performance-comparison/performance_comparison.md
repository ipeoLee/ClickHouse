# [draft] Performance comparison test

This is an experimental mode that compares performance of old and new server
side by side. Both servers are run, and the query is executed on one then another,
measuring the times. This setup should remove much of the variability present in
the current performance tests, which only run the new version and compare with
the old results recorded some time in the past.

To interpret the observed results, we build randomization distribution for the
observed difference of median times between old and new server, under the null
hypothesis that the performance distribution is the same (for the details of the
method, see [1]). We consider the observed difference in performance significant,
if it is above 5% and above the 95th percentile of the randomization distribution.
We also consider the test to be unstable, if the observed difference is less than
5%, but the 95th percentile is above 5% -- this means that we are likely to observe
performance differences above 5% more often than in 5% runs, so the test is likely
to have false positives.

### How to read the report

Should add inline comments there, because who reads the docs anyway. They must
be collapsible and I am afraid of Javascript, so I'm going to do it later.

### How to run
Run the entire docker container, specifying PR number (0 for master)
and SHA of the commit to test. The reference revision is determined as a nearest
ancestor testing release tag. It is possible to specify the reference revision and
pull requests (0 for master) manually.

```
docker run --network=host --volume=$(pwd)/workspace:/workspace --volume=$(pwd)/output:/output
    [-e REF_PR={} -e REF_SHA={} -e ]
    -e PR_TO_TEST={} -e SHA_TO_TEST={}
    yandex/clickhouse-performance-comparison
```

Then see the `report.html` in the `output` directory.

There are some environment variables that influence what the test does:
 * `-e CHCP_RUNS` -- the number of runs;
 * `-e CHPC_TEST_GREP` -- the names of the tests (xml files) to run, interpreted
 as a grep pattern.

#### Re-genarate report with your tweaks
From the workspace directory (extracted test output archive):
```
stage=report compare.sh
```
More stages are available, e.g. restart servers or run the tests. See the code.

#### Run a single test on the already configured servers
```
docker/test/performance-comparison/perf.py --host=localhost --port=9000 --runs=1 tests/performance/logical_functions_small.xml
```

### References
1\. Box, Hunter, Hunter "Statictics for exprerimenters", p. 78: "A Randomized Design Used in the Comparison of Standard and Modified Fertilizer Mixtures for Tomato Plants."
