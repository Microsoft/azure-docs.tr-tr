---
title: Erişim kimlik bilgilerini güvenli bir şekilde depolayın
titleSuffix: Azure Data Science Virtual Machine
description: Veri Bilimi Sanal Makinesi erişim kimlik bilgilerini güvenli bir şekilde nasıl depolayacağınızı öğrenin. Yönetilen hizmet kimliklerini nasıl kullanacağınızı ve erişim kimlik bilgilerini depolamak için Azure Key Vault öğreneceksiniz.
keywords: derin öğrenme, AI, veri bilimi araçları, veri bilimi sanal makinesi, Jeo-uzamsal analiz, ekip veri bilimi işlemi
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: adc66fc794ac6cce3fc0f8fd1552f701a4968860
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519686"
---
# <a name="store-access-credentials-securely-on-an-azure-data-science-virtual-machine"></a>Erişim kimlik bilgilerini Azure Veri Bilimi Sanal Makinesi güvenli bir şekilde depolayın

Bulut uygulamalarındaki kodun, bulut hizmetlerinde kimlik doğrulaması için kimlik bilgilerini içermesi yaygındır. Bu kimlik bilgilerini yönetme ve güvenli hale getirme, bulut uygulamaları oluşturma konusunda iyi bilinen bir zorluk dır. İdeal olarak, kimlik bilgileri geliştirici iş istasyonlarında hiçbir şekilde gösterilmemelidir veya kaynak denetimine iade edilmelidir.

[Azure kaynakları için Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md) özelliği, azure hizmetlerine Azure Active Directory (Azure AD) içinde otomatik olarak yönetilen bir kimlik vererek bu sorunu daha kolay bir şekilde çözmenizi sağlar. Bu kimliği kullanarak, Azure AD kimlik doğrulamasını destekleyen tüm hizmetlerde kodunuzda kimlik bilgileri bulunmasına gerek kalmadan kimlik doğrulaması yapabilirsiniz.

Kimlik bilgilerini güvenli hale getirmenin bir yolu, gizli dizileri ve şifreleme anahtarlarını güvenli bir şekilde depolamak için yönetilen bir Azure hizmeti olan [Azure Key Vault](../../key-vault/index.yml)birlikte Wındows ınstaller (MSI) kullanmaktır. Yönetilen kimliği kullanarak bir anahtar kasasına erişebilir ve sonra anahtar kasasından yetkili gizli dizileri ve şifreleme anahtarlarını alın.

Azure kaynakları ve Key Vault için Yönetilen kimlikler hakkındaki belgeler, bu hizmetlerle ilgili ayrıntılı bilgi için kapsamlı bir kaynak içerir. Bu makalenin geri kalanında, Azure kaynaklarına erişmek için MSI ve Veri Bilimi Sanal Makinesi (DSVM) üzerinde Key Vault temel kullanımı gösterilmektedir. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>DSVM 'de yönetilen kimlik oluşturma

```azurecli-interactive
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```

## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>VM sorumlusuna Key Vault erişim izinleri atama

```azurecli-interactive
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>DSVM 'deki anahtar kasasındaki bir gizli anahtara erişme

```bash
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>DSVM 'den depolama anahtarlarına erişme

```bash
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```

## <a name="access-the-key-vault-from-python"></a>Python 'dan anahtar kasasına erişme

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
    credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
    key_vault_uri,  # Your key vault URL.
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Azure CLı 'dan anahtar kasasına erişme

```azurecli-interactive
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```