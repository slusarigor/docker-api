# docker-api

- Require gem 'docker-api' 


```ruby
require 'docker'

class DockerApi

  def self.resolve_ip
    host = "api.#{$environment}"
    begin
      IPSocket.getaddress(host)
    rescue SocketError => er
      puts er.message
    end
  end

  def self.connect_to_docker
    Docker.url = "tcp://#{resolve_ip}:2375"
  end

  def self.docker_containers
    Docker::Container.all.select {|cont| cont.info['State'].eql?('running')}
  end
  
  def self.run_ruby_command(container, command)
    container.store_file("/test_ruby_command", "exec(\"echo '#{command}' | rails c\" )")
    container.exec(["ruby", '/test_ruby_command'])
  end  
end
```


Example usage
```ruby
container = DockerApi.docker_containers[0]
DockerApi.run_ruby_command(container, 'puts User.count')
```