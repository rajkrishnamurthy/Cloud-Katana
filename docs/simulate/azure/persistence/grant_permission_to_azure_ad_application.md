---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.14.1
  kernelspec:
    language: python
---

# Grant Permission to Azure AD Application


## Metadata



|                   |    |
|:------------------|:---|
| platform          | Azure |
| contributors      | Roberto Rodriguez @Cyb3rWard0g,MSTIC R&D |
| creation date     | 2021-08-05 |
| modification date | 2021-08-09 |
| Tactics           | [TA0003](https://attack.mitre.org/tactics/TA0003) |
| Techniques        | [T1098](https://attack.mitre.org/techniques/T1098) |


## Description
A threat actor might want to grant permissions (Delegated or Application) to an Azure AD application (Service Principal) via Microsoft Graph APIs and the right permissions.



## Run Simulation


### Get OAuth Access Token

```python
from msal import PublicClientApplication
import requests
import time

function_app_url = "https://FUNCTION_APP_NAME.azurewebsites.net"

tenant_id = "TENANT_ID"
public_client_app_id = "KATANA_CLIENT_APP_ID"
server_app_id_uri = "api://" + tenant_id + "/cloudkatana"
scope = server_app_id_uri + "/user_impersonation"

app = PublicClientApplication(
    public_client_app_id,
    authority="https://login.microsoftonline.com/" + tenant_id
)
result = app.acquire_token_interactive(scopes=[scope])
bearer_token = result['access_token']
```

### Set Azure Function Orchestrator

```python
endpoint = function_app_url + "/api/orchestrators/Orchestrator"
```

### Prepare HTTP Body

```python
data = [{'RequestId': '52932ea9-e4a4-4b73-8287-70b1a86923af', 'name': 'Grant Permission to Azure AD Application', 'metadata': {'creationDate': '2021-08-05', 'modificationDate': '2021-08-09', 'description': 'A threat actor might want to grant permissions (Delegated or Application) to an Azure AD application (Service Principal) via Microsoft Graph APIs and the right permissions.\n', 'contributors': ['Roberto Rodriguez @Cyb3rWard0g', 'MSTIC R&D'], 'mitreAttack': [{'technique': 'T1098', 'tactics': ['TA0003']}]}, 'steps': [{'schema': 'atomic', 'id': '0721f7ce-f04b-4bdd-9fd1-aefc566aa0fb', 'name': 'Grant Permission to Azure AD Application', 'metadata': {'creationDate': '2021-08-05', 'modificationDate': '2021-08-09', 'description': 'A threat actor might want to grant permissions (Delegated or Application) to an Azure AD application (Service Principal) via Microsoft Graph APIs and the right permissions.\n', 'contributors': ['Roberto Rodriguez @Cyb3rWard0g', 'MSTIC R&D'], 'mitreAttack': [{'technique': 'T1098', 'tactics': ['TA0003']}]}, 'authorization': [{'resource': 'https://graph.microsoft.com/', 'permissionsType': 'application', 'permissions': ['AppRoleAssignment.ReadWrite.All', 'DelegatedPermissionGrant.ReadWrite.All']}], 'execution': {'type': 'ScriptModule', 'platform': 'Azure', 'executor': 'PowerShell', 'module': {'name': 'CloudKatanaAbilities', 'version': 1.0, 'function': 'Grant-CKPermissions'}, 'parameters': {}}, 'file_name': 'grant_permission_to_azure_ad_application', 'number': 1}]}]
```

### Send HTTP Request

```python
http_headers = {'Authorization': 'Bearer ' + bearer_token, 'Accept': 'application/json','Content-Type': 'application/json'}
results = requests.get(endpoint, json=data, headers=http_headers, stream=False).json()

time.sleep(30)
```

### Explore Output

```python
query_status = requests.get(results['statusQueryGetUri'], headers=http_headers, stream=False).json()
query_results = query_status['output']
query_results
```
