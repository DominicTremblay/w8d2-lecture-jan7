# W8D2 - Lecture

- Integration Testing (Feature)

## Different Tests

- Unit tests test the Models, which interact with the database.

- Controller tests test the Controllers, which communicate info between the models and views. A controller accepts input and sends commands.

- Integration tests test the Views in the browser as well as the user experience. In other words, what’s being presented as HTML to users of the app.

## Integration Tests (Feature)

- A lot more high-level than unit tests, which focus on the technical details and business logic of your models.

- Describe flows on and between your pages.

- Feature tests which are based on your user stories, check if the requirements are being met or not

### Pros and Cons

- Tests that are not possible with other kind of tests

- Slower to run than unit tests

- Increases the costs of running tests

### Capybara

Capybara is a testing tool that allows us to:

- Automate Tests for the UI

- Writing assertions againts actual pages

- Written from the perspective of a user

- Simulate your browsers clicking through different actions such as clicking around and filling out forms

#### Setup

Gems we need:

- Capybara
- Poltergeist
- Phantomjs (npm)
- DatabaseCleaner

##### Steps

1. npm install -g phantomjs
2. Add the following Gems to your Gemfile

```ruby
group :test do
  gem 'capybara'
  gem 'poltergeist'
  gem 'database_cleaner'
end
```

run bundle install

3. Modify rails_helper

```ruby
require "capybara/rails"
require "capybara/rspec"
require "capybara/poltergeist" # Add this line to require poltergeist

# Specs flagged with `js: true` will use Capybara's JS driver. Set
# that JS driver to :poltergeist
Capybara.javascript_driver = :poltergeist
```

4. Also change in rails_helpers

- set `config.use_transactional_fixtures` to false instead of true

5. Uncomment the following

`Dir[Rails.root.join('spec/support/**/*.rb')].each { |f| require f }`

6. Database Cleaner Setup

a- Create the spec/support folder and within it create a file called database_cleaner.rb.
b- Paste the following code

```ruby
RSpec.configure do |config|

config.before(:suite) do
  if config.use_transactional_fixtures?
    raise(<<-MSG)
      Delete line `config.use_transactional_fixtures = true` from rails_helper.rb
      (or set it to false) to prevent uncommitted transactions being used in
      JavaScript-dependent specs.

      During testing, the app-under-test that the browser driver connects to
      uses a different database connection to the database connection used by
      the spec. The app's database connection would not be able to access
      uncommitted transaction data setup over the spec's database connection.
    MSG
  end
  DatabaseCleaner.clean_with(:truncation)
end

config.before(:each) do
  DatabaseCleaner.strategy = :transaction
end

config.before(:each, type: :feature) do
  # :rack_test driver's Rack app under test shares database connection
  # with the specs, so continue to use transaction strategy for speed.
  driver_shares_db_connection_with_specs = Capybara.current_driver == :rack_test

  if !driver_shares_db_connection_with_specs
    # Driver is probably for an external browser with an app
    # under test that does *not* share a database connection with the
    # specs, so use truncation strategy.
    DatabaseCleaner.strategy = :truncation
  end
end

config.before(:each) do
  DatabaseCleaner.start
end

config.append_after(:each) do
  DatabaseCleaner.clean
end

end
```

7. Make sure to add a test group in secrets.yml

8) Create a file `images.rb` in the support folder and add the following:

```ruby
# Helper function to open product images
def open_asset(file_name)
  File.open(Rails.root.join('db', 'seed_assets', file_name))
end
```

##### Create the tests

###### Testing Create New User

`rails generate rspec:feature users`

Here are the 2 spec files we did during the lecture, with an additional one as a reference:

- [users_spec.rb](./users.md)
- [sold_outs_spec.rb](./sold_out.md)
- [carts_spec.rb](./cart.md)

## References

- [Capybara](https://github.com/teamcapybara/capybara)
- [Cabybara Ruby Doc](https://www.rubydoc.info/github/jnicklas/capybara)
