---
title: 'Öğretici: Azure Ilkesini GitHub ile kod olarak uygulama'
description: Bu öğreticide, dışarı aktarma, GitHub eylemleri ve GitHub iş akışlarıyla kod iş akışı olarak bir Azure Ilkesi uygulıyorum
ms.date: 03/31/2021
ms.topic: tutorial
ms.openlocfilehash: 64957671597ad6df237f92176e10280dc45018c9
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092764"
---
# <a name="tutorial-implement-azure-policy-as-code-with-github"></a>Öğretici: Azure Ilkesini GitHub ile kod olarak uygulama

Kod iş akışı olarak bir Azure Ilkesi, ilke tanımlarınızı ve atamalarınızı kod olarak yönetmeyi, bu tanımları güncelleştirme yaşam döngüsünü denetlemenizi ve uyumluluk sonuçlarının doğrulanmasını otomatikleştirebilmesini sağlar. Bu öğreticide, bir yaşam döngüsü işlemi oluşturmak için GitHub ile Azure Ilke özelliklerini kullanmayı öğreneceksiniz. Bu görevler arasında şunlar yer alır:

> [!div class="checklist"]
> - İlke tanımlarını ve atamalarını GitHub 'a aktarma
> - GitHub ' da Azure 'a güncelleştirilmiş gönderme ilkesi nesneleri
> - GitHub eyleminden bir uyumluluk taraması tetikleyin

Mevcut kaynaklarınızın geçerli uyumluluk durumunu belirlemek için bir ilke atamak istiyorsanız, hızlı başlangıç makalelerinde bunun nasıl yapılacağı açıklanmaktadır.

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
- Bu öğreticide kullanılan tasarım düzenlerini anlamak için [bir Azure Ilkesini kod iş akışı olarak tasarlamayı](../concepts/policy-as-code.md) inceleyin.

### <a name="export-azure-policy-objects-from-the-azure-portal"></a>Azure portal Azure Ilke nesnelerini dışarı aktarma

Azure portal bir ilke tanımını dışarı aktarmak için şu adımları izleyin:

1. Azure portalında **Tüm hizmetler**’e tıkladıktan sonra **İlke**'yi arayıp seçerek Azure İlkesi hizmetini başlatın.

1. Azure Ilkesi sayfasının sol tarafındaki **tanımlar** ' ı seçin.

1. **Tanımları dışarı aktar** düğmesini veya bir ilke tanımının satırındaki üç noktayı seçip **dışarı aktarma tanımı**' nı seçin.

