# Sidekiq

**Note:**
Sidekiq has a public API allowing access to real-time information about workers, queues and jobs. If console don't load sidekiq/api, run this command in rails console `require 'sidekiq/api'`

**Get all queues**
```bash
Sidekiq::Queue.all
```

**Get a queue**
```bash
Sidekiq::Queue.new # the "default" queue
Sidekiq::Queue.new("mailer")
```

**Gets the number of jobs within a queue**
```bash
Sidekiq::Queue.new.size # => 4
```

**Calculate the latency (in seconds) of a queue (now - when the oldest job was enqueued)**
```bash
Sidekiq::Queue.new.latency
14.5
```

**Find a job by JID (WARNING: this is very inefficient if your queue is big!)**
```bash
Sidekiq::Queue.new.find_job(somejid)
```

**Deletes all jobs in a queue / redis queue**

```bash
Sidekiq::Queue.new.clear
Sidekiq::ScheduledSet.new.clear
Sidekiq::RetrySet.new.clear
Sidekiq::DeadSet.new.clear
```
Sometimes, these commands would clear the queue.
Other times, you would need to clear the queue from Redis itself. You can either empty the current database of Redis using
```bash
Sidekiq.redis { |conn| conn.flushdb }
```
Or you can clear all databases using
```bash
Sidekiq.redis { |conn| conn.flushall }
```
You can also run these commands directly in the terminal as
```bash
redis-cli -n flushdb
redis-cli flushall
```

**Deletes jobs within the queue mailer with a jid of 'abcdef1234567890'**
```bash
queue = Sidekiq::Queue.new("mailer")
queue.each do |job|
  job.klass # => 'MyWorker'
  job.args # => [1, 2, 3]
  job.delete if job.jid == 'abcdef1234567890'
end
```

**Stats**

```bash
stats = Sidekiq::Stats.new
stats.processed # => 100
stats.failed # => 3
stats.queues # => { "default" => 1001, "email" => 50 }
```

**Gets the number of jobs enqueued in all queues (does NOT include retries and scheduled jobs).**

```bash
stats.enqueued # => 5
```
