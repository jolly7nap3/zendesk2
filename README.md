# Zendesk2

[![Build Status](https://secure.travis-ci.org/lanej/zendesk2.png)](http://travis-ci.org/lanej/zendesk2)

Ruby client for the [Zendesk V2 API](http://developer.zendesk.com/documentation/rest_api/introduction.html) using [cistern](https://github.com/lanej/cistern) and [faraday](https://github.com/technoweenie/faraday)

## Installation

Add this line to your application's Gemfile:
```ruby
gem 'zendesk2'
```
Or install it yourself as:

    $ gem install zendesk2

## Usage

### Defaults

Default credentials will be read in from `~/.zendesk2` file in YAML format.

```yaml
---
:subdomain: zendeskdev
:username: zendeskedge@example.com
:password: wickedsecurepassword
```

### Creating the client

Either the absolute url or the subdomain is required.  Username and password is always required.
```ruby
Zendesk2::Client.new(subdomain: "engineyard", username: "orchestra", password: "gwoo")
=> #<Zendesk2::Client::Real:0x007f99da1f9430 @url="https://engineyard.zendesk.com/api/v2", @username="orchestra", @password="gwoo", …>
```
or
```ruby
Zendesk2::Client.new(url: "http://support.cloud.engineyard.com", username: "mate", password: "bambilla")
=> #<Zendesk2::Client::Real:0x007fd1bae486b0 @url="http://support.cloud.engineyard.com", @username="mate", @password="bambilla", …>
```
### Resources

#### Collections

Currently support resources

* Categories
* Forums
* Organization
* Ticket
* Topics
* User

All collection are accessed like so:
```ruby
client.users.all
=> <Zendesk2::Client::Users
  count=1779,
  next_page_link="https://dev.zendesk.com/api/v2/users.json?page=2",
  previous_page_link=nil
  [
    <Zendesk2::Client::User
      id=125394183,
      url="https://dev.zendesk.com/api/v2/users/125394183.json",
      ...
    >
  ]
```
Collections also respond to `create` and `new`
```ruby
client.users.create(email: "ohhai@example.org", name: "lulz")
=> <Zendesk2::Client::User
  id=234020811,
  ...
  url="https://engineyarddev.zendesk.com/api/v2/users/234020811.json",
  ...
  email="ohhai@example.org",
  >
```

```ruby
client.users.new(email: "ohhai@example.org")
=> <Zendesk2::Client::User
  id=nil,
  ...
  url=nil,
  ...
  email="ohhai@example.org",
  ...
  >
```
#### Paging

Paged collections respond to `next_page` and `previous_page` when appropriate.  `page_size` and `page` can be passed directly to the collection to control size and index.
```ruby
page = client.users.all("per_page" => 1, "page" => 4)
=> <Zendesk2::Client::Users
  count=1780,
  next_page_link="https://dev.zendesk.com/api/v2/users.json?page=5&per_page=1",
  previous_page_link="https://dev.zendesk.com/api/v2/users.json?page=3&per_page=1"
  [
    <Zendesk2::Client::User
      id=217761652,
      url="https://dev.zendesk.com/api/v2/users/217761652.json",
      external_id=nil,
      name="Guy Dude",
      ...
    >
  ]
```
```ruby
page.next_page
=> <Zendesk2::Client::Users
  count=1780,
  next_page_link="https://dev.zendesk.com/api/v2/users.json?page=6&per_page=1",
  previous_page_link="https://dev.zendesk.com/api/v2/users.json?page=4&per_page=1"
  [
    <Zendesk2::Client::User
      id=217761742,
      url="https://dev.zendesk.com/api/v2/users/217761742.json",
      ...
      name="epitaphical osteofibrous",
      ...
    >
  ]
```
```ruby
page.previous_page
=> <Zendesk2::Client::Users
  count=1780,
  next_page_link="https://dev.zendesk.com/api/v2/users.json?page=5&per_page=1",
  previous_page_link="https://dev.zendesk.com/api/v2/users.json?page=3&per_page=1"
  [
    <Zendesk2::Client::User
      id=217761652,
      url="https://dev.zendesk.com/api/v2/users/217761652.json",
      ...
      name="Guy Dude",
      ...
    >
  ]
```
#### Models

All models respond to `destroy` and `save` if applicable.  `save` performs a 'create' operation if there is no identity provided or an 'update' if there is an identity.
```ruby
Zendesk2::Client::Ticket.new.save        # performs a create
Zendesk2::Client::Ticket.new(id: 1).save # performs an update
```
Attributes can be enumerated by the `attributes` method.

## Releasing

    $ gem install gem-release
    $ gem bump -trv (major|minor|patch)

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Added some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
