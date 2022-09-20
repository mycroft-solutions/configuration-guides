# Mycroft System Installation Guides

![System structure](./img/system-structure.png 'System structure')

The Mycroft system consists of following services:

- [Mycroft API](./mycroft-api) - connects Mycroft Scanner application to Mycroft Website,
- Mycroft Auth - installed with _Mycroft API_; handles user data and security,
- [Mycroft Website](./mycroft-website) - provides a browser interface for organization administrators,
- [Mycroft Web Server](./mycroft-web-server) - responsible for secure access to other services.

The installation process of _Mycroft API_ and _Mycroft Auth_ is bundled together in order to simplify their configuration.
_Mycroft Website_ can be installed separately, but requires connection to both _Mycroft API_ and _Mycroft Auth_ for complete functionality.

The three services should be installed on a single machine or a local network, then exposed to the Internet by the _Mycroft Web Server_.
All requests, including communication between the services, should be directed at _Mycroft Web Server_.

# System structure

The goal of this section of the guide is to illustrate some of the possible ways to configure the Mycroft System.
While most of the configuration shown on the diagrams is connected to the _Mycroft Web Server_, it's important to keep the system structure in mind while installing the individual services.

Each service is associated with two locations.
The private address (e.g. `123.456.79.90:8089` for _Mycroft API_ in provided examples), specified in respective service installation guides should be visible and accessed by _Mycroft Web Server_ **only**.
The public address (e.g. `123.456.79.90:12389` for _Mycroft API_ in provided examples) managed by _Mycroft Web Server_ redirects to the private address and can be used by other services, web browsers and the _Mycroft Scanner_ application.

## Simple configuration

![Simple configuration](./img/system-simple.png 'Simple configuration')

The simplest scenario would assume that all of the services are installed on a single machine (e.g. `123.456.79.90`).
_Mycroft Website_ can be accessed on the original IP address when hosted on port `443`.
We also specify public ports for each Mycroft service, redirecting to their private ports configured in their respective installations.

## Domain configuration

![Domain configuration](./img/system-domain.png 'Domain configuration')

If you own a domain (or multiple domains) pointing at your IP address, _Mycroft Web Server_ can be configured to utilize them.
The server should then be configured to listen only on port 443 and resolve the hostname for correct redirection.

## Using multiple machines

![Multiserver configuration](./img/system-multiple.png 'Multiserver configuration')

The services can be installed on separate machines, with the exception of _Mycroft Website_, which has to be available to _Mycroft Web Server_ directly.
To ensure secure connections, the private services (*API\*\*, *Auth\**) in this scenario must enforce HTTPS or be available to the *Mycroft Web Server\* within a local network, not visible from the Internet.
