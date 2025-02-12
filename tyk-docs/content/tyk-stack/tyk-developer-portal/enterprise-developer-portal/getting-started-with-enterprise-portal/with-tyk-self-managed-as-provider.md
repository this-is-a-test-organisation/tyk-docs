---
title: "Connect to a provider (Tyk Self-Managed)"
date: 2022-02-08
tags: [""]
description: ""
menu:
  main:
    parent: "Getting Started With Enterprise Portal"
weight: 1
---

{{< note success >}}
**Tyk Enterprise Developer Portal**

If you are interested in getting access contact us at [support@tyk.io](<mailto:support@tyk.io?subject=Tyk Enterprise Portal Beta>)

{{< /note >}}

## Introduction

The first step in getting started with the developer portal is to connect the portal to a provider. The current provider supported is Tyk Self Managed, although you can still use multiple instances of Tyk Self Managed. When the connection is established, it will import policies as API Products to the portal. The 'Getting started guide' part shows you how to set up a policy and import it to the developer portal.

{{< youtube 8KJSVACD-j4 >}}

## Prerequisites

- A Tyk Self-Managed [installation]({{< ref "/content/tyk-self-managed/install.md" >}})
- The Enterprise portal installed
- A login for the portal admin app

## Connect to a provider (Tyk Self-Managed)

1. Go to the provider section in the portal admin dashboard
2. Click Add provider
3. Add your provider details

| Field                    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Name                     | This is an internal reference to the provider.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Provider type (disabled) | This refers to the type of provider; however, the only supported provider at this stage is Tyk Self-Managed.                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| URL                      | The URL refers to the provider host URL for your Tyk Self-Managed installation. Within the Tyk instance, the URL can be simply copied.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Gateway URL              | The gateway URL refers to the URL that the portal developers will send the queries and use the access credentials.                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Secret                   | The Secret can be fetched from the Tyk Self-Managed / Tyk analytics dashboard. The procedure is as follows:  Go to the Tyk Dashboard. Navigate to Users. Select a user with the permissions you want to bring on to the portal. You can find the secret under API Access Credentials. (Optional) You can find the organisation id  under Organisation ID if your use case requires. Note: The Portal will share the same permissions that the user selected to provide the secret.                              |
| Organisation ID          | The org id is required in order to connect to your installation as a provider. It can be found in the user profile within the Tyk Dashboard.                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Policy tags              | This is an optional field that can be used to define which policies from Tyk will be imported to the portal. If a tag is defined here, it needs to also be defined in the Policy section in Tyk (described in the next journey). If this field is left empty in both this provider section and in the policies within Tyk, then all policies will be imported from the Tyk instance. How to include the label in the policy section inside Tyk will be explained in the Publish API Products and plans to the public-facing portal. |

4. Save your changes

### How to find the Secret and Org ID inside your Tyk Dashboard

1.  Select **Users** from the **System Management** section.
2.  In the users list, click **Edit** for your user.
3.  The Secret is the **Tyk Dashboard API Access Credentials**. The **Organisation ID** is underneath **Reset key**. {{< img src="/img/2.10/user_api_id.png" alt="API key location" >}}
4.  Select **APIs** from the **System Management** section.
5.  From the **Actions** menu for your API, select Copy API ID
