# Installation Guide

### First Install

If you're installing Riata for this for the first time, follow these instructions carefully. Order matters. All steps are required.

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
10. 'Edit' the `License` table (you'll be 'editing' the data, not the schema) and insert the provided license key into the `Key` field.
11. Assign the `Riata User` security role and `Riata Column Security Profile` to either a team of users or individual users through the [admin center](https://admin.powerplatform.microsoft.com/manage/environments).

Installation complete!
