

## Task
Your task is to create a simple Spring Boot service, that is able to calculate any possible land
route from one country to another. The objective is to take a list of country data in JSON format
and calculate the route by utilizing individual countries border information.

**Specifications**
- Spring Boot, Maven
- Data link: https://raw.githubusercontent.com/mledoze/countries/master/countries.json
- The application exposes REST endpoint /routing/{origin}/{destination} that
returns a list of border crossings to get from origin to destination
- Single route is returned if the journey is possible
- Algorithm needs to be efficient
- If there is no land crossing, the endpoint returns HTTP 400
- Countries are identified by `cca3` field in country data
- HTTP request sample (land route from Czech Republic to Italy):
  - `GET /routing/CZE/ITA HTTP/1.0` :
    ```
    {
    "route": ["CZE", "AUT", "ITA"]
    }
    ```

**Expected deliveries**
1. Source code
2. Instructions on how to build and run the application

_________

## Solution

I have created a simple Spring Boot application exposing a REST endpoint with a logic of routing between countries. 
A list of countries with borders is available at another endpoint. So the application serves both as a REST server and REST client.

### Libraries and implementation
- **REST client** using **Spring Cloud OpenFeign** for a REST client to the single [endpoint](https://raw.githubusercontent.com/mledoze/countries/master/countries.json). 
DTO object is mapped manually. Since the media type is `text/plain` from the Github raw-content, 
there is required an additional `MappingJackson2HttpMessageConverter` configured in the global `FeignConfiguration`.
- **REST server** is generated from the OpenApi specification using **OpenAPI generator Maven plugin**. 
The controller layer with the models is available on the classpath once generated. 
A simple GET endpoint is the only choice for this use-case.
- **DTO mapping** and object boilerplate generation is done using a strong combo **MapStruct** and **Lombok**. 
It means the annotation processors must be configured in `maven-compiler-plugin`. 
- **Package structure** is simply [layered](https://phauer.com/2020/package-by-feature/#package-by-layer). 
I use standard Spring beans implementing an interface and autowiring by constructor (except the test classes).
- **Automation** is based on GitHub Actions and SonarCloud integration for code coverage.
- **Search algorithm** is a breadth-first search in an unweighted graph remembering visited notes to avoid cycling.


### How to run

The only prerequisite is Java 11.

 - Build the application:
   - Windows: `mvnw.cmd clean install`
   - Unix: `mvnw clean install`
   
 - Run the application:
   - Windows: `mvnw.cmd spring-boot:run`
   - Unix: `mvnw spring-boot:run`

 - Try it out in Postman / Insomnia / Command line:
    ```
   curl --request GET \
    --url http://localhost:8080/routing/CZE/ITA
  
