---
title: Azure portalında sorgu oluşturma ve paylaşma
description: Bu öğreticide, bir kaynak grafiği sorgusu oluşturup Azure portal başkalarıyla paylaşma hakkında bilgi edinin.
ms.date: 10/23/2019
ms.topic: tutorial
ms.openlocfilehash: 65b96da3bd9064f34d75d5e87f1fcf55336d9893
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958565"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Öğretici: Azure portal Azure Kaynak Grafiği sorgusu oluşturma ve paylaşma

Azure Kaynak Grafiği Gezgini, kaynak grafik sorgularınızı doğrudan Azure portal kaydetmenize olanak tanır. İki tür sorgu vardır: _özel_ ve _paylaşılan_. Azure portal ayarlarınıza özel bir sorgu kaydedilir. Paylaşılan sorgu, rol tabanlı erişim denetimleriyle (RBAC) yönetilebilen ve kaynak kilitleriyle korunan bir Kaynak Yöneticisi kaynağıdır.

Sorguları Azure portal kaydederek sık kullandığınız veya yaygın olarak kullanılan sorguları Aradığınız zamandan tasarruf edebilirsiniz. Sorguları paylaştığınızda, ekibinizin yineleme aracılığıyla tutarlılık ve verimlilik hedeflerini fark etmiş olursunuz.

Bu öğreticide, aşağıdaki görevleri tamamlayacaksınız:

> [!div class="checklist"]
> - Özel sorgu oluşturma ve silme
> - Paylaşılan sorgu oluşturma
> - Paylaşılan sorguları bulma
> - Paylaşılan bir sorguyu silme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için bir Azure aboneliğinizin olması gerekir. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="create-and-delete-a-private-query"></a>Özel sorgu oluşturma ve silme

Özel sorgular erişilebilir ve yalnızca kendilerini oluşturan hesap tarafından görülebilir. Bir hesabın Azure portal ayarlarına kaydedildiğinden, bunlar yalnızca Azure portal içinden oluşturulabilir, kullanılabilirler ve silinebilir. Özel bir sorgu Kaynak Yöneticisi kaynak değildir. Yeni bir özel sorgu oluşturmak için aşağıdaki adımları izleyin:

1. Portal menüsünde **tüm hizmetler** ' i seçin veya tüm sayfaların en üstündeki Azure Arama kutusunu kullanın. **Kaynak Grafiği Gezgini**' ni arayıp seçin.

