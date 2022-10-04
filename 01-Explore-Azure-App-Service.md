# Azure App Service

Azure App Service is an HTTP-based service for hosting web applications, REST APIs, and mobile back ends. It contains the following features:

- Built-in auto scale support, scaling resources up or down like ram and scaling the number of machine instances out or in.
- Continuous integration/deployment support with Azure DevOps, GitHub, Bitbucket, FTP, or a local Git repository on your development machine.
- Deployment slots, which are live apps with their own host names.
- App Service on Linux, it can also run custom Linux containersalso known as Web App for Containers

Limitations:

- App Service on Linux is not supported on Shared pricing tier.
- You can't mix Windows and Linux apps in the same App Service plan.
- Historically, you could not mix Windows and Linux apps in the same resource group. However, all resource groups created on or after January 21, 2021 do support this scenario. Support for resource groups created before January 21, 2021 will be rolled out across Azure regions (including National cloud regions) soon.
- The Azure portal shows only features that currently work for Linux apps. As features are enabled, they're activated on the portal.

## Azure App Service plans

- Region (West US, East US, etc.)
- Number of VM instances
- Size of VM instances (Small, Medium, Large)
- Pricing tier (Free, Shared, Basic, Standard, Premium, PremiumV2, PremiumV3, Isolated and Consumption for only function apps)

In the Free and Shared tiers, an app receives CPU minutes on a shared VM instance and can't scale out. In other tiers, an app runs, and scales as follows:

- An app runs on all the VM instances configured in the App Service plan.
- If multiple apps are in the same App Service plan, they all share the same VM instances.
- If you have multiple deployment slots for an app, all deployment slots also run on the same VM instances.
- If you enable diagnostic logs, perform backups, or run WebJobs, they also use CPU cycles and memory on these VM instances.

Isolate your app into a new App Service plan when:

- The app is resource-intensive.
- You want to scale the app independently from the other apps in the existing plan.
- The app needs resource in a different geographical region.

## Deploy to App Service

There is manual deployment and automated deployment. Automated deployment can be achieved with Azure DevOps, GitHub and Bitbucket.

For manual the following is needed:

- Git: App Service web apps feature a Git URL that you can add as a remote repository. Pushing to the remote repository will deploy your app.
- CLI: webapp up is a feature of the az command-line interface that packages your app and deploys it. Unlike other deployment methods, az webapp up can create a new App Service web app for you if you haven't already created one.
- Zip deploy: Use curl or a similar HTTP utility to send a ZIP of your application files to App Service.
- FTP/S: FTP or FTPS is a traditional way of pushing your code to many hosting environments, including App Service.

Whenever possible, use deployment slots when deploying a new production build. When using a Standard App Service Plan tier or better, you can deploy your app to a staging environment and then swap your staging and production slots. The swap operation warms up the necessary worker instances to match your production scale, thus eliminating downtime.

## Authentication and authorization in App Service

Azure App Service provides built-in authentication and authorization support, so you can sign in users and access data by writing minimal or no code in your web app, API, and mobile back end, and also Azure Functions.

Features:

- Azure App Service allows you to integrate a variety of auth capabilities into your web app or API without implementing them yourself.
- It’s built directly into the platform and doesn’t require any particular language, SDK, security expertise, or even any code to utilize.
- You can integrate with multiple login providers. For example, Azure AD, Facebook, Google, Twitter.

Identity providers:

| Provider                    | Sign-in endpoint              |
| :-------------------------- | :---------------------------- |
| Microsoft Identity Platform | /.auth/login/aad              |
| Facebook                    | /.auth/login/facebook         |
| Google                      | /.auth/login/google           |
| Twitter                     | /.auth/login/twitter          |
| Any OpenID Connect provider | /.auth/login/`<providerName>` |

How it works is that the authentication and authorization module runs in the same sandbox as your application code. When it's enabled, every incoming HTTP request passes through it before being handled by your application code. This module handles several things for your app:

- Authenticates users with the specified provider
- Validates, stores, and refreshes tokens
- Manages the authenticated session
- Injects identity information into request headers
- The authentication flow is the same for all providers, but differs depending on whether you want to sign in with the provider's SDK.

| Step                            | Without provider SDK                                                                             | With provider SDK                                                                                                                               |
| :------------------------------ | :----------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------- |
| Sign user in                    | Redirects client to /.auth/login/`<provider>`.                                                   | Client code signs user in directly with provider's SDK and receives an authentication token. For information, see the provider's documentation. |
| Post-authentication             | Provider redirects client to /.auth/login/`<provider>`/callback.                                 | Client code posts token from provider to /.auth/login/`<provider>` for validation.                                                              |
| Establish authenticated session | App Service adds authenticated cookie to response.                                               | App Service returns its own authentication token to client code.                                                                                |
| Serve authenticated content     | Client includes authentication cookie in subsequent requests (automatically handled by browser). | Client code presents authentication token in X-ZUMO-AUTH header (automatically handled by Mobile Apps client SDKs).                             |

In the Azure portal, you can configure App Service with a number of behaviors when an incoming request is not authenticated.

- Allow unauthenticated requests
- Require authentication

## App Service networking features

By default, apps hosted in App Service are accessible directly through the internet and can reach only internet-hosted endpoints. But for many applications, you need to control the inbound and outbound network traffic.

Azure App Service is a distributed system. The roles that handle incoming HTTP or HTTPS requests are called front ends. The roles that host the customer workload are called workers.

Inbound features

- App-assigned address
- Access restrictions
- Service endpoints
- Private endpoints

Outbound features

- Hybrid Connections
- Gateway-required virtual network integration
- Virtual network integration
