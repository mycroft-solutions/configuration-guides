# Mycroft Website

The browser interface for Mycroft Scanner application. It provides administrative functionalities for organization administrators and remote actions for client applications across the organization.

# Prerequisites

To run _web-app-front_ you will need the LTS version of `Node.js` runtime as well as the corresponding `npm` version.

# Installation

Start with cloning the repository or downloading and unzipping the files, then navigate to the project directory.

## Dependencies

Run the following command to install the project dependencies:

```
npm install
```

## Environment variables

Before you run the project, some environment variables have to be set.
The easiest method of doing that is creating a `.env` file in the project directory, then defining the variables inside of it using `KEY=VALUE` format with each pair in a new line (see the [example](#example)).

### Required environment variables

- `REACT_APP_BACKEND_URL` - the address of the corresponding Mycroft WebApp backend server
- `REACT_APP_AUTH_CLIENT` - the name of the Keycloak client configured for frontend connections
- `REACT_APP_AUTH_REALM` - the name of the Keycloak realm configured with the system
- `REACT_APP_AUTH_URL` - the address of the corresponding Keycloak authentication server

### Example

Suppose the Mycroft WebApp server is published at `https://123.456.78.90:12391` and the Keycloak authentication server is available at` https://123.456.78.90:12392`.
On the authentication server you have set up a client named `mycroft-website` that accepts connections from your website's address.

The contents of the `.env` file should look as follows:

```
REACT_APP_BACKEND_URL=https://123.456.78.90:12391
REACT_APP_AUTH_CLIENT=mycroft-website
REACT_APP_AUTH_REALM=mycroft
REACT_APP_TOKEN_URL=https://123.456.78.90:12392
```

## Test run _(optional)_

At this point you can test the website by running the command:

```
npm run dev
```

This will host the application on your local machine and, if possible, open it in an available browser.
You can also access the website by going to `http://localhost:3000`.

If the other services are running and your environment variables are set correctly, you should be able to create an account and use the system.

However, this isn't the final step, as your connection is not encrypted and the bundle is not optimized.
This step can be used for verifying the configuration and confirming that the application is ready for deployment.

## Final build

To generate optimized build run the command:

```
npm run build
```

This will create a `build/` directory containing static files that can be served in a production environment.
For that you will need a HTTP server.

# Publishing

Currently the Nginx server is meant to directly host the contents of the `build/` directory, as well as act as a reverse proxy for other Mycroft services.
You can find the Nginx configuration [here](../mycroft-web-server).

Make sure to copy the files to a directory available to the Nginx service, for example:

```
cp -rf ./build /var/html/mycroftsolutions
```
