---
title: "Rspec: tip for test case"
layout: post
date: 2019-11-28 22:02
guid: urn:uuid:07ed34aa-19dc-48be-bd20-5a523eefee97
author: "Lixiang"
tags:
  - Rspec
---

### Single expectation test

The 'one expectation' tip is more broadly expressed as 'each test should
make only one assertion'. This helps you on finding possible errors,
going directly to the failing test, and to make your code readable.

### Test all possible cases

Testing is a good practice, but if you do not test the edge cases, it
will not be useful. Test valid, edge and invalid case.

e.g.

- Destroy Action

```ruby
  before_filter :find_owned_resources
  before_filter :find_resource

  def destroy
    render 'show'
    @consumption.destroy
  end
```

The error I usually see lies in testing only whether the resource has
been removed. But there are at least two edge cases: when the resouce is
not found and when it's not owned. Ad a rule of thumb think of all the
possible inputs and test them.

- ~~Bad~~

```ruby
it 'shows the resource'
```

- Good

```ruby
describe '#destroy' do
  context 'when resource is found' do
    it 'responds with 200'
    it 'show the resource'
  end

  context 'when resource is notfound' do
    it 'responds with 404'
  end
end
```
