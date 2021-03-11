# Automated Domain Control Validation Renewal

This GitHub Action allows you to automatically (re)perform domain control validation using the Entrust API and your DNS provider.

## Config

| Environment variable            | Value                                                     |
|---------------------------------|-----------------------------------------------------------|
| `ENTRUST_API_CERTIFICATE`       | PEM encoded client certificate                            |
| `ENTRUST_API_PRIVATE_KEY`       | PEM encoded private key                                   |
| `ENTRUST_API_USERNAME`          | API Username                                              |
| `ENTRUST_API_PASSEWORD`         | API Password (API Key)                                    |
| `DURATION_BEFORE_EXPIRY`        | Optional, remaining validity before starting revalidation |

Each DNS provider has it's own set of environment variables

## Obtaining credentials

These credentials can be obtained via the Enterprise Portal, `Administration > Advanced Settings > API` and your account must be enabled for API usage.

# Example

```yaml
name: Domain Control Validation Renewal

on:
  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:
  
  # Run automatically, to check if domains should be renewed
  schedule:
    - cron: "22 * */7 * *"

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
