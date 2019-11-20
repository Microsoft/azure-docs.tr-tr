---
title: Öğretici-Python 'da Windows sanal makinesiyle Azure Key Vault kullanma | Microsoft Docs
description: Bu öğreticide, anahtar kasaınızdan gizli dizi okumak için bir ASP.NET Core uygulaması yapılandırırsınız.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 52791719e518f6a6b1a4178d8b9a010f1db14fc4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003853"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Öğretici: Python 'da Windows sanal makinesiyle Azure Key Vault kullanma

Azure Key Vault, API anahtarları, uygulamalarınıza, hizmetlerinize ve BT kaynaklarınıza erişmeniz gereken veritabanı bağlantı dizeleri gibi gizli dizileri korumanıza yardımcı olur.

Bu öğreticide, Azure Key Vault bilgileri okumak için bir konsol uygulamasının nasıl alınacağını öğreneceksiniz. Bunu yapmak için Azure kaynakları için Yönetilen kimlikler kullanırsınız. 

Öğretici şunların nasıl yapıldığını göstermektedir:

> [!div class="checklist"]
> * Bir anahtar kasası oluşturma.
> * Anahtar kasasına gizli dizi ekleyin.
> * Anahtar kasasından bir gizli dizi alma.
> * Bir Azure sanal makinesi oluşturun.
> * Yönetilen bir kimliği etkinleştirin.
> * VM kimliğine izin atayın.

Başlamadan önce [temel kavramları Key Vault](basic-concepts.md)okuyun. 

Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Windows, Mac ve Linux için:
  * [Git](https://git-scm.com/downloads)
  * Bu öğretici, Azure CLı 'yı yerel olarak çalıştırmanızı gerektirir. Azure CLı sürüm 2.0.4 veya daha yeni bir sürümün yüklü olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. CLI’yı yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 2.0’ı yükleme](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Yönetilen Hizmet Kimliği hakkında

Azure Key Vault, kimlik bilgilerini güvenli bir şekilde depolar, bu nedenle kodunuzda görüntülenmezler. Ancak, anahtarlarınızı almak için Azure Key Vault kimlik doğrulaması yapmanız gerekir. Key Vault kimlik doğrulaması yapmak için bir kimlik bilgisi gerekir. Bu, klasik bir önyükleme divma. Yönetilen Hizmet Kimliği (MSI), işlemi basitleştiren bir _önyükleme kimliği_ sağlayarak bu sorunu çözer.

Azure sanal makineler, Azure App Service veya Azure Işlevleri gibi bir Azure hizmeti için MSI 'yi etkinleştirdiğinizde Azure bir [hizmet sorumlusu](basic-concepts.md)oluşturur. MSI bunu, Azure Active Directory (Azure AD) içindeki hizmetin örneği için yapar ve hizmet sorumlusu kimlik bilgilerini bu örneğe çıkartır. 

![MSI](media/MSI.png)

Ardından, bir erişim belirteci almak için, kodunuz Azure kaynağında bulunan bir yerel meta veri hizmetini çağırır. Azure Key Vault bir hizmette kimlik doğrulaması yapmak için, kodunuz yerel MSI uç noktasından aldığı erişim belirtecini kullanır. 

## <a name="log-in-to-azure"></a>Azure'da oturum açma

Azure CLI'yi kullanarak Azure'da oturum açmak için, şunları girin:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[az group create](/cli/azure/group#az-group-create) komutunu kullanarak bir kaynak grubu oluşturun. 

Kaynak grubu adı seçin ve yer tutucuyu doldurun. Aşağıdaki örnekte Batı ABD konumunda bir kaynak grubu oluşturulur:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Bu öğreticide yeni oluşturulan kaynak grubunuzu kullanırsınız.

## <a name="create-a-key-vault"></a>Bir anahtar kasası oluşturma

Önceki adımda oluşturduğunuz kaynak grubunda bir Anahtar Kasası oluşturmak için aşağıdaki bilgileri sağlayın:

* Anahtar Kasası adı: yalnızca rakam (0-9), harf (a-z, A-Z) ve kısa çizgi (-) içerebilen 3 ile 24 karakter arasında bir dize
* Kaynak grubu adı
* Konumuna **Batı ABD**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Bu noktada, Azure hesabınız, bu yeni anahtar kasası üzerinde işlem gerçekleştirme yetkisine sahip tek bir hesaptır.

## <a name="add-a-secret-to-the-key-vault"></a>Anahtar kasasına gizli dizi ekleme

Bunun nasıl çalıştığını göstermemize yardımcı olması için bir gizli dizi ekliyoruz. Gizli dizi bir SQL bağlantı dizesi veya hem güvenli hem de uygulamanız için kullanılabilir durumda tutmanız gereken diğer bilgiler olabilir.

**Appsecret**adlı anahtar kasasında bir gizli dizi oluşturmak için aşağıdaki komutu girin:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Bu gizli anahtar, **MySecret**değerini depolar.

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma
Aşağıdaki yöntemlerden birini kullanarak bir sanal makine oluşturabilirsiniz:

* [Azure CLI](../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../virtual-machines/windows/quick-create-powershell.md)
* [Azure portalı](../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>VM 'ye bir kimlik atama
Bu adımda, Azure CLı 'de aşağıdaki komutu çalıştırarak sanal makine için sistem tarafından atanan bir kimlik oluşturursunuz:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Aşağıdaki kodda görüntülenen sistem tarafından atanan kimliği unutmayın. Yukarıdaki komutun çıkışı şöyle olacaktır: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>VM kimliğine izin atama
Artık aşağıdaki komutu çalıştırarak, önceden oluşturulmuş kimlik izinlerini anahtar kasanıza atayabilirsiniz:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Sanal makinede oturum açma

Sanal makinede oturum açmak için Bağlan bölümündeki yönergeleri izleyin [ve Windows çalıştıran bir Azure sanal makinesinde oturum açın](../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Örnek bir Python uygulaması oluşturma ve çalıştırma

Sonraki bölümde, *Sample.py*adlı örnek bir dosyadır. HTTP GET çağrısı yapmak için bir [istekler](http://docs.python-requests.org/en/master/) kitaplığı kullanır.

## <a name="edit-samplepy"></a>Sample.py Düzenle

*Sample.py*oluşturduktan sonra dosyayı açın ve bu bölümdeki kodu kopyalayın. 

Kod, iki adımlı bir işlem sunar:
1. VM 'deki yerel MSI uç noktasından bir belirteç getirir.  
  Bunun yapılması Azure AD 'den de bir belirteç getirir.
1. Belirteci anahtar kasanıza geçirin ve sonra gizli dizinizi getirin. 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Aşağıdaki kodu çalıştırarak gizli değeri görüntüleyebilirsiniz: 

```
python Sample.py
```

Yukarıdaki kod, Windows sanal makinesinde Azure Key Vault işlemleri nasıl yapılacağını gösterir. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında, sanal makineyi ve anahtar kasanızı silin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
