# Secvisogram CSAF Demonstration Environment

- [About The Project](#about-the-project)
- [Getting started](#getting-started)
- [How to use](#how-to-use)
- [Contributing](#contributing)
  - [Developer Guide, Architecture and Technical Design](#developer-guide-architecture-and-technical-design)
  - [Security Considerations](#security-considerations)

## About the project

This is a test and demontration environment to use all available tools around [CSAF](https://docs.oasis-open.org/csaf/csaf/v2.0/csaf-v2.0.html).

It demonstrates the use of 

- [Secvisogram](https://secvisogram.github.io)
- CSAF CMS Backend and 
- CSAF Provider

[(back to top)](#secvisogram-csaf-demonstration-environment)

## Getting started

Assure that you have **Docker** and **docker-compose ** installed.

Check out the repository and navigate to the working directory.

    git clone git@github.com:secvisogram/secvisogram_demonstrator
    cd secvisogram_demonstrator

Now you can start a development server as follows:

    docker-compose up

The containers will try to start. Some container need some additional configuration. This can be done as follows:

	docker compose -f docker-compose.yaml up init-keycloak
	docker compose -f docker-compose.yaml up init-cms-backend-db
	docker compose -f docker-compose.yaml up init-provider

The application is now running and accessible at http://localhost

[(back to top)](#secvisogram-csaf-demonstration-environment)

### Deploying to Production

When deploying this environment to production **Keycloak**, **Couchdb** and **postgresql** container need to be configured for production use. Please refer to the guides provided by these projects:

[(Keycloak)](https://keycloak.org)
[(CouchDB)](https://couchdb.org)
[(Postgresql)](https://postgreql.org)

There is template file ´´config/keycloak/csaf-realm.json´´ for keycloak can be used as a starting point. This file contains all relevant informations for the realm, but no default user. You could also use ´´config/keycloak/csaf-realm-demo.json´´, remove the demo users and create new users by your own.

The config file ´´config/reverse-proxy/nginx.conf´´ for the reverse proxy contains some URL to access couchdb and keycloak. These lines have to be removed.

[(back to top)](#secvisogram-csaf-demonstration-environment)

## How to use

More information about the usage of Secvisogram can be found at the project page of [Secvisogram](https://github.com/secvisogram/secvisogram).

[(back to top)](#secvisogram-csaf-demonstration-environment)

## Contributing

Please refer to [`CONTRIBUTING.md`](CONTRIBUTING.md) for details about how to contribute to the development of [Secvisogram Demonstrator](https://github.com/secvisogram/secvisogram_demonstrator).

[(back to top)](#secvisogram-csaf-demonstration-environment)

### Developer Guide, Architecture and Technical Design

```mermaid
   C4Component
    title Component diagram for CSAF CMS Backend

    Person(user,"User")
    Container(reverseproxy, "Reverse-Proxy", "nginx")
    
    Container_Boundary(c4, "Internal") {
        Container(secvisogram, "Secvisogram", "nginx + javascript", "Provides secvisogramm via their web browser.")

        Container_Boundary(c2, "Keycloak") {
            Container(keycloak, "Keycloak", "keycloak")
            ContainerDb(keycloak-db, "PostGreSQL", "Keycloak-Database")
        }

        Container_Boundary(c3, "Oauth") {
            Container(oauth, "OAuth2-Proxy", "Authentication for REST-API")
            Container(validator, "CSAF validator service", "node")

            Container_Boundary(c1, "Backend") {
                Container(backend, "CSAF-CMS-Backend", "Spring Boot")
                ContainerDb(backend-db, "CouchDB", "CMS-Backend-Database")
            }
        }
    }

    Rel(user, reverseproxy,"","HTTPS")
    Rel(reverseproxy, secvisogram,"/")
    Rel(reverseproxy, oauth,"/api/*")
    Rel(reverseproxy, keycloak,"/realm/csaf/")
    Rel(reverseproxy, provider,"/.well-known/csaf")
    Rel(oauth, validator, "/api/v1/test")
    Rel(oauth, validator, "/api/v1/validate")
    Rel(oauth, backend, "/api/v1/advisories/*")
    Rel(backend, backend-db,"")
    Rel(backend, keycloak,"")
    Rel(keycloak, keycloak-db,"")   

```

[(back to top)](#secvisogram-csaf-demonstration-environment)