---
title: Öğretici-Azure Key Vault gizli dizileri depolamak için bir Linux sanal makinesi ve Python uygulaması kullanın | Microsoft Docs
description: Bu öğreticide, Azure Key Vault bir gizli dizi okumak için bir Python uygulamasını nasıl yapılandıracağınızı öğreneceksiniz.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 15650de776b481d1635b58f2b8ecf2bf2921d12f
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242407"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Öğretici: Azure Key Vault içinde gizli dizileri depolamak için bir Linux sanal makinesi ve Python uygulaması kullanma

Azure Key Vault, uygulamalarınıza, hizmetlerinize ve BT kaynaklarınıza erişmek için gereken API anahtarları ve veritabanı bağlantı dizeleri gibi gizli dizileri korumanıza yardımcı olur.

Bu öğreticide, Azure kaynakları için Yönetilen kimlikler kullanarak Azure Key Vault bilgi okumak üzere bir Azure Web uygulaması ayarlarsınız. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Bir anahtar kasası oluşturma
> * Anahtar kasanıza gizli dizi depolayın
> * Linux sanal makinesi oluşturma
> * Sanal makine için [yönetilen kimliği](../active-directory/managed-identities-azure-resources/overview.md) etkinleştir
> * Anahtar kasasından verileri okumak için konsol uygulaması için gerekli izinleri verme
> * Anahtar kasanızdan gizli dizi alma

Daha sonra devam etmeden önce [Key Vault temel kavramlarını](basic-concepts.md)anladığınızdan emin olun.

## <a name="prerequisites"></a>Önkoşullar

