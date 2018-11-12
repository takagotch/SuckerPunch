### SuckerPunch
---
https://github.com/brandonhilkert/sucker_punch

```
gem 'sucker_punch', '~> 2.0'
bundle
gem install sucker_punch
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
end












```

```
```
