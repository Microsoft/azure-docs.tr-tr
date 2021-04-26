---
title: GitHub eylemlerini kullanarak Kaynak Yöneticisi şablonları dağıtma
description: GitHub eylemleri kullanılarak Azure Resource Manager şablonlarının (ARM şablonları) nasıl dağıtılacağını açıklar.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: ec29ae019555c54ccdcef9dd743706f8d6401bbd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781982"
---
# <a name="deploy-arm-templates-by-using-github-actions"></a>GitHub eylemlerini kullanarak ARM şablonları dağıtma

[GitHub eylemleri](https://docs.github.com/en/actions) , yazılım geliştirme iş akışlarınızı aynı yerde otomatik hale getirmek için GitHub 'daki bir özellik paketidir. böylece, çekme istekleri ve sorunları üzerinde kod depolar ve işbirliği yapın.

Azure 'a bir Azure Resource Manager şablonu (ARM şablonu) dağıtmayı otomatikleştirmek için [Azure Resource Manager şablonu dağıt eylemini](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) kullanın.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub hesabı. Bir hesabınız yoksa [ücretsiz](https://github.com/join)kaydolun.
    - Kaynak Yöneticisi şablonlarınızı ve iş akışı dosyalarınızı depolamak için bir GitHub deposu. Bir tane oluşturmak için bkz. [Yeni bir depo oluşturma](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository).


## <a name="workflow-file-overview"></a>İş akışı dosyasına genel bakış

Bir iş akışı, deponuzdaki yoldaki bir YAML (. yıml) dosyası tarafından tanımlanır `/.github/workflows/` . Bu tanım, iş akışını oluşturan çeşitli adımları ve parametreleri içerir.

Dosya iki bölümden oluşur:

|Section  |Görevler  |
|---------|---------|
|**Kimlik Doğrulaması** | 1. bir hizmet sorumlusu tanımlayın. <br /> 2. GitHub gizli dizisi oluşturun. |
|**Dağıtma** | 1. Kaynak Yöneticisi şablonunu dağıtın. |

## <a name="generate-deployment-credentials"></a>Dağıtım kimlik bilgileri oluştur


[Azure CLI](/cli/azure/)'de [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) komutuyla bir [hizmet sorumlusu](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) oluşturabilirsiniz. Bu komutu Azure portal [Azure Cloud Shell](https://shell.azure.com/) veya **deneyin** düğmesini seçerek çalıştırın.

Henüz bir tane yoksa bir kaynak grubu oluşturun.

```azurecli-interactive
    az group create -n {MyResourceGroup} -l {location}
```

Yer tutucusunu `myApp` uygulamanızın adıyla değiştirin.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

Yukarıdaki örnekte yer tutucuları abonelik KIMLIĞINIZ ve kaynak grubu adıyla değiştirin. Çıktı, aşağıda gösterilene benzer App Service uygulamanıza erişim sağlayan rol atama kimlik bilgileri içeren bir JSON nesnesidir. Bu JSON nesnesini daha sonra kopyalayın. Yalnızca `clientId` ,, `clientSecret` `subscriptionId` ve değerlerine sahip bölümlere ihtiyacınız olacaktır `tenantId` .

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
> En az erişim sağlamak her zaman iyi bir uygulamadır. Önceki örnekteki kapsam, kaynak grubuyla sınırlandırılmıştır.



## <a name="configure-the-github-secrets"></a>GitHub gizli dizilerini yapılandırma

Azure kimlik bilgileriniz, kaynak grubunuz ve abonelikleriniz için gizli diziler oluşturmanız gerekir.

1. [GitHub](https://github.com/)'da deponuza gözatamazsınız.

1. **Yeni gizli > > ayarlar**' ı seçin.

1. Azure CLı komutundan tüm JSON çıkışını gizli dizi değeri alanına yapıştırın. Gizli dizi adını verin `AZURE_CREDENTIALS` .

1. Adlı başka bir gizli dizi oluşturun `AZURE_RG` . Kaynak grubunuzun adını gizli dizi değeri alanına ekleyin (örnek: `myResourceGroup` ).

1. Adlı ek bir gizli dizi oluşturun `AZURE_SUBSCRIPTION` . Abonelik KIMLIĞINIZI gizli dizi değeri alanına ekleyin (örnek: `90fd3f9d-4c61-432d-99ba-1273f236afa2` ).

## <a name="add-resource-manager-template"></a>Kaynak Yöneticisi şablonu Ekle

GitHub deponuza bir Kaynak Yöneticisi şablonu ekleyin. Bu şablon bir depolama hesabı oluşturur.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Dosyayı depodaki herhangi bir yere koyabilirsiniz. Sonraki bölümde yer alan iş akışı örneği, şablon dosyasının **azuredeploy.js** olarak adlandırıldığını ve deponun kökünde depolandığını varsayar.

## <a name="create-workflow"></a>İş akışı oluşturma

İş akışı dosyası, deponuzın kökündeki **. GitHub/iş akışları** klasöründe depolanmalıdır. İş akışı dosyası uzantısı **. yıml** ya da **. YAML** olabilir.

1. GitHub deponuzdan üstteki menüden **Eylemler** ' i seçin.
1. **Yeni iş akışı**' nı seçin.
1. **Bir iş akışını kendiniz ayarlayın** öğesini seçin.
1. **Main. yml** dışında farklı bir ad tercih ediyorsanız, iş akışı dosyasını yeniden adlandırın. Örneğin: **Deploystorageaccount. yml**.
1. yml dosyasının içeriğini aşağıdakilerle değiştirin:

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy ARM template
        - name: Run ARM deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS

          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```
    > [!NOTE]
    > ARM dağıtımı eyleminde bunun yerine bir JSON biçim parametreleri dosyası belirtebilirsiniz (örnek: `.azuredeploy.parameters.json` ).

    İş akışı dosyasının ilk bölümü şunları içerir:

    - **ad**: iş akışının adı.
    - **Açık**: iş akışını tetikleyen GitHub olaylarının adı. Ana dalda, belirtilen iki dosyadan en az birini değiştiren bir anında iletme olayı olduğunda iş akışı tetiklenir. İki dosya, iş akışı dosyası ve şablon dosyasıdır.

1. **Start commit** (İşlemeye başla) öğesini seçin.
1. **Doğrudan ana dala kaydet**' i seçin.
1. **Yeni dosya Kaydet** ' i (veya **değişiklikleri Yürüt**) seçin.

İş akışı, iş akışı dosyası ya da güncelleştirilmekte olan şablon dosyası tarafından tetiklenecek şekilde yapılandırıldığından, değişiklikleri kaydettikten sonra iş akışı hemen başlar.

## <a name="check-workflow-status"></a>İş akışı durumunu denetle

1. **Eylemler** sekmesini seçin. Listelenen bir **deployStorageAccount. yıml** iş akışı görüntülenir. İş akışını çalıştırmak için 1-2 dakika sürer.
1. Açmak için iş akışını seçin.
1. Dağıtımı doğrulamak için menüden **ARM dağıtımını Çalıştır** ' ı seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Kaynak grubunuz ve deponuz artık gerekmiyorsa, kaynak grubunu ve GitHub deponuzu silerek dağıttığınız kaynakları temizleyin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [İlk ARM şablonunuzu oluşturma](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Modül öğren: GitHub eylemleri kullanarak ARM şablonlarının dağıtımını otomatikleştirme](/learn/modules/deploy-templates-command-line-github-actions/)
