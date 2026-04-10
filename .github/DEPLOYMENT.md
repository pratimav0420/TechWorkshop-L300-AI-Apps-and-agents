# GitHub Actions Deployment Setup

This repository includes a GitHub Actions workflow (`deploy-to-azure.yml`) that automatically builds and deploys the application to Azure Container App when code is pushed to the main branch.

## Required GitHub Secrets

To use this workflow, you need to configure the following secrets in your GitHub repository:

### 1. Azure Container Registry Authentication
- **`ACR_USERNAME`** - Azure Container Registry username (service principal name or admin username)
- **`ACR_PASSWORD`** - Azure Container Registry password (service principal password or admin password)

To get ACR credentials, you can either:

**Option A: Use Admin User (Easiest)**
```bash
# Enable admin user on your ACR
az acr update --name mss7d64x723x4cosureg --admin-enabled true

# Get admin credentials
az acr credential show --name mss7d64x723x4cosureg
```

**Option B: Create Service Principal**
```bash
# Create service principal for ACR
az ad sp create-for-rbac --name "acr-github-actions" --role AcrPush \
  --scopes /subscriptions/{subscription-id}/resourceGroups/agentappsprval0409/providers/Microsoft.ContainerRegistry/registries/mss7d64x723x4cosureg
```

### 2. Azure Container App Deployment
- **`AZURE_CREDENTIALS`** - Azure service principal credentials in JSON format for Container App updates

To create Azure credentials:
```bash
az ad sp create-for-rbac --name "github-actions-sp" --role contributor \
  --scopes /subscriptions/{subscription-id}/resourceGroups/agentappsprval0409 \
  --sdk-auth
```

**Note**: Environment variables (FOUNDRY_ENDPOINT, gpt_endpoint, etc.) are already configured on your Container App in Azure, so no additional secrets are needed for those.

## How to Add Secrets

1. Go to your GitHub repository
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Add the following secrets:
   - `ACR_USERNAME` - Your Azure Container Registry username
   - `ACR_PASSWORD` - Your Azure Container Registry password  
   - `AZURE_CREDENTIALS` - Azure service principal JSON for Container App deployment

## Workflow Features

- ✅ Triggers on push to `main` branch
- ✅ Can be manually triggered via GitHub UI
- ✅ Authenticates to ACR using username/password
- ✅ Builds Docker image from `src/` folder only
- ✅ Uses commit SHA (first 7 characters) as image tag
- ✅ Pushes to Azure Container Registry: `mss7d64x723x4cosureg`
- ✅ Updates Azure Container App: `mss7d64x723x4-app` with new image
- ✅ Uses pre-configured environment variables on Container App
- ✅ Shows deployment URL and image tag in workflow output

## Manual Deployment

You can also trigger the deployment manually:
1. Go to **Actions** tab in your repository
2. Select **Build and Deploy to Azure Container App** workflow
3. Click **Run workflow** and select the `main` branch