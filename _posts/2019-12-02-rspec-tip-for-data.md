---
title: "Rspec: tip for data"
layout: post
date: 2019-12-02 00:01
guid: urn:uuid:f2d8cfc3-504a-4bc3-8142-ae6a16b3e651
author: "Lixiang"
tags:
  - Rspec
---

### Create only the data you need
If you have ever worked in a medium size project(but also in small
ones), test suites can be heavy to run. To solve this problem, it's
important not to load more data than needed. Also, if you think you need
dozens of records, you are probably wrong.

- Good

```ruby
describe "User" do
  describe ".top" do
    before { FactoryGirl.create_list(:user, 3) }
    it { expect(User.top(2)).to have(2).item }
  end
end
```

### Use factories and not fixtures
This is an old topic, but it's still good to remember it. Do not use fixtures because
they are difficult to control, use factories instead. Use them to reduce the verbosity on creating new data.

- ~~Bad~~

```ruby
user = User.create(
  name: 'Genoveffa',
  surname: 'Piccolina',
  city: 'Billyville',
  birth: '17 Agoust 1982',
  active: true
)
```

- Good

```ruby
user = FactoryGirl.create :user
```

One important note. When talking about unit tests the best practice would be to use
neither fixtures or factories. Put as much of your domain logic in libraries that
can be tested without needing complex, time consuming setup with either factories
or fixtures. Read more in [this article.](http://blog.steveklabnik.com/posts/2012-07-14-why-i-don-t-like-factory_girl)


### Mock or not to mock
There's a debate going on. Do not(over) use mocks and test real behavior when possible. Testing real cases are useful when updating your application flow.

- Good

```ruby
# simulate a not found resource
context "when not found" do
  before { Resource.stub(:where).with(created_from: params[:id]).and_return(false) }
  it { should respond_with 404 }
end
```

Mocking makes your specs fater but they are difficult to use. You need to understand them well to use them well. Read more [about.](http://myronmars.to/n/dev-blog/2012/06/thoughts-on-mocking)
