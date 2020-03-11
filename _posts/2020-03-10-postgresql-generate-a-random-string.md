---
title: "PostgreSQL: How to generate a random string"
layout: post
date: 2020-03-10 23:54
guid: urn:uuid:1bae3fde-f81c-46b2-b527-5f92b3b6f1ce
author: "Lixiang"
tags:
  - PostgreSQL
  - SQL
---
If you want to mock a large amount data, you may need to generate some random strings.

### random_string function

```sql
CREATE OR REPLACE FUNCTION random_string(integer) RETURNS text AS $body$
    SELECT array_to_string(array(SELECT substring('0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz' FROM (ceil(random()*62))::int FOR 1) FROM generate_series(1, $1)), '');
$body$ LANGUAGE SQL VOLATILE;
```

### How to use

```sql
SELECT randome_string(10);
```
