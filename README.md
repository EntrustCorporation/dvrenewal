# Automated Domain Control Validation Renewal

This GitHub Action allows you to automatically (re)perform domain control validation using the Entrust API and your DNS provider.

**This project is made available by Entrust Corporation under the [MIT license](LICENSE).** As such the GitHub Action is provided to you AS IS and without warranty, as further detailed in the MIT license.
## Config

| Environment variable            | Value                                                     |
|---------------------------------|-----------------------------------------------------------|
| `DNS_PROVIDER`                  | The name of your default DNS provider                     |
| `ENTRUST_API_CERTIFICATE`       | PEM encoded client certificate                            |
| `ENTRUST_API_PRIVATE_KEY`       | PEM encoded private key                                   |
| `ENTRUST_API_USERNAME`          | API Username                                              |
| `ENTRUST_API_PASSEWORD`         | API Password (API Key)                                    |
| `DURATION_BEFORE_EXPIRY`        | Optional, remaining validity before starting revalidation |

Each DNS provider has its own set of environment variables.

## Obtaining credentials

### Entrust

These credentials can be obtained via the Entrust Certificate Services Enterprise Portal (Enterprise Portal, Administration > Advanced Settings > API). In order to obtain an account and related credentials, you must have an active subscription or other entitlement from Entrust for Entrust Certificate Services (ECS).

### DNS Provider(s)

This project is utilizing the DNS providers of the LEGO ACME client; documentation can be found here:
https://go-acme.github.io/lego/dns/#dns-providers

In addition to the default DNS provider specified in `DNS_PROVIDER`, alternative DNS providers can be specified on a per domain basis by setting the environment variable `DNS_PROVIDER_example_com` where the suffix `example_com` stands for `example.com` and can be replaced by any domain in your account.

# Security

Both the Entrust and the DNS provider credentials provide strong entitlements and can have a devastating impact to your organisation or service when not secured properly. It’s your sole responsibility to store these credentials securely and restrict API credentials to the resources and capabilities this tool requires.

**Do not put any credential directly in your configuration file!**

# Example

```yaml
name: Domain Control Validation Renewal

on:
  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:
  
  # Run automatically, to check if domains should be renewed
  schedule:
    #         ┌───────────── minute (0 - 59)
    #         │ ┌───────────── hour (0 - 23)
    #         │ │  ┌───────────── day of the month (1 - 31)
    #         │ │  │  ┌───────────── month (1 - 12)
    #         │ │  │  │ ┌───────────── day of the week (0 - 6) (Sunday to Saturday)
    #         │ │  │  │ │
    #         │ │  │  │ │
    #         │ │  │  │ │
    - cron: "22 8 */7 * *"

jobs:
  renew:
    name: Renew
    runs-on: ubuntu-latest

    steps:
      - uses: digitorus/dvrenewal@main
        with:
          DNS_PROVIDER: cloudflare
          CLOUDFLARE_DNS_API_TOKEN: ${{ secrets.CLOUDFLARE_DNS_API_TOKEN }}
          ENTRUST_API_CERTIFICATE: ${{ secrets.ENTRUST_API_CERTIFICATE }}
          ENTRUST_API_PRIVATE_KEY: ${{ secrets.ENTRUST_API_PRIVATE_KEY }}
          ENTRUST_API_USERNAME: ${{ secrets.ENTRUST_API_USERNAME }}
          ENTRUST_API_PASSEWORD: ${{ secrets.ENTRUST_API_PASSEWORD }}

          # Uncomment the setting below to renew early, 6 months in this example.
          # DURATION_BEFORE_EXPIRY: 4380h

```