1. Azure Kaynak Grafiği Gezgini sayfasındaki **sorgu 1** sekmesinde aşağıdaki sorguyu girin:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Alttaki bölmede sorgu sonuçlarını görmek için **Sorguyu Çalıştır** ' ı seçin.

   Bu sorgu hakkında daha fazla bilgi için bkz. [Samples: sanal makineleri işletim sistemi türüne göre say](../samples/starter.md#count-virtual-machines-by-os-type).


1. **Farklı** **Kaydet ' i seçin,** ad **olarak işletim sistemine göre VM sayısını** girin, türü **özel sorgu**olarak bırakın ve ardından Save **Query** bölmesinin en altında bulunan **Kaydet** ' i seçin. Sekme başlığı, **sorgu 1** ' den **Işletim sistemine göre VM 'lere göre**değişir.

1. Azure portal Azure Kaynak Grafiği Gezgini ' nden uzakta ilerleyin ve sonra buna geri dönün. Kaydedilen sorgunun artık gösterilmediğine ve **sorgu 1** sekmesinin döndürüldiğine dikkat edin.

1. **Sorgu aç**' ı seçin. Türün **özel sorgu**olduğundan emin olun. **İşletim sistemi tarafından kaydedilen ad sayısı VM 'leri** artık **sorgu adı** listesinde görünür. Kaydedilen sorgunun başlık bağlantısını seçtiğinizde, bu sorgu adı ile yeni bir sekmeye yüklenir.

   > [!NOTE] 
   >Kaydedilen bir sorgu açık olduğunda ve sekme bu adı gösteriyorsa, **Kaydet** düğmesi seçildiğinde bu değişiklik yapılmış değişikliklerle güncelleştirilir. Bu açık sorgudan yeni bir kaydedilmiş sorgu oluşturmak için **farklı kaydet** ' i seçin ve yeni bir yepyeni sorgu kaydediyorsanız devam edin.

1. Kayıtlı sorguyu silmek için **sorguyu yeniden aç** ' ı seçin ve **tür** alanının **özel sorgu**olarak ayarlandığını doğrulayın. Kaydedilen `Count VMs by OS` sorgusunun satırında **Sil** ' i (geri dönüşüm kutusu simgesi) seçin. Onay iletişim kutusunda, sorguyu silmeyi sona bırakmak için **Evet** ' i seçin.
   Sonra, **bir sorgu aç** bölmesini kapatın.

## <a name="create-a-shared-query"></a>Paylaşılan sorgu oluşturma

Özel bir sorgunun aksine, paylaşılan bir sorgu Kaynak Yöneticisi kaynağıdır. Bu olgu, sorgunun bir kaynak grubuna kaydedildiği, RBAC ile yönetilebilecek ve denetlenebileceği, hatta kaynak kilitleri ile korunabilecek anlamına gelir. Kaynak olarak, uygun izinlere sahip olan herkes onu görebilir ve kullanabilir.
Yeni bir paylaşılan sorgu oluşturmak için aşağıdaki adımları izleyin:

1. Portal menüsünde **tüm hizmetler**' i seçin veya **kaynak grafik Gezgini**' ni aramak ve seçmek için tüm sayfaların en üstündeki Azure Arama kutusunu kullanın.

1. Azure Kaynak Grafiği Gezgini sayfasındaki **sorgu 1** sekmesinde aşağıdaki sorguyu girin:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Alttaki bölmede sorgu sonuçlarını görmek için **Sorguyu Çalıştır** ' ı seçin.

   Bu sorgu hakkında daha fazla bilgi için bkz. [Samples: sanal makineleri işletim sistemi türüne göre say](../samples/starter.md#count-virtual-machines-by-os-type).

1. **Kaydet** veya **farklı kaydet**' i seçin.

   
   ![Kaydet düğmesini kullanarak yeni sorguyu kaydetme](../media/create-share-query/save-shared-query-buttons.png)

1. **Sorguyu Kaydet** bölmesinde, ad için **Işletim sistemine göre sayı VM 'leri** girin.

1. Türü **paylaşılan sorgu**olarak değiştirin, açıklamasını **işletim sistemi türüne göre sanal makine sayısı**olarak ayarlayın ve sorgu kaynağının nerede oluşturulacağını belirtmek için **abonelik** ayarlayın.

1. **Kaynak olarak Yayımla-Graph-Queries kaynak grubu** onay kutusunu seçili bırakın ve **kaynak grubu konumu** **(US) Orta Batı ABD**olarak ayarlanır.

1. **Sorguyu Kaydet** bölmesinin en altında bulunan **Kaydet** ' i seçin. Sekme başlığı, **sorgu 1** ' den **Işletim sistemine göre VM 'lere göre**değişir. **Kaynak-grafik sorguları** kaynak grubu ilk kez kullanıldığında, kaynak grubu oluşturulduğundan, kaydetme beklenenden uzun sürer.
   
   ![Yeni sorguyu paylaşılan sorgu olarak kaydet](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE] 
   > Paylaşılan sorguyu kaydetmek için mevcut bir kaynak grubunun adını sağlamak istiyorsanız **kaynağa Yayımla-Graph-Queries kaynak grubu** onay kutusunu temizleyebilirsiniz. Sorgular için varsayılan adlandırılmış kaynak grubunun kullanılması, paylaşılan sorguları bulmayı kolaylaştırır. Ayrıca, bu kaynak grubunun amacını daha belirgin hale getirir. Bununla birlikte, mevcut izinlere göre güvenlik nedenleriyle mevcut bir kaynak grubunu seçebilirsiniz.

1. Azure portal Azure Kaynak Grafiği Gezgini ' nden uzakta ilerleyin ve sonra buna geri dönün. Kaydedilen sorgunun artık gösterilmediğine ve **sorgu 1** sekmesinin döndürüldiğine dikkat edin.

1. **Sorgu aç**' ı seçin. Türün **paylaşılan sorgu** olarak ayarlandığını, **aboneliğin** ve **kaynak grubunun** birleşimini, sorguyu kaydettiğiniz yere göre eşleştiğini doğrulayın. **İşletim sistemi öğesine göre kaydedilen sayı VM 'leri** artık **sorgu adı** listesinde görünür. Kaydedilen sorgunun başlık bağlantısını seçerek sorgunun adına sahip yeni bir sekmeye yükleyin. Paylaşılan bir sorgu olarak, başlığın yanındaki sekmede, paylaşılan olarak gösterildiği gibi bir simge görüntüler.

   ![Paylaşılan sorguyu simge ile göster](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE] 
   > Kaydedilen bir sorgu açık olduğunda ve sekme bu adı gösteriyorsa, **Kaydet** düğmesi bunu yapılmış değişikliklerle günceller. Yeni kaydedilmiş bir sorgu oluşturmak için **farklı kaydet** ' i seçin ve yepyeni bir sorgu kaydediyorsunuz gibi ilerleyin.

## <a name="discover-shared-queries"></a>Paylaşılan sorguları bulma

Paylaşılan bir sorgu Kaynak Yöneticisi kaynak olduğundan, bir tane bulmak için çeşitli yollar vardır:

- Kaynak Grafiği Gezgini ' nden **bir sorgu aç** ' ı seçin ve türü **paylaşılan sorgu**olarak ayarlayın.
- Kaynak Grafiği sorguları portalı sayfasından.
- Paylaşılan sorgunun kaydedildiği kaynak grubundan.
- Kaynak grafiğine bir sorgu üzerinden.

### <a name="view-resource-graph-queries"></a>Kaynak Grafiği sorgularını görüntüle

Azure portal, kaynak grafiği sorguları sayfasında, oturum açma hesabının erişimi olan paylaşılan sorgular görüntülenir. Bu sayfa, kaynak grafiği sorgusunun adına, aboneliğine, kaynak grubuna ve diğer özelliklerine göre filtrelemeye izin vermez. Ayrıca, bu arabirimi kullanarak kaynak grafiği sorgularını etiketleyebilir, dışarı aktarabilir ve silebilirsiniz.

Sorgulardan birini seçmek kaynak grafiği sorgu sayfasını açar. Diğer Kaynak Yöneticisi kaynaklar gibi bu sayfada, etkinlik günlüğü, erişim denetimi ve etiketleriyle birlikte etkileşimli bir genel bakış sunulmaktadır. Ayrıca, bu sayfadan doğrudan bir kaynak kilidi uygulayabilirsiniz.

**Tüm hizmetler** ' i seçerek veya tüm sayfaların en üstündeki Azure Arama kutusunu kullanarak portal menüsünden Kaynak Grafiği sorguları sayfasına gidin. **Kaynak Grafiği Gezginini**arayın ve seçin.

### <a name="list-resource-groups-resources"></a>Kaynak grupları kaynaklarını listeleme

Kaynak Grafiği sorgusu, kaynak grubunun parçası olan diğer kaynakların yanı sıra listelenir.
Kaynak Grafiği sorgusunun seçilmesi, bu sorgunun sayfasını açar. Üç nokta ve kısayol menü seçenekleri (sağ tıklatmaya tetiklenir), kaynak Graph sorgu sayfası ile aynı şekilde çalışır.

### <a name="query-resource-graph"></a>Sorgu kaynağı grafiği

Kaynak grafından bir sorgu aracılığıyla kaynak grafik sorgularını bulabilirsiniz. Aşağıdaki kaynak Graph sorgu `Microsoft.ResourceGraph/queries`türe göre kısıtlar ve sonra yalnızca adı, değiştirilme süresini ve sorgunun kendisini listelemek için `project` kullanır:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Paylaşılan bir sorguyu silme

Paylaşılan bir sorgu artık gerekmiyorsa, silin. Paylaşılan bir sorguyu silerek karşılık gelen Kaynak Yöneticisi kaynağını kaldırırsınız. Sonuçlar grafiğinin şimdi bir hata mesajı görüntülemesi için sabitlendiğini belirten panolar. Bu hata iletisi görüntülendiğinde panonuzu temizlemek için **panodan kaldır** düğmesini kullanın.

Paylaşılan bir sorguyu aşağıdaki arabirimler aracılığıyla silebilirsiniz:
- Kaynak Grafiği sorguları sayfası
- Kaynak Grafiği sorgu sayfası
- Kaynak Grafiği Gezgininde **sorgu aç** sayfası
- Kaynak grupları sayfası

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticiyi tamamladığınızda, artık istemediğiniz özel ve paylaşılan sorguları silin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Portal](../first-query-portal.md)kullanarak ilk sorgunuzu çalıştırın.
- [Sorgu dili](../concepts/query-language.md)hakkında daha fazla bilgi alın.
- [Kaynakları araştırma](../concepts/explore-resources.md)hakkında daha fazla bilgi edinin.
- Bkz. [Başlangıç sorguları](../samples/starter.md)örnekleri.
- [Gelişmiş sorguların](../samples/advanced.md)örneklerine bakın.
- [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)hakkında geri bildirim sağlayın.