---
title: GitHub eylemleriyle AKS düğüm yükseltmelerini işleme
titleSuffix: Azure Kubernetes Service
description: GitHub eylemlerini kullanarak AKS düğümlerini güncelleştirmeyi öğrenin
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 6876cf1e5044246492e249d8a61060cbeac46f96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217966"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>GitHub eylemlerini kullanarak Azure Kubernetes Service (AKS) düğümlerine otomatik olarak güvenlik güncelleştirmeleri uygulama

Güvenlik güncelleştirmeleri, AKS kümenizin güvenliğini korumanın ve temel alınan işletim sistemi için en son düzeltmelerle uyumluluğun önemli bir parçasıdır. Bu güncelleştirmeler, işletim sistemi güvenlik düzeltmelerini veya çekirdek güncelleştirmelerini içerir. İşlemin tamamlanabilmesi için bazı güncelleştirmelerin bir düğüm yeniden başlatması gerekir.

Çalışan `az aks upgrade` , güncelleştirmeleri uygulamak için sıfır kesinti süresi sağlar. Bu komut, en son güncelleştirmeleri tüm kümenizin düğümlerine uygulamayı ve düğümlere giden trafiği ve düğüm boşaltmayı ve düğümlerin yeniden başlatılmasını, sonra da güncelleştirilmiş düğümlere giden trafiğe izin vermeyi işler. Farklı bir yöntem kullanarak düğümlerinizi güncelleştirirseniz AKS, düğümlerinizi otomatik olarak yeniden başlatmaz.

> [!NOTE]
> Bayrağıyla birlikte kullanılması arasındaki temel fark, `az aks upgrade` `--node-image-only` kullanıldığında, yalnızca düğüm görüntülerinin yükseltilecektir. Atlanırsa, hem düğüm görüntüleri hem de Kubernetes denetim düzlemi sürümü yükseltilir. Daha ayrıntılı bilgi için [, düğümlerde yönetilen yükseltmeler][managed-node-upgrades-article] ve [küme yükseltmeleri][cluster-upgrades-article] için belgeler ' i kontrol edebilirsiniz.

Tüm Kubernetes ' düğümleri standart bir Azure sanal makinesinde (VM) çalışır. Bu VM 'Ler Windows veya Linux tabanlı olabilir. Linux tabanlı VM 'Ler, işletim sistemi tarafından her gece güncelleştirmeleri otomatik olarak denetleyecek şekilde yapılandırılmış bir Ubuntu görüntüsü kullanır.

`az aks upgrade`Komutunu kullandığınızda, Azure CLI en son güvenlik ve çekirdek güncelleştirmeleriyle yeni düğümlerin bir dalgalanmasını oluşturur; bu düğümler, güncelleştirme tamamlanana kadar uygulamaların zamanlanmasını engellemek için ilk olarak bu düğümlere yönlendirilmekte. Tamamlandıktan sonra, Azure cordons (düğümü yeni iş yüklerinin zamanlaması için kullanılamaz) ve (var olan iş yüklerini diğer düğüme taşısa), eski düğümleri etkin hale getirir ve tüm zamanlanmış uygulamaların yeni düğümlere aktarılmasını sağlar.

Bu işlem, Linux tabanlı kerimnels 'leri el ile güncelleştirmeden daha iyidir, çünkü yeni bir çekirdek güncelleştirmesi yüklendiğinde Linux 'un yeniden başlatılması gerekir. İşletim sistemini el ile güncelleştirirseniz, sanal makineyi de yeniden başlatmanız, tüm uygulamaları el ile eklemeniz ve boşaltmanız gerekir.

Bu makalede AKS düğümlerinin güncelleştirme işlemini nasıl otomatikleştirebileceğiniz gösterilmektedir. Otomatik olarak çalıştırılan bir güncelleştirme görevi oluşturmak için GitHub eylemlerini ve Azure CLı 'yi kullanırsınız `cron` .

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

Bu makalede, ' de eylemlerinizi oluşturmak için bir [GitHub][github] hesabınız olduğunu varsaymaktadır.

## <a name="create-a-timed-github-action"></a>Zamanlanmış bir GitHub eylemi oluşturma

`cron` otomatikleştirilmiş bir zamanlamaya göre bir dizi komut veya iş çalıştırmanızı sağlayan bir yardımcı programdır. AKS düğümlerinizi otomatik bir zamanlamaya göre güncelleştirmek üzere iş oluşturmak için, eylemlerinizi barındırmak üzere bir depoya ihtiyacınız vardır. Genellikle, GitHub eylemleri uygulamanızla aynı depoda yapılandırılır, ancak herhangi bir depoyu kullanabilirsiniz. Bu makalede [profil deponuzu][profile-repository]kullanacağız. Aboneliğiniz yoksa GitHub Kullanıcı adınızla aynı ada sahip yeni bir depo oluşturun.

1. GitHub 'da deponuza gidin
1. Sayfanın üst kısmındaki **Eylemler** sekmesine tıklayın.
1. Bu depoda zaten bir iş akışı ayarladıysanız, tamamlanan çalıştırmalar listesine yönlendirilirsiniz, bu durumda **Yeni Iş akışı** düğmesine tıklayın. Bu, depodaki ilk iş akışıdır, GitHub size bazı proje şablonları sunar, Açıklama metninin altındaki **iş akışını ayarla** bağlantısına tıklayın.
1. `name` `on` Aşağıda gösterilene benzer iş akışını ve etiketleri değiştirin. GitHub eylemleri, Linux tabanlı sistemle aynı [POSIX cron söz dizimini][cron-syntax] kullanır. Bu zamanlamada, iş akışının her 15 günde bir çalışacak şekilde 3:00'da çalışacağını söyliyoruz.

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. Aşağıdaki kullanarak yeni bir iş oluşturun. Bu iş adlandırılır `upgrade-node` , Ubuntu aracısında çalışır ve düğümleri yükseltmek için gerekli adımları yürütmek üzere Azure CLI hesabınıza bağlanır.

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>İş akışında Azure CLı 'yı ayarlama

