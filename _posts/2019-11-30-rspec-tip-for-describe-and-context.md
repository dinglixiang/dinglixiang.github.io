---
title: "Rspec: tip for describe and context"
layout: post
date: 2019-11-30 21:06
guid: urn:uuid:bac794c5-9144-4afd-963c-e2193ab8a6d4
author: "Lixiang"
tags:
  - Rspec
---

### How to describe your methods
Be clear about what method you are describing.

For instance, use the
Ruby documentation convertion of `.`(or `::`) when referring to a class
method's name and `#` when referring to an instance method's name.

e.g.

- ~~Bad~~

    ```ruby
    describe 'the authenticate method for User' do
    describe 'if the user is an admin' do
    ```
- Good

    ```ruby
    describe '.authenticate' do
    describe '#admin?' do
    ```

### Use contexts
Contexts are a powerful method to make your tests clear and well
organized. In the long term this practice will keep test easy to
read.
>When describing a context, start its description with "when" or
"with".

e.g.

- ~~Bad~~

    ```ruby
    it 'has 200 status code if logged in' do
      expect(response).to respond_with 200
    end
    it 'has 401 status code if not logged in' do
      expect(response).to respond_with 401
    end
    ```
- Good

    ```ruby
    context 'when logged in' do
      it { should respond_with 200 }
    end
    context 'when logged out' do
      it { should respond_with 401 }
    end
    ```

### Keep your description short
A spec description should never be longer than 40 characters.If this
happens you should split it using a context.

e.g.

- ~~Bad~~

    ```ruby
    it 'has 422 status code if an unexpected params will be added' do
    ```
- Good

    ```ruby
    context 'when not valid' do
      it { should respond_with 422 }
    end
    ```
