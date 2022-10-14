Springboot Microservice demo project 

in this project we have 4 microservice 

1,customer-service
2,account-service
3,discovery-service(Eureka)
4,gateway-service(Zuul)



when client calls endpoint available inside customer-service which stores basic customer data via Zuul gateway. 
This endpoint interacts with account-service to collect information about customer accounts served by endpoint in account-service. 
Each service registering itself on Eureka discovery service.


we have account-service controller in the account-service application,inside there is findByCustomer method for collecting customer accounts by its id.


there is also customer-service controller in the customer-service application. there we have findById method which interacts with account-service using Feign client.


important configuration inside application.yml in customer-service. The ribbon load balancer needs to be enabled. 
I also suggest setting lease renewal and expiration on the Eureka client to enable unregistration from discovery service when our service is shutting down.


in order to run the project we have to create and run discovery service based on Eureka server. This functionality is provided by our discovery-service. 
We only have to import one dependency spring-cloud-starter-eureka-server and enable it in the application main class using @EnableEurekaServer annotation. 



After running discovery-service we see its monitoring console available on 8761 port. And now let’s run our two microservices on default ports set in their application.yml 
configuration file and more two instances of them on another ports using -DPORT VM argument, for example account-service on port 2223, and customer-service on port 3334. 
Now we take a look at the Eureka monitoring console. We’ve got two instances of account-service running on 2222, 2223 ports and two instances of customer-service running on 3333, 3334 ports.



Zuul will forward our request to the specific microservice based on its proxy configuration. Such request will also be load balances by ribbon client.
to enable Zuul gateway dependency spring-cloud-starter-zuul should be added inside pom.xml and annotation @EnableZuulProxy in the main class


Zuul is configured to be available under its default port 8765 and it forwards requests from /api/account/ path to account-service and from /api/customer/ to customer-service. 
When URL http://localhost:8765/customer/customers/1 is call several times we’ll see its load balanced between two instances of each microservice. Also when we shut down one microservice 
instance we can take a look that it is unregistered from the Eureka server.