`steps`Bu anahtarda, iş akışının, düğümleri yükseltmek için yürütüleceği tüm işleri tanımlayacaksınız.

Azure CLı 'yi indirip açın.

1. GitHub eylemleri ekranının sağ tarafında *Market arama çubuğunu* bulun ve **"Azure oturum açma"** yazın.
1. Sonuç olarak, **Azure tarafından** yayımlanan **Azure oturum açma** adı verilen bir eylem elde edersiniz:

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="İki satırı gösteren arama sonuçları, ilk eyleme ' Azure Login' ve ikinci ' Azure Container Registry Login' adı verilir":::

1. **Azure oturum açma**' ya tıklayın. Sonraki ekranda, kod örneğinin sağ üst kısmındaki **Kopyala simgesine** tıklayın.

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="Aşağıdaki kod örneğiyle Azure oturum açma eylemi sonuç bölmesi, bir kopyalama simgesinin çevresindeki kırmızı kare tıklama noktasını vurgular":::

1. Anahtarın altına şunu yapıştırın `steps` :

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. Yeni bir Kullanıcı adı ve parola oluşturmak için Azure CLı 'dan aşağıdaki komutu çalıştırın.

    ```azurecli-interactive
    az ad sp create-for-rbac -o json
    ```

    Çıktı aşağıdaki JSON 'a benzer olmalıdır:

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. **Yeni bir tarayıcı penceresinde** GitHub deponuza gidin ve deponun **Ayarlar** sekmesini açın. **Gizlilikler** ' a ve ardından **yeni depo parolası**' na tıklayın.
1. *Ad* için öğesini kullanın `AZURE_CREDENTIALS` .
1.  *Değer* için, yeni bir Kullanıcı adı ve parola oluşturduğunuz önceki adımın çıktısından tüm içeriği ekleyin.

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="Gizli başlık olarak AZURE_CREDENTIALS gösteren form ve yürütülen komutun JSON olarak yapıştırılmış çıktısı":::

1. **Gizli dizi Ekle**' ye tıklayın.

Eyleminiz tarafından kullanılan CLı, Azure hesabınızda günlüğe kaydedilir ve komutları çalıştırmaya hazırlanacaktır.

Azure CLı komutlarını yürütme adımlarını oluşturmak için.

1. Ekranın sağ tarafındaki *GitHub marketi* ' nde **arama sayfasına** gidin ve *Azure CLI eyleminde* arama yapın. Azure 'da *Azure CLI eylemini* seçin.

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="İlk sonuç Azure tarafından yapıldığı gibi gösterilmekte olan ' Azure CLı eylemi ' için arama sonucu":::

1. *GitHub marketi sonucundaki* Kopyala düğmesine tıklayın ve eylemin Içeriğini *Azure oturum açma* adımının altındaki ana düzenleyicide, aşağıdakine benzer şekilde yapıştırın:

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only --yes
    ```

    > [!TIP]
    > , `-g` Ve `-n` parametrelerini, önceki adımlara benzer gizli dizilerle ekleyerek komuttan ayırarak. `{resourceGroupName}`Ve `{aksClusterName}` yer tutucuları gizli karşılıklarına göre değiştirin, örneğin `${{secrets.RESOURCE_GROUP_NAME}}` ve`${{secrets.AKS_CLUSTER_NAME}}`

1. Dosyayı `upgrade-node-images` olarak yeniden adlandırın.
1. **Işlemeye başla**, bir ileti başlığı Ekle ve iş akışını Kaydet ' e tıklayın.

Yürütmeyi oluşturduktan sonra iş akışı kaydedilir ve yürütülmeye hazırlanacaktır.

> [!NOTE]
> Kümedeki tüm düğüm havuzları yerine tek bir düğüm havuzunu yükseltmek için, `--name` `az aks nodepool upgrade` düğüm havuzu adını belirtmek üzere komutuna parametresini ekleyin. Örnek:
> ```azurecli-interactive
> az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>GitHub eylemini el ile Çalıştır

Zamanlanan çalıştırmaya ek olarak, adlı yeni bir tetikleyici ekleyerek iş akışını el ile çalıştırabilirsiniz `on` `workflow_dispatch` . Tamamlanan dosya aşağıdaki gibi görünmelidir:

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>Sonraki adımlar

- En son düğüm görüntüleri hakkında bilgi için bkz. [aks sürüm notları](https://github.com/Azure/AKS/releases) .
- [AKS kümesini yükseltme][cluster-upgrades-article]Ile Kubernetes sürümünü nasıl yükselteceğinizi öğrenin.
- Birden çok düğüm havuzu ve [birden çok düğüm havuzu oluşturma ve yönetme][use-multiple-node-pools]ile düğüm havuzlarını yükseltme hakkında daha fazla bilgi edinin.
- [Sistem düğüm havuzları][system-pools] hakkında daha fazla bilgi edinin
- Spot örnekleri kullanarak maliyetlerin nasıl kaydedileceğini öğrenmek için bkz. [AKS 'e spot düğüm havuzu ekleme][spot-pools]

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
