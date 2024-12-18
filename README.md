# Maurisource Magento Logs Integration

## Overview

This integration allows Magento (Adobe Commerce) websites to send log data directly to Datadog, enabling centralized monitoring and enhanced observability. The integration can either make use of the Datadog API or Datadog Agent. Merchants can monitor their website's logs in Datadog instead of painfully digging into `var/log` folder. The Integration consolidates all available logs into a single source and then sends it to Datadag based on severity levels. This limits any manual action on a production environment, eliminating any risks associated with it.

## Setup

### Install the Datadog Logs Module

#### Prerequisites
- Ensure your Adobe Commerce or Magento marketpace account has the [Datadog Logs Integration][7] added to it. Your developers can pull the package using the Composer steps later on.
- A Datadog account with an API Key is required. Create an API Key from your [Datadog account][8] if you don’t already have one.

#### Installation Steps
1. Navigate to the Magento root directory. Access your Magento root directory through the CLI.
2. Install the module using Composer with the following command:
   ```shell
   composer require maurisource/datadog
   ```

3. After the installation is complete, enable the module by running:
   ```shell
   bin/magento module:enable Maurisource_Datadog
   ```

4. Update the Magento system to recognize the new module:
   ```shell
   bin/magento setup:upgrade
   ```

5. If your store is in production mode, deploy static content:
   ```shell
   bin/magento setup:static-content:deploy
   ```

6. Clear the Magento cache to ensure the changes take effect:
   ```shell
   bin/magento cache:flush
   ```

#### Configuration in Adobe Commerce Admin
1. Log in to the **Adobe Commerce Admin Panel**.
2. Navigate to **Stores > Configuration > Maurisource > Datadog**.
3. Set the following fields:
   - **Datadog Logs Enabled**: Yes.
   - **Debug Enabled**: As per your needs (Yes or No).
   - **Save Datadog Logs In**: Choose "DB" if using API or "File" if Datadog Agent is installed.
   - **Minimum Level**: Set the log severity (for example, DEBUG, WARNING, or ERROR).
   - **Account Region**: Choose your Datadog region (for example, United States or Europe).
   - **API Key**: Paste the API Key from your Datadog account.

4. Save the configuration and monitor logs directly in your Datadog dashboard.

## Determine environment type
- For Adobe Commerce environments **without Datadog Agent**: Set **Save Datadog Logs In** to "DB" because third-party services cannot run on the environment. This option makes use of the **Datadog API** to transmit logs. No further configuration is needed.
- For on-premises setups **with Datadog Agent**: Set **Save Datadog Logs In** to "File". This creates a centralized `DatadogLogger.log` file in the `var/log` folder. Follow the next section to configure the agent to collect logs from that file.

### Configure the Datadog Agent to Collect Logs

_Available for Agent versions >6.0_

#### Host

1. Collecting logs is disabled by default in the Datadog Agent, enable it in your `datadog.yaml` file:

   ```yaml
   logs_enabled: true
   ```

2. Add this configuration block to your `maurisource_mangento.d/conf.yaml` file to start collecting your Magento Logs:

   ```yaml
   logs:
     - type: file
       path: /var/log/DatadogLogger.log
       service: 'maurisource_magento'
       source: maurisource_mangento_logs
   ```

3. [Restart the Agent][1].

#### Container

For containerized environments, see [Container Monitoring][6] for guidance.  
Select your containerized environment and follow the **Log Collection** instructions.

## Uninstallation

To uninstall this integration from Datadog, click the **Uninstall Integration** button.

## Support

For support contact Maurisource at:

- **Email**: [info@maurisource.com][2]  
- **Website**: [https://maurisource.com/][3]

## Troubleshooting

Need help? Open a support ticket [Maurisource support][2].

[1]: https://docs.datadoghq.com/agent/guide/agent-commands/#start-stop-and-restart-the-agent  
[2]: mailto:datadog@maurisource.zendesk.com  
[3]: https://maurisource.com  
[6]: https://docs.datadoghq.com/agent/logs/?tab=containerizedenvironment
[7]: https://commercemarketplace.adobe.com/maurisource-datadog.html
[8]: https://app.datadoghq.com/organization-settings/api-keys
