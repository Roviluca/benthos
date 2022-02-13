---
title: gcp_bigquery_select
type: processor
status: experimental
categories: ["Integration"]
---

<!--
     THIS FILE IS AUTOGENERATED!

     To make changes please edit the contents of:
     lib/processor/gcp_bigquery_select.go
-->

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

:::caution EXPERIMENTAL
This component is experimental and therefore subject to change or removal outside of major version releases.
:::
Executes a `SELECT` query against BigQuery and replaces messages with the rows returned.

Introduced in version 3.63.0.

```yaml
# Config fields, showing default values
label: ""
gcp_bigquery_select:
  project: ""
  table: ""
  columns: []
  where: ""
  job_labels: {}
  args_mapping: ""
  prefix: ""
  suffix: ""
```

## Examples

<Tabs defaultValue="Word count" values={[
{ label: 'Word count', value: 'Word count', },
]}>

<TabItem value="Word count">


Given a stream of English terms, enrich the messages with the word count from Shakespeare's public works:

```yaml
pipeline:
  processors:
    - branch:
        processors:
          - gcp_bigquery_select:
              project: test-project
              table: bigquery-public-data.samples.shakespeare
              columns:
                - word
                - sum(word_count) as total_count
              where: word = ?
              suffix: |
                GROUP BY word
                ORDER BY total_count DESC
                LIMIT 10
              args_mapping: root = [ this.term ]
        result_map: |
          root.count = this.get("0.total_count")
```

</TabItem>
</Tabs>

## Fields

### `project`

GCP project where the query job will execute.


Type: `string`  

### `table`

Fully-qualified BigQuery table name to query.


Type: `string`  

```yaml
# Examples

table: bigquery-public-data.samples.shakespeare
```

### `columns`

A list of columns to query.


Type: `array`  

### `where`

An optional where clause to add. Placeholder arguments are populated with the `args_mapping` field. Placeholders should always be question marks (`?`).


Type: `string`  

```yaml
# Examples

where: type = ? and created_at > ?

where: user_id = ?
```

### `job_labels`

A list of labels to add to the query job.


Type: `object`  
Default: `{}`  

### `args_mapping`

An optional [Bloblang mapping](/docs/guides/bloblang/about) which should evaluate to an array of values matching in size to the number of placeholder arguments in the field `where`.


Type: `string`  

```yaml
# Examples

args_mapping: root = [ "article", now().format_timestamp("2006-01-02") ]
```

### `prefix`

An optional prefix to prepend to the select query (before SELECT).


Type: `string`  

### `suffix`

An optional suffix to append to the select query.


Type: `string`  