1. **GitHub Ile oturum aç** düğmesini seçin. Kaynağı dışarı aktarmak için Azure Ilkesini yetkilendirmek üzere GitHub ile henüz kimlik doğrulamasından ayrıldıysanız, açılan yeni pencerede [GitHub eylemi](https://github.com/features/actions) ihtiyaçlarına erişimi gözden geçirin ve dışarı aktarma işlemine devam etmek Için **Yetkilendir AzureGitHubActions** ' ı seçin. Tamamlandıktan sonra, yeni pencere kendi kendini kapatır.

1. **Temel bilgiler** sekmesinde, aşağıdaki seçenekleri ayarlayın ve ardından sayfanın alt kısmındaki **Ilkeler** sekmesini veya **Sonraki: ilkeler** düğmesini seçin.

   - **Depo filtresi**: GitHub eyleminin erişimine izin verdiğiniz tüm depoları görmek Için _depolarıma_ yalnızca sahip olduğunuz depoları veya _Tüm depolarımı_ görüntülemek için ayarlayın.
   - **Depo**: Azure ilke kaynaklarını dışarı aktarmak istediğiniz depoya ayarlayın.
   - **Dal**: depodaki dalı ayarlayın. Varsayılan dışında bir dal kullanmak, kaynak kodunuzda daha fazla birleştirmeden önce güncelleştirmelerinizi doğrulamak için iyi bir yoldur.
   - **Dizin**: Azure ilke kaynaklarının dışarı aktarılacağı _kök düzeyi klasörü_ . Bu dizin altındaki alt klasörler, hangi kaynakların verileceği temel alınarak oluşturulur.

1. **İlkeler** sekmesinde, üç noktayı seçip yönetim gruplarının, aboneliklerinin veya kaynak gruplarının bir birleşimini seçerek kapsamı arama olarak ayarlayın.
   
1. Hangi nesnelerin dışarı aktarılacağı kapsamı aramak için **ilke tanımlarını Ekle** düğmesini kullanın. Açılan yan pencerede, dışarı aktarılacak her bir nesneyi seçin. Seçimi arama kutusuna veya türe göre filtreleyin. Dışarı aktarılacak tüm nesneleri seçtikten sonra sayfanın alt kısmındaki **Ekle** düğmesini kullanın.

1. Seçili her nesne için, _yalnızca tanım_ veya tanım ve ilke tanımı için _atama (ler)_ gibi istenen dışarı aktarma seçeneklerini seçin. Ardından sayfanın alt kısmındaki **gözden geçir + dışarı aktar** sekmesini veya **Sonraki: gözden geçir + dışarı aktar** düğmesini seçin.

   > [!NOTE]
   > Seçenek _tanımı ve atamaları_ seçilirse, yalnızca ilke tanımı eklendiğinde filtre tarafından ayarlanan kapsamdaki ilke atamaları verilir.

1. **Gözden geçir ve dışarı aktar** sekmesinde, Ayrıntılar ' ı işaretleyin ve ardından sayfanın alt kısmındaki **dışarı aktar** düğmesini kullanın.

1. Seçilen kaynakların artık kaynak denetiminize aktarıldığına bakmak için GitHub deponuzu, dalınızı ve _kök düzeyi klasörünüzü_ denetleyin.

Azure Ilke kaynakları, seçilen GitHub deposu ve _kök düzeyi klasörü_ içinde aşağıdaki yapıya aktarılabilir:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

### <a name="push-policy-objects-updated-in-github-to-azure"></a>GitHub ' da Azure 'a güncelleştirilmiş gönderme ilkesi nesneleri

1. İlke nesneleri aktarıldığında, başlamanızı sağlamak için adlı bir [GitHub iş akışı](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) dosyası `.github/workflows/manage-azure-policy-<randomLetters>.yml` da oluşturulur.

   > [!NOTE]
   > GitHub iş akışı dosyası her dışa aktarma kullanıldığında oluşturulur. Dosyanın her örneği, bu dışarı aktarma eylemi sırasında seçeneklere özgüdür.

1. Bu iş akışı dosyası, GitHub deposundaki içeri aktarılmış İlke nesnelerinde yapılan değişiklikleri Azure Ilkesine geri göndermek için [Azure Ilkesini Yönet](https://github.com/marketplace/actions/manage-azure-policy) eylemini kullanır. Varsayılan olarak, eylem yalnızca Azure 'daki mevcut olanlardan farklı olan dosyaları dikkate alır ve eşitler. `assignments`Yalnızca belirli atama dosyalarına yapılan değişiklikleri eşitlemek için Eylemdeki parametresini de kullanabilirsiniz. Bu parametre, yalnızca belirli bir ortam için ilke atamalarını uygulamak üzere kullanılabilir. Daha fazla bilgi için bkz. [Azure ilke deposunu yönetme Benioku dosyası](https://github.com/Azure/manage-azure-policy).

1. Varsayılan olarak, iş akışının el ile tetiklenmesi gerekir. Bunu yapmak için GitHub 'daki **eylemleri** kullanın, `manage-azure-policy-<randomLetters>` iş akışını seçin, **iş akışını Çalıştır**' ı seçin ve **iş akışını yeniden çalıştırın** .

   :::image type="content" source="../media/policy-as-code-github/manually-trigger-workflow.png" alt-text="GitHub Web arabirimindeki eylem sekmesi, iş akışı ve iş akışı düğmelerinin ekran görüntüsü.":::

   > [!NOTE]
   > İş akışı dosyasının algılanabilmesi ve el ile çalıştırılması için varsayılan dalda olması gerekir.

1. İş akışı, İlke nesnelerinde yapılan değişiklikleri Azure ile eşitler ve günlüklerde durum verir.

   :::image type="content" source="../media/policy-as-code-github/workflow-logging.png" alt-text="İşlem aşamasında iş akışının ve günlüğe kaydedilen ayrıntıların ekran görüntüsü.":::

1. İş akışı Ayrıca izleme için Azure Ilke nesnelerine ayrıntılar ekler `properties.metadata` .

   :::image type="content" source="../media/policy-as-code-github/updated-definition-metadata.png" alt-text="Azure portal 'daki Azure Ilke tanımının ekran görüntüsü, GitHub eylemine özgü meta verilerle güncelleştirildi.":::

### <a name="trigger-compliance-scans-using-github-action"></a>GitHub eylemini kullanarak uyumluluk taramalarını tetikleme

[Azure Ilkesi uyumluluk tarama eylemini](https://github.com/marketplace/actions/azure-policy-compliance-scan) kullanarak, bir veya birden çok kaynakta, kaynak grubunda veya aboneliklerde [GitHub iş akışınız](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) için isteğe bağlı bir uyumluluk değerlendirmesi taraması tetikleyebilirsiniz ve bu kaynakların uyumluluk durumuna göre iş akışı yolunu değiştirebilirsiniz. Ayrıca, en son uyumluluk durumunu uygun bir zamanda almak için iş akışını zamanlanan bir zamanda çalışacak şekilde yapılandırabilirsiniz. İsteğe bağlı olarak, bu GitHub eylemi, daha fazla analiz veya arşivleme için taranan kaynakların uyumluluk durumu hakkında bir rapor da oluşturabilir.

Aşağıdaki örnek bir abonelik için uyumluluk taraması çalıştırır. 

```yaml

on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

```

## <a name="review"></a>Gözden geçirme

Bu öğreticide, aşağıdaki görevleri başarıyla gerçekleştirdiniz:

> [!div class="checklist"]
> - GitHub 'a aktarılmış ilke tanımları ve atamaları
> - GitHub 'da Azure 'a güncelleştirilmiş ilke nesneleri gönderildi
> - GitHub eyleminden bir uyumluluk taraması tetikleniyor

## <a name="next-steps"></a>Sonraki adımlar

İlke tanımlarının yapıları hakkında daha fazla bilgi edinmek için şu makaleyi gözden geçirin:

> [!div class="nextstepaction"]
> [Azure İlkesi tanım yapısı](../concepts/definition-structure.md)