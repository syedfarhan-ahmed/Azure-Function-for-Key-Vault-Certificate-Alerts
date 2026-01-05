# Azure Key Vault Certificate Expiry Monitor

This Azure Function (PowerShell) automatically monitors certificates in an Azure Key Vault and sends email alerts via SendGrid if certificates are approaching expiration. It is designed to run on a daily schedule.

## 🚀 Features

* **Automated Daily Checks:** Runs on a timer trigger (default: Daily at 14:30 PM UTC).
* **Tiered Alerting:**
    * **CRITICAL (High Priority):** Expires in **≤ 7 days**.
    * **ERROR:** Expires in **< 15 days**.
    * **WARNING:** Expires in **< 30 days**.
* **Smart Notification:**
    * Sends formatted HTML emails with a summary table.
    * Supports multiple **To** and **Bcc** recipients.
    * Automatically parses "Sender Name" and "Email" from the configuration.
* **Secure:** Uses **Managed Identity** for Key Vault access (no hardcoded credentials).

## 📋 Prerequisites

1.  **Azure Function App:**
    * Runtime stack: **PowerShell Core**.
    * System Assigned Managed Identity: **Enabled**.
2.  **Azure Key Vault:**
    * Must contain the certificates to monitor.
    * **Access Policy:** The Function App's Managed Identity must have **Get** and **List** permissions for *Certificates*.
3.  **SendGrid Account:**
    * An API Key with "Mail Send" permissions.
    * A verified Sender Identity.

## ⚙️ Configuration (App Settings)

The function relies on Environment Variables. Navigate to your **Function App > Settings > Environment Variables** and add the following:

| Setting Name | Value Example | Description |
| :--- | :--- | :--- |
| `VaultName` | `my-prod-kv` | The name of your Azure Key Vault. |
| `SendGridApiKey` | `SG.xyz...` | Your SendGrid API Key. |
| `FromEmail` | `Alerts <no-reply@domain.com>` | The sender address. Supports `"Name <email>"` or just `"email"`. |
| `ToEmailList` | `admin@domain.com, dev@domain.com` | Comma-separated list of primary recipients. |
| `BccEmailList` | `audit@domain.com` | (Optional) Comma-separated list of BCC recipients. |

## 📦 Deployment

### Option 1: VS Code (Recommended)
1.  Open this folder in VS Code.
2.  Install the **Azure Functions** extension.
3.  Click the Azure icon -> **Workspace** -> **Deploy to Function App**.
4.  Select your subscription and Function App.

### Option 2: Azure Portal
1.  Create a new Function App.
2.  Create a function named `CertificateExpiryCheck` using the "Timer Trigger" template.
3.  Copy the content of `run.ps1` into the portal editor.
4.  Create a file named `requirements.psd1` in the "App Files" section and add the `Az` module dependency.

## 🛠️ Local Development

To run this locally:
1.  Create a `local.settings.json` file in the root (this is ignored by Git).
2.  Add your values:
    ```json
    {
      "IsEncrypted": false,
      "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "FUNCTIONS_WORKER_RUNTIME": "powershell",
        "VaultName": "your-kv-name",
        "SendGridApiKey": "your-key",
        "FromEmail": "me@test.com",
        "ToEmailList": "me@test.com",
        "BccEmailList": ""
      }
    }
    ```
3.  You must be logged into Azure CLI (`az login`) with an account that has access to the Key Vault.

## 📝 License

Owned by Syed Farhan Ahmed
