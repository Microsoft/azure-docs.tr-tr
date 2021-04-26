---
title: GitHub eylemlerinde Key Vault Azure Spring Cloud kimlik doğrulaması yapma
description: GitHub eylemleriyle Azure Spring Cloud için CI/CD iş akışıyla Anahtar Kasası kullanma
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0ea0db1faf8c452958b8d95c193d45506057777c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878794"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>GitHub eylemlerinde Key Vault Azure Spring Cloud kimlik doğrulaması yapma

**Bu makale şu şekilde geçerlidir:** ✔️ Java ✔️ C #

Anahtar Kasası, anahtarları depolamak için güvenli bir yerdir. Kurumsal kullanıcıların, denetdukları kapsamdaki CI/CD ortamları için kimlik bilgilerini depolaması gerekir. Anahtar kasasındaki kimlik bilgilerini almak için gereken anahtar, kaynak kapsamıyla sınırlı olmalıdır.  Azure kapsamının tamamına değil yalnızca Anahtar Kasası kapsamına erişebilir. Yalnızca bir binadaki tüm kapıları açan bir ana anahtar olmayan güçlü bir kutuyu açan bir anahtar gibidir. Bir CICD iş akışında yararlı olan başka bir anahtarla anahtar almanın bir yoludur. 

## <a name="generate-credential"></a>Kimlik bilgisi oluştur
Anahtar kasasına erişmek için bir anahtar oluşturmak üzere yerel makinenizde aşağıdaki komutu yürütün:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
Parametresi tarafından belirtilen kapsam, `--scopes` kaynağa yönelik anahtar erişimini sınırlandırır.  Yalnızca güçlü kutuya erişebilir.

Sonuçlarla:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Ardından, [GitHub deponuzu ayarlama ve Azure ile kimlik doğrulama](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate)konularında açıklandığı şekilde sonuçları GitHub **gizli** dizileri ' ne kaydedin.

## <a name="add-access-policies-for-the-credential"></a>Kimlik bilgileri için erişim Ilkeleri ekleme
Yukarıda oluşturduğunuz kimlik bilgileri, depoladığı içeriklerle değil Key Vault hakkında yalnızca genel bilgileri alabilir.  Key Vault depolanan gizli dizileri almak için kimlik bilgisi için erişim ilkeleri ayarlamanız gerekir.

Azure portal **Key Vault** panosuna gidin, **erişim denetim** menüsüne tıklayın ve ardından **rol atamaları** sekmesini açın. **Tür** ve kapsam için **uygulamalar** ' ı seçin `This resource` .   Önceki adımda oluşturduğunuz kimlik bilgisini görmeniz gerekir:

 ![Erişim ilkesini ayarla](./media/github-actions/key-vault1.png)

Kimlik bilgisi adını (örneğin,) kopyalayın `azure-cli-2020-01-19-04-39-02` . **Erişim ilkeleri** menüsünü açın, **+ erişim ilkesi Ekle** bağlantısı ' na tıklayın.  `Secret Management` **Şablon** için ' i seçin ve ardından **sorumlu**' ı seçin. Kimlik bilgisi adını **asıl öğe** / **Seç** giriş kutusuna yapıştırın:

 ![Şunu seçin:](./media/github-actions/key-vault2.png)

 **Erişim Ilkesi Ekle** Iletişim kutusunda **Ekle** düğmesine tıklayın ve ardından **Kaydet**' e tıklayın.

## <a name="generate-full-scope-azure-credential"></a>Tam kapsam Azure kimlik bilgisi oluştur
Bu, binadaki tüm kapıların açılacağı ana anahtardır. Yordam, önceki adıma benzerdir, ancak burada ana anahtarı oluşturmak için kapsamı değiştirdik:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Yine, sonuçlar:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Tüm JSON dizesini kopyalayın.  Kutuyu **Key Vault** panoya geri dönün. **Gizli** diziler menüsünü açın ve ardından **Oluştur/al** düğmesine tıklayın. Gizli adı girin, örneğin `AZURE-CREDENTIALS-FOR-SPRING` . JSON kimlik bilgisi dizesini **değer** giriş kutusuna yapıştırın. Değer giriş kutusunun çok satırlı bir metin alanı yerine tek satırlık bir metin alanı olduğunu fark edebilirsiniz.  Tüm JSON dizesini buraya yapıştırabilirsiniz.

 ![Tam kapsam kimlik bilgisi](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>GitHub eylemlerinde kimlik bilgilerini birleştirme
CICD ardışık düzeni yürütüldüğünde kullanılan kimlik bilgilerini ayarlayın:

```console
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "<Your Key Vault Name>"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Sonraki adımlar
* [Yay bulutu GitHub eylemleri](./spring-cloud-howto-github-actions.md)
