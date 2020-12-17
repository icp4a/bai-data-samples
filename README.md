# Importing sample data for IBM Business Automation Insights

**New in 20.0.3** You can test and explore IBM Business Automation Insights by using the provided script and sample data. This script is designed only for IBM Business Automation Insights 20.0.3 or later.

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

The script imports data sets directly into Elasticsearch: for BPMN processes, Operational Decision Manager decisions or 
Automation Decision Services. At each import, the data is dated by default at the current date minus 1 day (except for 
Automation Decision Services). You can adjust the time span later in the dashboard.

## Accessing Elasticsearch

To use the import script, you must have access to Elasticsearch.

You access Elasticsearch differently depending on whether you work with IBM Business Automation Insights for a server or with an OpenShift deployment.

  * With IBM Business Automation Insights for a server:

  1. Set the ELASTICSEARCH_PORT environment variable in the <bai_sn_install_dir>/.env configuration file, for example to 9200.
  2. Log in to Elasticsearch by using the credentials that you passed when you installed IBM Business Automation Insights for a server.

  * OpenShift cluster:

    Access to Elasticsearch requires an OpenShift route.<br />
    1. Check whether an OpenShift route to Elasticsearch exists.
    ```
    oc get route --namespace <NAMESPACE> | grep "-ibm-dba-ek-client"
    ```

    If the route exists, the command returns its name and hostname. Otherwise, it returns nothing. Use the route URL as the Elasticsearch URL when you run the import script.<br />

    1. If the Openshift route does not exist, create one as instructed in
  link: [Exposing the Elasticsearch service through an OpenShift route or an Ingress](https://www.ibm.com/support/knowledgecenter/en/SSYHZ8_20.0.x/com.ibm.dba.install/op_topics/tsk_post_bai_deploy_es_route.html)<br />

    1. To log in to Elasticsearch, use the credentials that you specified when you installed IBM Business Automation Insights with the provided Elasticsearch and Kibana. By default, use <code>admin</code> as the username and <code>passw0rd</code> as the user password. See [Embedded Elasticsearch parameters](https://www.ibm.com/support/knowledgecenter/en/SSYHZ8_20.0.x/com.ibm.dba.ref/k8s_topics/ref_bai_k8s_es_params.html).<br />

## Importing sample data

### Prerequisites

**Important** Make sure that the path of the target installation directory contains no spaces.

### Procedure

1. Clone or download the GitHub project from https://github.com/icp4a/bai-data-samples, and then decompress it.<br />

1. Run the <code>bai-import-index</code> script with the following options:
```
./bin/bai-import-index -u <Elasticsearch_user_name> -p <Elasticsearch_user_password> -k <Elasticsearch_URL> -c <periodic_odm | bpmn>
```
When the import completes, you can create visualizations from the imported data by using Kibana and Business Performance Center. <br />
* Operational Decision Manager data appears as **Decisions** dashboards.

* BPMN data appears as **Workflow - Hiring Sample**, **Workflow - Process Tasks**, and **Workflow - Processes** dashboards.<br />

* Automation Decision Services data appears as **Decisions (ADS) - Common Data**.<br />

**Note:** To make sure that, in any dashboard, you see all the data you are interested in, adjust the date filter up to the latest 2 days.

### Examples

To run the following commands, set <code>localhost</code> to the hostname that you specified during the installation and use 9200 as the Elasticsearch port.

This command imports data from a IBM Business Automation Workflow BPMN data source.

```
./bin/bai-import-index -u elastic -p myPassword -k localhost:9200 -c bpmn

```

This command imports data from an Operational Decision Manager data source.

```
./bin/bai-import-index -u elastic -p myPassword -k localhost:9200 -c periodic_odm
```

This command imports data from an Automation Decision Services data source.

```
./bin/bai-import-index -u elastic -p myPassword -k localhost:9200 -c ads-common-data
```
