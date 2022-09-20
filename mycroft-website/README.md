# Mycroft Website installation guide

_Mycroft Website_ is the browser interface for _Mycroft Scanner_ application. It provides functionalities for organization administrators and remote actions for client applications across an organization.

# Prerequisites

To run _Mycroft Website_ you will need the following:

- [Node JS](https://nodejs.org/en/download/package-manager/) 16

# Installation

In order to install _Mycroft Website_ on your system, the **Website Installation Package** will be needed.
After acquiring an archive containing necessary files, unpack it in a chosen location.
Let's mark the absolute path of this location as **<working_dir>**.

The following instructions assume you are located in the **<working_dir>**.

## Dependencies

Run the following command to install the project dependencies:

```sh
npm install
```

## Environment variables

Before you run the project, some environment variables have to be set.
The easiest method of doing that is creating a `.env` file directly in the **<working_dir>**, then defining the variables inside of it, using `KEY=VALUE` format with each pair in a new line (see the [example](#example-env-file)).

### Required environment variables

- `REACT_APP_BACKEND_URL` - the **public** address of _Mycroft API_ service
- `REACT_APP_AUTH_CLIENT` - the name of the Keycloak client configured for frontend connections (`mycroft-website` by default)
- `REACT_APP_AUTH_REALM` - the name of the Keycloak realm configured with the Mycroft system (`mycroft` by default)
- `REACT_APP_AUTH_URL` - the **public** address of _Mycroft Auth_ service

### Example env file

Suppose the _Mycroft Web Server_ is available at `https://123.456.78.90`.
It redirects requests from port `12389` to _Mycroft API_ and requests from `12381` to _Mycroft Auth_.

The contents of the `.env` file should look as follows:

```conf
REACT_APP_BACKEND_URL=https://123.456.78.90:12389
REACT_APP_AUTH_CLIENT=mycroft-website
REACT_APP_AUTH_REALM=mycroft
REACT_APP_TOKEN_URL=https://123.456.78.90:12381
```

## Test run _(optional)_

At this point you can test the website by running the command:

```sh
npm run dev
```

This will host the application on your local machine and, if possible, open it in an available browser.
You can also access the website by going to `http://localhost:3000`.

If the other services are running and your environment variables are set correctly, you should be able to create an account and use the system.

However, this isn't the final step, as your connection is not encrypted and the website is not optimized.
This step can be used for verifying the configuration.

## Final build

To generate an optimized build run the command:

```sh
npm run build
```

This will create a `build/` directory containing static files that can be served by [_Mycroft Web Server_](../mycroft-web-server).
