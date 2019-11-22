---
title: "Ruby: parse all json files in directory"
layout: post
date: 2019-11-22 18:00
guid: urn:uuid:7cf64777-8a33-423b-bce2-40144093e146
author: "Lixiang"
tags:
  - Ruby
---

Find all the JSON files in the local directory and any subdirectories and attempt to parse them with a Ruby JSON parser.

```ruby
require 'json'

Dir["**/*.json"].each do |json_file|
  JSON.parse(File.read(json_file))
end
```

FYI:

- [parse-json-file](https://gist.github.com/burtlo/0726166e41481c286ede)