* [Git](https://git-scm.com/downloads).
* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
* [Azure CLI sürüm 2.0.4 veya üzeri](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ya da Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Yönetilen Hizmet Kimliği anlayın

Azure Key Vault, kimlik bilgilerini kodunuzda olmayan güvenli bir şekilde saklayabilir. Bunları almak için Azure Key Vault kimlik doğrulaması yapmanız gerekir. Ancak, Key Vault kimlik doğrulaması yapmak için bir kimlik bilgisi gerekir. Bu, klasik bir önyükleme sorunudur. Azure ve Azure Active Directory (Azure AD) aracılığıyla, Yönetilen Hizmet Kimliği (MSI), işleri daha kolay hale getiren bir önyükleme kimliği sağlar.

Sanal makineler, App Service veya Işlevler gibi bir Azure hizmeti için MSI 'yi etkinleştirdiğinizde Azure, Azure AD 'de hizmet örneği için bir hizmet sorumlusu oluşturur. Hizmet sorumlusunun kimlik bilgilerini hizmet örneğine çıkartır.

![MSI](media/MSI.png)

Daha sonra, kodunuz, bir erişim belirteci almak için Azure kaynağında bulunan bir yerel meta veri hizmetini çağırır. Kodunuz, Azure Key Vault bir hizmette kimlik doğrulamak için yerel MSI uç noktasından aldığı erişim belirtecini kullanır.

## <a name="sign-in-to-azure"></a>Azure'da oturum açın

Azure CLı kullanarak Azure 'da oturum açmak için şunu girin:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki kodla Batı ABD konumundaki `az group create` komutunu kullanarak bir kaynak grubu oluşturun. `YourResourceGroupName` yerine istediğiniz adı koyun.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Bu kaynak grubunu öğretici genelinde kullanırsınız.

## <a name="create-a-key-vault"></a>Bir anahtar kasası oluşturma

Ardından, önceki adımda oluşturduğunuz kaynak grubunda bir Anahtar Kasası oluşturursunuz. Şu bilgileri belirtin:

* Anahtar Kasası adı: ad 3-24 karakter uzunluğunda olmalıdır ve yalnızca 0-9, a-z, A-Z ve kısa çizgi (-) içermelidir.
* Kaynak grubu adı.
* Konum: **West US**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Bu noktada Azure hesabınız, bu yeni anahtar kasasında herhangi bir işlemi gerçekleştirmeye yetkili olan tek hesaptır.

## <a name="add-a-secret-to-the-key-vault"></a>Anahtar kasasına gizli dizi ekleme

Bunun nasıl çalıştığını göstermemize yardımcı olması için bir gizli dizi ekliyoruz. Hem güvenli hem de uygulamanızda kullanılabilir olması gereken herhangi bir SQL bağlantı dizesini veya diğer bilgileri depolamak isteyebilirsiniz.

Anahtar kasasında *AppSecret* adlı bir gizli dizi oluşturmak için aşağıdaki komutları yazın. Bu gizli dizi **MySecret** değerini depolar.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Linux sanal makinesi oluşturma

`az vm create` komutunu kullanarak bir VM oluşturun.

Aşağıdaki örnek, **myVM** adlı bir VM oluşturur ve **azureuser** adlı bir kullanıcı hesabı ekler. `--generate-ssh-keys` parametresi otomatik olarak bir SSH anahtarı oluşturur ve varsayılan anahtar konumuna ( **~/vb SSH**) koyar. Bunun yerine belirli bir anahtar kümesi oluşturmak için `--ssh-key-value` seçeneğini kullanın.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM’yi ve destekleyici kaynakları oluşturmak birkaç dakika sürer. Aşağıdaki örnek çıktı, VM oluşturma işleminin başarılı olduğunu göstermektedir:

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

SANAL makinenizin çıktısında kendi `publicIpAddress` bir bölümünü bir yere iade edin. Bu adresi sonraki adımlarda VM 'ye erişmek için kullanacaksınız.

## <a name="assign-an-identity-to-the-vm"></a>VM 'ye bir kimlik atama

Aşağıdaki komutu çalıştırarak sanal makineye sistem tarafından atanan bir kimlik oluşturun:

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Komutun çıktısı aşağıdaki gibidir.

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

`systemAssignedIdentity`bir kopyasını alın. Sonraki adımda kullanacaksınız.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Key Vault için VM kimliği izni verin

Artık oluşturduğunuz kimliğe Key Vault izin verebilirsiniz. Şu komutu çalıştırın:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>VM 'de oturum açma

Bir Terminal kullanarak sanal makinede oturum açın.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>SANAL makineye Python kitaplığı 'nı yükler

HTTP GET çağrısı yapmak için [istekleri](https://pypi.org/project/requests/2.7.0/) Python Kitaplığı ' nı indirip yükleyin.

## <a name="create-edit-and-run-the-sample-python-app"></a>Örnek Python uygulaması oluşturma, düzenleme ve çalıştırma

**Sample.py**adlı bir Python dosyası oluşturun.

Sample.py açın ve aşağıdaki kodu içerecek şekilde düzenleyin:

```python
# importing the requests library
import requests

# Step 1: Fetch an access token from an MSI-enabled Azure resource      
# Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"})

# Extracting data in JSON format 
# This request gets an access token from Azure Active Directory by using the local MSI endpoint
data = r.json()

# Step 2: Pass the access token received from the previous HTTP GET call to the key vault
KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})

print(kvSecret.json()["value"])
```

Yukarıdaki kod iki adımlı bir işlem gerçekleştirir:

   1. VM 'deki yerel MSI uç noktasından bir belirteç getirir. Uç nokta daha sonra Azure Active Directory bir belirteç getirir.
   1. Belirteci anahtar kasasına geçirir ve gizli dizinizi getirir.

Aşağıdaki komutu çalıştırın. Gizli değeri görmeniz gerekir.

```console
python Sample.py
```

Bu öğreticide, Azure Key Vault Linux sanal makinesinde çalışan bir Python uygulamasıyla nasıl kullanacağınızı öğrendiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç kalmadığında kaynak grubunu, sanal makineyi ve tüm ilgili kaynakları silin. Bunu yapmak için VM 'nin kaynak grubunu seçin ve **Sil**' i seçin.

`az keyvault delete` komutunu kullanarak anahtar kasasını silin:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
