# ![Riata](/assets/RiataHat.png) Riata

Riata was designed for the wayfarer: someone who's never quite content with the financial tools or processes offered to them. Riata empowers the wayfarer to own their financial journey and build the tools they want.

### The finance app that lets you own your data and customize your experience

Built with [Plaid](https://plaid.com/) and the [Power Platform](https://www.microsoft.com/en-us/power-platform), Riata serves as a low-code, do-it-yourself starter kit. It comes packed with the fundamentals necessary to access your financial data and craft your own processes.

- Plaid is the API broker between you and your financial institutions. Riata allows you to quickly connect to Plaid and immediately retrieve the financial data you want.
- The Power Platform is your data store and workbench - Riata's low-code SDK. Within the Power Platform you can build your own:
  - data models
  - data reports and visuals
  - apps
  - flows/processes

### Packaged for stand-alone use, prepared for extensibility

Maybe you're not quite ready to forge your own path, but you want to explore what's possible. Riata is fully functional out-of-the-box, no additional development required. The included canvas app and cloud flows have all of the functionality (and more) you need to connect, visualize, and interact with your financial data.

Though Riata can stand on its own two-feet, its also designed with extensibility in mind. Extend, enhance, and customize Riata to your heart's content.

## What's Included

- Plaid custom connector with the essential endpoints
- Intuitive Dataverse schema
- Custom security role
- Mobile-first canvas app that can be used on any device (desktop included)
- Multiple plug-and-play cloud flows

## Security

### Built-In

- Riata uses a least-privilege security model. End users only have read access to core/sensitive records. CRUD operations on core/sensitive records are routed through cloud flows that execute minimum functionality needed for business logic.
- API keys are only retrieved at runtime and never stored in flow run histories or logs.
- The included webhook verifies all requests have a matching JWT key that originates from Plaid. (For extra webhook security, [create an Azure function](#optional))
- Upon user-initiated institution removal, all related data (transactions, accounts, tokens, etc) are permanently deleted from the environment. Only active institution data is stored.

### Optional

- [Tenant-level](https://admin.powerplatform.microsoft.com/security/overview)
  - Enable tenant isolation
  - Restrict IP Addresses (requires managed environments)
    - [Plaid's IP addresses](https://plaid.com/docs/api/webhooks/#configuring-webhooks)
  - Create a Data Loss Prevention (DLP) policy
  - Block guest access
- [Environment-level](https://admin.powerplatform.microsoft.com/manage/environments)
  - Create Entra-backed security groups
  - Dedicate/Isolate the `Riata` solution to it's own environment
- [Setup an Azure Key Vault](https://learn.microsoft.com/en-us/azure/key-vault/general/quick-create-portal). This will store your Plaid API keys that will be securely retrieved by Riata. If this is skipped, the key will be required as a plain text environment variable.
- [Create an Azure function](https://github.com/paradiseisfound/azure-functions). This will enhance the security of your webhook to block bad actors who might be clever enough to impersonate Plaid. If this is skipped, only basic webhook security will be employed.

## Requirements

- A Power Platform instance. If you're new to the Power Platform, you'll need to create an Azure tenant and provision a [Power Platform](https://www.microsoft.com/en-us/power-platform/try-free) environment with Dataverse enabled.
- A Plaid developer account. [Signup](https://dashboard.plaid.com/signup) is easy and free.

## Licensing and Pricing (as of 2026-01-14)

- The Plaid `Pay-as-You-Go` plan and Sandbox is free, but Production [pricing](https://plaid.com/pricing/) is usage based. Riata only uses the [Transaction](https://plaid.com/products/transactions/) product, which costs ~USD$0.30 per account per month.
- The Power Platform offers a free [developer license](https://www.microsoft.com/en-us/power-platform/products/power-apps/pricing) to get you started. To use Riata in a Production capacity, a Power Apps license with Dataverse use-rights is required. The Power Platform [licensing guide](https://cdn-dynmedia-1.microsoft.com/is/content/microsoftcorp/microsoft/bade/documents/products-and-services/en-us/bizapps/Power-Platform-Licensing-Guide-January-2026.pdf) can help you determine which license is best for you.
  - Some Microsoft and Dynamics licenses include Power Apps use-rights. If you have existing licenses in your Azure tenant, consult a licensing guide to determine if you already have Power Apps use-rights.
  - Assuming you need a dedicated Premium Power Apps license, the `per app pay-as-you-go meter` is the cheapest option at ~USD$10 per user, per app, per month.
- Riata requires its own subscription-based license for use. ~USD$5 per month, per environment.
  - Riata uses [Ianus Guard](https://www.ianusguard.com/), an [open-source](https://github.com/Ianua-Software/Ianus-Clients), Power Platform native license management tool.

Total cost example: (5 accounts x 0.30) + (1 user, 1 app x 10) + (1 environment x 5) = ~USD$16.50 per month

## Installation Guide

If you're installing this for this for the first time, follow these instructions carefully. Order matters. All steps are required.

1. Install [Ianus Guard](https://marketplace.microsoft.com/en-us/product/dynamics-365/ianuasoftware.ianus_guard?tab=Overview) in the same environment as you intend to install `Riata`.
2. Create connections ahead of solution import. Though you can create these connections during import, I have found that creating the connections ahead of time avoids the occasional solution import error.
   - ![Microsoft Dataverse](/assets/MicrosoftDataverse.png) Microsoft Dataverse
     - Use a service principle, a service account, or an administrator account that has the `System Administrator` role.
   - ![HTTP with Microsoft Entra ID (preauthorized)](/assets/HTTPwithMicrosoftEntraIDpreauthorized.png) HTTP with Microsoft Entra ID (preauthorized)
     - My recommendation is to use a service principle, a service account, or an administrator account that has the `System Administrator` role, which would use the `Log in with Microsoft Entra ID` authentication type. Provide the target environment's base URL for both the `Base Resource URL` and `Microsoft Entra ID Resource URI`.
       - Example: https://[custom subdomain].crm[optional digit].dynamics.com/
   - ![Power Apps Notification (v1)](/assets/PowerAppsNotificationsv1.png) Power Apps Notification (v1)
     - You'll have to provide placeholder text for the `Target Application` until Riata is installed.
3. Install `Riata`.
   1. Check the box: `Enable Plugin steps and flows included in the solution`
   2. The import wizard should recognize the connections from step three. If not, go back to step three and troubleshoot.
   3. The `Webhook URL` environment variable must be left blank until after install.
4. Create a connection for the ![Plaid](/assets/Plaid.jpg) Plaid custom connector.
5. Update the Power Apps Notification (v1) connection with the proper `Target Application` value, and use 'Riata' for the `Display Name`.
6. Navigating to the `Default Solution` in the same environment, update the Plaid connection reference with the connection created in step four.
7. Navigating back to the `Riata` solution, turn on:
   1. `Transaction Sync` flow.
   2. `Plaid Event Webhook` flow. 'Edit' the flow (but not really), so you can copy the URL of the webhook/trigger.
8. Navigate back to the `Default Solution` to update the `Webhook URL` environment variable with the URL you copied from the `Plaid Event Webhook` flow.
   - Use the `Current Value` field, _not_ the `Default Value`.
9. Turn on the remaining flows in the `Riata` solution.
   - You may optionally turn on `Scheduled Background Flows`, but a valid Power Automate license is required. If you leave this flow turned off, you can still manually trigger the background flows in the Power Apps/Automate portal, or via the `Riata` canvas app so long as the `Show Flow Action in App` environment variable is set to 'yes'.
10. Navigate to the `Ianus Guard` solution within the same environment. 'Edit' the `License` table (you'll be 'editing' the data, not the schema) and insert the provided license key into the `Key` field.

Installation complete!
