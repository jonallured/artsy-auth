# Artsy Authentication

Ruby Gem for adding Artsy's omniauth based authentication to your app.

## Installation
Add following line to your Gemfile

```
gem 'artsy-auth'
```

## Usage
Artsy Auth is based on [`Rails::Engine`](http://api.rubyonrails.org/classes/Rails/Engine.html).

### Configure
Add `artsy_auth.rb` under `config/initializer`. We need to configure `ArtsyAuth` to use proper Artsy `application_id` and `application_secret`. Also it needs `artsy_url` which will be used to redirect `sign_out` to proper location.
`callback_url` defines after a successful omniauth handshake, where should we get redirected to.

```ruby
# config/initalizers/artsy_auth.rb
ArtsyAuth.config.artsy_url = 'https://stagingapi.artsy.net'
ArtsyAuth.config.callback_url = '/admin'
ArtsyAuth.config.application_id = '321322131'
ArtsyAuth.config.application_secret = '123123asdasd'
```

You also need to mount session related endpoints to your app, in your `config/routes.rb`. Add following line to your current routes.
```ruby
# config/routes.rb
mount ArtsyAuth::Engine => '/'
```

In order to force authenticaiton, you need to change your `ApplicationController` to inherit from ` ArtsyAuth::ApplicationController`, you also need to add (override) `authorize?` method there which gets a token and in your app you need to define how do you authorize that token, for example:
```ruby
class ApplicationController < ArtsyAuth::ApplicationController
  # Prevent CSRF attacks by raising an exception.
  protect_from_forgery with: :exception

  # override applicaiton to decode token and allow only users with `tester` role
  def authorized?(token)
    decoded_token, _headers = JWT.decode(token, 'some-secret')
    decoded_token['roles'].include? 'tester'
  end
end
```
Note that this will add authenticaiton to all of your controllers, if you want to skip Artsy's authentication for specific controller you can skip it in your controller by adding:
```ruby
class TestController
  skip_before_action :require_artsy_authentication
end
```


# Contributing

* Fork the project.
* Make your feature addition or bug fix with tests.
* Update CHANGELOG.
* Send a pull request. Bonus points for topic branches.