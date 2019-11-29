---
title: "Rspec: tip for subject"
layout: post
date: 2019-11-29 21:33
guid: urn:uuid:736b4e9a-a1bb-4fbe-ac23-e48bac18c5f9
author: "Lixiang"
tags:
  - Rspec
---

##### Use subject
If you have several tests related to the same subject use `subject{}` to
DRY them up.

- Bad

```ruby
it { expect(assigns('message')).to match /it was born n Belville/ }
it { expect(assigns('message').creator).to match /Topolino/ }
```
- Good

```ruby
subject { assigns('message') }
it { should match /it was born in Billiville/ }
```

RSpec has also the ability to use a named subject.

- Good

```ruby
subject(:hero) { Hero.first }
it "carries a sword" do
  expect(hero.equipment).to include "sword"
end
```
