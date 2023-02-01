original tweet: https://twitter.com/johnterickson/status/1455961957698183173

The goal of this repository is demostrate how to use GitHub Actions' OpenID Connect support (https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure) and Azure DevOp's upcoming Managed Service Identity support (no link yet) to be able to securely authenticate to Azure DevOps without any secrets.

The gist of it is:
1. Create a service principal with a GitHub federated credential.  In this example, Actions running in my repo on the `main` branch can impersonate this service principal.
![service principal](/images/service-principal.png)
2. Carefully, give this service principal access to resources in AzDO.
![azdo](/images/azdo-access.png)
3. In Actions, add `id-token: write` (see https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure#adding-permissions-settings)
4. In Actions, use `azure/login@v1` to login as the service principal.
5. In Actions, run `az account get-access-token --resource 499b84ac-1321-427f-aa17-267ca6975798 | jq -r .accessToken` to get an access token.  That GUID is "Azure DevOps".
6. This access token can be used anywhere `Bearer` or `Basic` auth is accepeted (i.e. anywhere a PAT is accepted).  See [the CI for examples](/.github/workflows/ci.yml)
