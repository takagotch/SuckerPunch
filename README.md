### SuckerPunch
---
https://github.com/brandonhilkert/sucker_punch

```
gem 'sucker_punch', '~> 2.0'
bundle
gem install sucker_punch

rails g sucker_punch:job logger
```

```ruby
LogJob.new.async.perform(...)
LogJob.perform_async(...)

# config/initializers/sucker_punch.rb
require 'sucker_punch/async_syntax'

# app/jobs/log_job.rb
class LogJob
  include SuckerPunch::Job
  def perform(evnet)
    Log.new(event).track
  end
end

LogJob.new.perform("login")
LogJob.perform_async("login")

class LogJob
  include SuckerPunch::Job
  workers 4
  def perform(event)
    Log.new(event).track
  end
end

class LogJob
  include SuckerPunch::Job
  max_jobs 10
  def perform(event)
    Log.new(event).track
  end
end

class DataJob
  include SuckerPunch::Job
  def perform(data)
    puts data
  end
end
DataJob.perform_async("asdf")
DataJob.perform_in(60, "asdf")

# app/jobs/awesome_job.rb
class AwesomeJob
  include SuckerPunch::Job
  def perform(user_id)
    ActiveRecord::Base.connection_pool.with_connection do
      user = User.find(user_id)
      user.update(is_awesome: true)
    end
  end
end

class AwesomeJob
  include SuckerPunch::Job
  def perform(user_id)
    ActiveRecord::Base.connection_pool.with_connection do
      user = User.find(user_id)
      user.update_attributes(is_awesome: true)
      LogJob.perform_async("User #{user.id} become awesome!")
    end
  end
end

SuckerPunch.logger = Logger.new('sucker_punch.log')
SuckerPunch.logger # => #<Logger:0x007fa1f28b83f0>

SukerPunch.exception_handler = -> (ex, klass, args) { ExceptionNotifier.notify_exception(ex) }
SuckerPunch.exception_handler = -> (ex, klass, args) { Airbrake.notify(ex) }

SuckerPunch.shutdown_timeout = 15

# spec/spec_helper.rb
require 'sucker_punch/testing/inline'
LogJob.perform_async("login")

# app.rb
require 'sucker_punch'
require 'sinatra'

gem 'sucker_punch'

# config/application.rb
class Application < Rails::Application
  config.active_job.queue_adapter = :sucker_punch
end

# config/initializers/sucker_punch.rb
require 'sucker_punch/async_syntax'

# spec/spec_helper.rb
RSpec.configure do |config|
  # config.use_transactional_fixtures = true
  config.before(:each) do
    DatabaseCleaner.strategy = :truncation
  end
  config.before(:each, job: true) do
    DatabaseCleaner.strategy = :truncation
  end
  config.before(:each) do
    DatabaseCleaner.clean
  end
  config.after(:each) do
    DatabaseCleaner.clean
  end
end

# spec/jobs/email_job_spec.rb
require 'spec_helper'

describe EmailJob, job: true do
  describe "#perform" do
    let(:user) { FactoryGirl.create(:user) }
    it "" do
      expect {
        EmailJob.new.perform(user.id)
      }.to change( ActionMailer::Base.deliveries.size ).by(1)
    end
  end
end
```

```
```
