---
title: Azure Depolama’ya statik bir site dağıtmak için GitHub Actions kullanma
description: GitHub eylemleri ile Azure depolama statik Web sitesi barındırma
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 01/11/2021
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 3ae0904eda2608026ad09ba8b8993008380725f4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788538"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Azure Storage 'da statik Web sitenizi dağıtmak için bir GitHub eylemleri iş akışı ayarlama

Bir Azure depolama hesabına statik bir site dağıtmak için bir iş akışı kullanarak [GitHub eylemleri](https://docs.github.com/en/actions) ile çalışmaya başlayın. GitHub eylemleri iş akışını ayarladıktan sonra, sitenizin kodunda değişiklik yaptığınızda sitenizi GitHub 'dan otomatik olarak Azure 'a dağıtabilirsiniz.

> [!NOTE]
> [Azure statik Web Apps](../../static-web-apps/index.yml)kullanıyorsanız, El Ile GitHub eylemleri iş akışını ayarlamanıza gerek yoktur.
> Azure statik Web Apps, sizin için otomatik olarak bir GitHub eylemleri iş akışı oluşturur. 

## <a name="prerequisites"></a>Önkoşullar

Bir Azure aboneliği ve GitHub hesabı. 

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Statik Web sitesi kodunuzla bir GitHub deposu. GitHub hesabınız yoksa [ücretsiz kaydolun](https://github.com/join).  
- Azure depolamada barındırılan, çalışan bir statik Web sitesi. [Azure depolama 'da statik bir Web sitesini nasıl barındıracağınızı](storage-blob-static-website-how-to.md)öğrenin. Bu örneği izlemek için, [Azure CDN](static-website-content-delivery-network.md)de dağıtmanız gerekir.

> [!NOTE]
> Dünyanın dört bir yanındaki kullanıcılarınıza gecikme süresini azaltmak ve depolama hesabınıza işlem sayısını azaltmak için bir Content Delivery Network (CDN) kullanılması yaygındır. Bulut tabanlı bir depolama hizmetine statik içerik dağıtmak, pahalı bir işlem örneği gereksinimini azaltabilir. Daha fazla bilgi için bkz. [statik Içerik barındırma stili](/azure/architecture/patterns/static-content-hosting).

## <a name="generate-deployment-credentials"></a>Dağıtım kimlik bilgileri oluştur

[Azure CLI](/cli/azure/)'de [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) komutuyla bir [hizmet sorumlusu](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) oluşturabilirsiniz. Bu komutu Azure portal [Azure Cloud Shell](https://shell.azure.com/) veya **deneyin** düğmesini seçerek çalıştırın.

Yer tutucusunu `myStaticSite` Azure Storage 'da barındırılan sitenizin adıyla değiştirin. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

Yukarıdaki örnekte yer tutucuları abonelik KIMLIĞINIZ ve kaynak grubu adıyla değiştirin. Çıktı, aşağıda gösterilene benzer depolama hesabınıza erişim sağlayan rol atama kimlik bilgileri içeren bir JSON nesnesidir. Bu JSON nesnesini daha sonra kopyalayın.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> En az erişim sağlamak her zaman iyi bir uygulamadır. Önceki örnekteki kapsam, kaynak grubunun tamamı değil, belirli App Service uygulamasıyla sınırlandırılmıştır.

## <a name="configure-the-github-secret"></a>GitHub gizliliğini yapılandırma

1. [GitHub](https://github.com/)'da deponuza gözatamazsınız.

1. **Yeni gizli > > ayarlar**' ı seçin.

1. Azure CLı komutundan tüm JSON çıkışını gizli dizi değeri alanına yapıştırın. Gizli dizi gibi bir ad verin `AZURE_CREDENTIALS` .

    Daha sonra iş akışı dosyasını yapılandırdığınızda, `creds` Azure oturum açma eyleminin girişi için gizli anahtarı kullanırsınız. Örnek:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>İş akışınızı ekleyin

1. GitHub deponuz için **eylemlere** gidin. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="GitHub eylemleri menü öğesi":::

1. **İş akışınızı kendiniz ayarlama** seçeneğini belirleyin. 

1. `on:`İş akışı dosyanızın bölümünden sonraki her şeyi silin. Örneğin, kalan iş akışınız şöyle görünebilir. 

    ```yaml
    name: CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]
    ```

1. İş akışınızı yeniden adlandırın `Blob storage website CI` ve kullanıma alma ve oturum açma eylemlerini ekleyin. Bu eylemler, site kodunuzu kullanıma alacak ve `AZURE_CREDENTIALS` daha önce oluşturduğunuz GitHub gizliliğini kullanarak Azure ile kimlik doğrulamasından acaktır. 

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Kodunuzu blob depolamaya yüklemek ve CDN uç noktanızı temizlemek için Azure CLı eylemini kullanın. İçin `az storage blob upload-batch` , yer tutucusunu depolama hesabınızın adıyla değiştirin. Betik `$web` kapsayıcıya yükleyecek. İçin `az cdn endpoint purge` , yer TUTUCULARı CDN profil adınız, CDN uç noktası adı ve kaynak grubuyla değiştirin.

    ```yaml
        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
    ``` 

1. Azure oturumunu kapatmak için bir eylem ekleyerek iş akışınızı doldurun. Tamamlanan iş akışı aşağıda verilmiştir. Dosya, `.github/workflows` deponuzdaki klasörde görüntülenir.

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}

        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
      
      # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Dağıtımınızı gözden geçirin

1. GitHub deponuz için **eylemlere** gidin. 

1. İş akışınızın çalışmasının ayrıntılı günlüklerini görmek için ilk sonucu açın. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="GitHub eylemlerinin çalışması günlüğü":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Statik Web siteniz ve GitHub deponuz artık gerekmiyorsa, kaynak grubunu ve GitHub deponuzu silerek dağıttığınız kaynakları temizleyin. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure statik Web Apps hakkında bilgi edinin](../../static-web-apps/index.yml)
