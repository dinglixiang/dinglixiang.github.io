---
title: "Gem: set the feature flags via the rollout"
layout: post
date: 2019-12-18 21:55
guid: urn:uuid:8dfcf204-939f-4940-99ef-3b39c3429eb1
author: "Lixiang"
tags:
  - Ruby
  - Gem
---

Recently, we redesign the mini program project. During the iteration, some features should be on the beta version or the beta users. And we control these via rollout.

The `rollout` gem is the fast feature flags based on Redis.

Within rollout, you can set the feature flags on so many dimensions. Like the follows:

### Set for groups
Rollout ships with one group by default: "all", which does exactly what it sounds like.

You can activate the all group for the chat feature like this:

```ruby
$rollout.activate_group(:chat, :all)
```
Deactivate groups like this:

```ruby
$rollout.deactivate_group(:chat, :all)
```

### Set for specific users

You might want to let a specific user into a beta test or something.

```ruby
$rollout.activate_user(:chat, @user)
```

Deactivate them like this:

```ruby
$rollout.deactivate_user(:chat, @user)
```
### Set for user percentages

f you're rolling out a new feature, you might want to test the waters by slowly enabling it for a percentage of your users.

```ruby
$rollout.activate_percentage(:chat, 20)
```

The algorithm for determining which users get let in is this:

```ruby
CRC32(user.id) < (2**32 - 1) / 100.0 * percentage
```
So, for 20%, users 0, 1, 10, 11, 20, 21, etc would be allowed in. Those users would remain in as the percentage increases.

Deactivate all percentages like this:

```ruby
$rollout.deactivate_percentage(:chat)
```

### Set for global actions
While groups can come in handy, the actual global setter for a feature does not require a group to be passed.

```ruby
$rollout.activate(:chat)
```

In that case you can check the global availability of a feature using the following

```ruby
$rollout.active?(:chat)
```
And if something is wrong you can set a feature off for everybody using

Deactivate everybody at once:

```ruby
$rollout.deactivate(:chat)
```

### END
You can replace the user object with other entity if you need.

```ruby
$rollout.activate_user(:new_theme, @site)
```

FYI:

- [rollout gem](https://github.com/fetlife/rollout)
