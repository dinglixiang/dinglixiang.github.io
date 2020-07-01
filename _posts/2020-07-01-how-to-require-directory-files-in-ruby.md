---
title: "Ruby: how to require all files in a directory"
layout: post
date: 2020-07-01 17:40
guid: urn:uuid:eae1dd6c-0075-46e1-97dd-208f0c1878ba
author: "Lixiang"
tags:
  - Ruby
---
About require files in Ruby.

### Method 1
```ruby
Dir["/path/to/directory/*.rb"].each {|file| require file }
```

### Method 2
try the [`require_all`](http://github.com/jarmo/require_all) gem

```ruby
require_all 'path/to/directory'
```

FYI:
- [best-way-to-require-all-files-from-a-directory-in-ruby](https://stackoverflow.com/questions/735073/best-way-to-require-all-files-from-a-directory-in-ruby)
