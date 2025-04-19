# azure-artifacts-devops-demo

**Basic to advanced overview of Azure Artifacts** with sample code and usage examples in CI/CD using **Azure Pipelines**. Azure Artifacts lets you create, host, and share packages (NuGet, npm, Maven, Python, Universal) from public and private sources.

---

## 📦 **Azure Artifacts – Topics Covered**

### ✅ **1. Basic Setup: Enable Azure Artifacts**
No code needed – just enable it in your **Azure DevOps Project** via the Artifacts tab.

---

### Install extension
```
az login
az extension add --name azure-devops
az extension update --name azure-devops
brew update && brew upgrade azure-cli
az devops configure --defaults organization=https://dev.azure.com/YOUR_ORG project=YOUR_PROJECT
```
```
az devops configure --defaults \
  organization=https://dev.azure.com/CloudnauticPune \
  project=project
```

### ✅ **2. Create a Feed**
# Azure CLI - Create a new feed
```bash
az artifacts feed create --name my-feed
az artifacts feed create --name my-feed --description "My private feed"
az artifacts feed create --name my-feed --debug
```

---

### ✅ **3. Publish a Universal Package**
```bash
# Install Azure DevOps Extension
az extension add --name azure-devops

# Publish a file/folder as Universal Package
az artifacts universal publish \
  --organization https://dev.azure.com/YOUR_ORG \
  --feed my-feed \
  --name demo-package \
  --version 1.0.0 \
  --description "My sample universal package" \
  --path ./my-artifact-folder
```

---

### ✅ **4. Consume Universal Package in Azure Pipeline**
```yaml
# azure-pipelines.yml
steps:
  - task: UniversalPackages@0
    inputs:
      command: 'download'
      downloadDirectory: '$(Pipeline.Workspace)'
      feedsToUse: 'internal'
      vstsFeed: '<FEED_GUID>'
      packageName: 'demo-package'
      packageVersion: '1.0.0'
```

---

### ✅ **5. Publish NuGet Package to Azure Artifacts**
```yaml
# azure-pipelines.yml
steps:
  - task: NuGetCommand@2
    inputs:
      command: 'push'
      packagesToPush: '**/*.nupkg'
      publishVstsFeed: '<FEED_GUID>'
```

---

### ✅ **6. Publish npm Package to Azure Artifacts**
```yaml
# .npmrc (place in repo root)
registry=https://pkgs.dev.azure.com/YOUR_ORG/_packaging/YOUR_FEED/npm/registry/
always-auth=true

# azure-pipelines.yml
steps:
  - script: |
      npm install
      npm publish
    env:
      NODE_AUTH_TOKEN: $(System.AccessToken)
```

---

### ✅ **7. Publish Python Package**
```bash
# Upload to feed
twine upload --repository-url https://pkgs.dev.azure.com/YOUR_ORG/_packaging/YOUR_FEED/pypi/upload dist/*
```

---

### ✅ **8. Secure Feeds with Permissions**
- Navigate to **Project → Artifacts → Feed → Permissions**.
- Assign roles like Reader, Contributor, or Owner to users or groups.

---

### ✅ **9. Retention Policies & Clean-Up**
```bash
# Set retention policy
az artifacts universal retention-policy set \
  --feed my-feed \
  --days 30
```

---

### ✅ **10. Use Azure Artifacts with GitHub Actions**
```yaml
# .github/workflows/npm.yml
- name: Set up .npmrc
  run: |
    echo "//pkgs.dev.azure.com/YOUR_ORG/_packaging/YOUR_FEED/npm/registry/:_authToken=${{ secrets.AZURE_TOKEN }}" > ~/.npmrc
- name: Publish npm
  run: npm publish
```

---

## 📁 Suggested GitHub Repo Name
`azure-artifacts-devops-demo`

Would you like me to generate a full sample repo structure for you with YAML, `.npmrc`, `.pip`, `.csproj`, or any specific language/package manager?
