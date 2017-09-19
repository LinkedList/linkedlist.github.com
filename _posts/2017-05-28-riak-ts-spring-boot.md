---
layout: post
title: "Riak TS in Spring Boot"
description: "Setting up Riak TS in a Spring Boot application"
category: 
tags: [Riak, spring, boot, Riak TS]
---
A little tutorial on how to integrate Riak TS with a Spring Boot application

* Install Riak TS according to [instructions](http://docs.basho.com/riak/ts/1.5.2/setup/installing/debian-ubuntu/). 
At the time of writing this post the latest version was 1.5.2. For ubuntu the installation consists of these shell commands:
~~~shell
sudo apt-get install libpam0g-dev
wget http://s3.amazonaws.com/downloads.basho.com/riak_ts/1.5/1.5.2/ubuntu/xenial/riak-ts_1.5.2-1_amd64.deb
sudo dpkg -i riak-ts_1.5.2-1_amd64.deb
dpkg -l | grep riak #This should return riak-ts
sudo riak start
sudo riak ping #Returns a nice pong
~~~
Next we will create a necessary configs for riak client to work. 

* Add [riak-client](https://mvnrepository.com/artifact/com.basho.riak/riak-client) to <code>pom.xml</code>

~~~xml
<dependency>
    <groupId>com.basho.riak</groupId>
    <artifactId>riak-client</artifactId>
    <version>2.1.1</version>
</dependency>
~~~

or for Gradle to <code>build.gradle</code>

~~~groovy
dependencies {
    compile 'com.basho.riak:riak-client:2.1.1'
}
~~~

* Add riak url and port to <code>application.yml</code>

~~~yaml
riak:
  port: 8087 #This is different from the default http port which is 8098
  address: localhost
~~~

* Next create a <code>RiakTSConfig</code> class

~~~java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
class RiakTSConfig {

	@Value{"${riak.port}"}
	private Integer port;

	@Value{"${riak.address}"}
	private String address;

	@Bean //No need to specify close method, as shutdown is inferred
	public RiakClient riakClient() {
		return RiakClient.newClient(port, address);
	}

}
~~~

Please consult [RiakClient](http://basho.github.io/riak-java-client/2.1.1/com/basho/riak/client/api/RiakClient.html) for more options on how to create a new Riak TS client instance.
Shown instance creation is the fastest for locally testing the Riak TS client.

* And that is all! Now you can <code>@Autowire RiakClient</code> anywhere in your components and use it.
