# Importing sample data for IBM Business Automation Insights

You can test and explore IBM Business Automation Insights by using the provided script and sample data.
This script is designed only for IBM Business Automation Insights 23.0.1 or later.

## Prerequisites

To run the script that imports data, you need the following tools.
* envsubst - The envsubst tool is available from this page: [https://github.com/a8m/envsubst](https://github.com/a8m/envsubst)

* jq - The jq tool is available from this page: [https://stedolan.github.io/jq/download/](https://stedolan.github.io/jq/download/)<br /><br />
You install jq on MacOS, on Linux, or on Windows Cygwin by running curl commands.
  * On MacOS:
  ```
  curl -L https://github.com/stedolan/jq/releases/download/jq-1.6/jq-osx-amd64 -o jq
  chmod +x jq
  sudo mv jq /usr/local/bin
  ```
  * On Linux:
  ```
  curl -L https://github.com/stedolan/jq/releases/download/jq-1.6/jq-linux64 -o jq
  chmod +x jq
  sudo mv jq /usr/local/bin
  ```
  * On Windows Cygwin:
  ```
  curl -L https://github.com/stedolan/jq/releases/download/jq-1.6/jq-win64.exe -o jq.exe
  chmod +x jq.exe
  mv jq /usr/local/bin
  ```

### About this capability

The script imports data sets directly into Opensearch: for BPMN processes or Operational Decision Manager decisions. At each import, the data is dated by default at the current date minus 1 day. You can adjust the time span later in the dashboard.

## Accessing Opensearch

To use the import script, you must have access to Opensearch.

  1. Log in to the OpenShift namespace where the IBM Cloud Pak for Business Automation platform is deployed.

  2. Retrieve the Opensearch URL. Enter the namespace

```sh
oc get route opensearch-route -n ${NAMESPACE} -o jsonpath='{.status.ingress[0].host}'
```

  3. Retrieve the name of the Opensearch secret containing the username and password.

```sh
OPENSEARCH_SECRET=opensearch-ibm-elasticsearch-cred-secret
```

  4. Retrieve the Opensearch username.

```sh
OPENSEARCH_USERNAME=elastic
```

  5. Retrieve the Opensearch password.

```sh
oc extract secret/${OPENSEARCH_SECRET} --keys=elastic --to=-
```

## Importing sample data

### Prerequisites

**Important** Make sure that the path of the target installation directory contains no spaces.

### Procedure

1. Clone or download the GitHub project from https://github.com/icp4a/bai-data-samples, and then decompress it.<br />

1. Run the <code>bai-import-index</code> script with the following options:
```
./bin/bai-import-index -u <Opensearch_username> -p <Opensearch_password> -k https://<Opensearch_URL> -c <periodic_odm | bpmn>
```
When the import completes, you can create visualizations from the imported data by using Kibana and Business Performance Center. <br />
* Operational Decision Manager data appears as **Decisions** dashboards.

* BPMN data appears as **Workflow - Hiring Sample**, **Workflow - Process Tasks**, and **Workflow - Processes** dashboards.<br />

**Note:** To make sure that, in any dashboard, you see all the data you are interested in, adjust the date filter up to the latest 2 days.

### Examples

This command imports data from a IBM Business Automation Workflow BPMN data source.

```sh
./bin/bai-import-index -u <Opensearch_username> -p <Opensearch_password> -k https://<Opensearch_URL> -c bpmn
```

This command imports data from an Operational Decision Manager data source.

```sh
./bin/bai-import-index -u <Opensearch_username> -p <Opensearch_password> -k https://<Opensearch_URL> -c periodic_odm
```