---
title: 'Hızlı başlangıç: ilk portal sorgunuz'
description: Bu hızlı başlangıçta, Azure Kaynak Grafiği gezginini kullanarak Azure portal ilk sorgunuzu çalıştırmak için adımları izleyin.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 07e336121c6338b27c018acb5b2332653663e1b4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533079"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Hızlı başlangıç: Azure Kaynak Grafiği gezginini kullanarak ilk kaynak Graph sorgunuzu çalıştırma

Azure Kaynak Graph 'ın gücü Azure Kaynak grafik Gezgini aracılığıyla Azure portal doğrudan kullanılabilir. Kaynak grafik Gezgini, sorgulayabilmeniz için Azure Resource Manager kaynak türleri ve özellikleri hakkında gözatılabilir bilgiler sağlar. Kaynak Grafiği Gezgini Ayrıca birden çok sorgu ile çalışmak, sonuçları değerlendirmek ve hatta bazı sorguların sonuçlarını bir Azure panosuna sabitlenebilir bir grafiğe dönüştürmek için temiz bir arabirim sağlar.

Bu hızlı başlangıçta, ilk kaynak Graph sorgunuzu çalıştırmak ve sonuçları bir panoya sabitlediğiniz Azure portal ve kaynak Graph Explorer 'ı kullandınız.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="run-your-first-resource-graph-query"></a>İlk Kaynak Grafiği sorgunuzu çalıştırma

İlk kaynak grafik sorgunuzu çalıştırmak için aşağıdaki adımları izleyerek kaynak grafiği Gezginini bulmak ve kullanmak üzere [Azure Portal](https://portal.azure.com) açın:

1. Sol bölmede **Tüm hizmetler**'i seçin. **Kaynak Grafiği Gezginini** arayın ve seçin.

1. Pencerenin **sorgu 1** bölümünde sorguyu girin `Resources | project name, type | limit 5` ve **Sorguyu Çalıştır**' ı seçin.

   > [!NOTE]
   > Bu sorgu örneği gibi bir sıralama değiştiricisi sağlamadığından `order by` , bu sorguyu birden çok kez çalıştırmak, istek başına farklı bir kaynak kümesi sunmamasından kaynaklanıyor olabilir.

1. **Sonuçlar** sekmesindeki sorgu yanıtını gözden geçirin. Sorgu hakkındaki ayrıntıları ve sorgu süresini görmek için **iletiler** sekmesini seçin. Varsa, bu sekme altında hatalar görüntülenir.

1. Sorguyu `order by` **Name** özelliği olarak güncelleştirin: `Resources | project name, type | limit 5 | order by name asc` . Sonra **Sorguyu Çalıştır**' ı seçin.

   > [!NOTE]
   > İlk sorguda olduğu gibi, bu sorguyu birden çok kez çalıştırmak, muhtemelen istek başına farklı bir kaynak kümesi sunacaktır. Sorgu komutlarının düzeni önemlidir. Bu örnekte `order by`, `limit`’den sonra gelmektedir. Bu komut sırası ilk olarak sorgu sonuçlarını kısıtlar ve sonra sıralar.

1. Sorguyu önce `order by` **ad** özelliğine, sonra da `limit` en üstteki beş sonuca güncelleştirin: `Resources | project name, type | order by name asc | limit 5` . Sonra **Sorguyu Çalıştır**' ı seçin.

Son sorgu birkaç kez çalıştırıldığında, ortamınızdaki hiçbir şeyin değişmediği kabul edildiğinde döndürülen sonuçlar tutarlı ve **ad** özelliğine göre sıralanır, ancak yine de en üstteki beş sonuçla sınırlıdır.

### <a name="schema-browser"></a>Şema tarayıcısı

Şema tarayıcısı, kaynak grafik Gezgini 'nin sol bölmesinde bulunur. Bu kaynak listesi, Azure Kaynak Grafiği tarafından desteklenen ve erişim sahibi olduğunuz bir kiracıda bulunan Azure kaynaklarının tüm _kaynak türlerini_ gösterir. Kaynak türü veya alt özellikleri genişletmek, kaynak grafik sorgusu oluşturmak için kullanılabilecek alt özellikleri gösterir.

Kaynak türünü seçme `where type =="<resource type>"` sorgu kutusuna yer koyar. Alt özelliklerden birini seçmek `where <propertyName> == "INSERT_VALUE_HERE"` sorgu kutusuna eklenir.
Şema tarayıcısı, sorgularda kullanılacak özellikleri bulmayı çok iyi bir yoldur. _\_ \_ Buraya, ekleme değerini_ kendi değer ile değiştirdiğinizden emin olun, sorguyu koşullar, işleçler ve işlevlerle ayarlayıp amaçlanan sonuçlara ulaşın.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Kaynak grafik sorgusundan grafik oluşturma

Önceki sorguyu çalıştırdıktan sonra, **grafikler** sekmesini seçerseniz "sonuç kümesi bir pasta grafik görselleştirmesi ile uyumlu değildir." iletisini alırsınız. Sonuçları listelemek için gereken sorgular bir grafiğe yapılamaz, ancak kaynak sayısı sağlayan sorgular olabilir. [Örnek sorgu-sayı sanal makinelerini işletim sistemi türüne göre](./samples/starter.md#count-os)kullanarak, kaynak Graph sorgusundan bir görselleştirme oluşturalım.

1. Pencerenin **sorgu 1** bölümünde aşağıdaki sorguyu girin ve **Sorguyu Çalıştır**' ı seçin.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. **Sonuçlar** sekmesini seçin ve bu sorguya yönelik yanıtın sayımlar sağladığını unutmayın.

1. **Grafikler** sekmesini seçin. Artık sorgu görselleştirmelere neden olur. Kullanılabilir görselleştirme seçenekleriyle denemeler yapmak için _grafik türünü seçin..._ ' yi _çubuk grafik_ veya _halka grafik_ olarak değiştirin.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>Sorgu görselleştirmesini panoya sabitleme

Görselleştirilebilen bir sorgunun sonuçları varsa, bu veri görselleştirmesi daha sonra panolarınızdan birine sabitlenebilir. Önceki sorguyu çalıştırdıktan sonra aşağıdaki adımları izleyin:

1. **Kaydet** ' i seçin ve "Işletim sistemi türüne göre VM 'ler" adını sağlayın. Ardından sağ bölmenin alt kısmındaki **Kaydet** ' i seçin.

1. Sorguyu yeniden **çalıştırmak için sorguyu Çalıştır** ' ı seçin.

1. **Grafikler** sekmesinde bir veri görselleştirme seçin. Sonra **panoya sabitle**' yi seçin.

1. Görüntülenen Portal bildirimini seçin ya da sol bölmeden **Pano** ' yı seçin.

Sorgu artık panonuzda sorgu adıyla eşleşen kutucuğun başlığı ile kullanılabilir. Sorgu sabitlenmemişse kaydedilmemiş ise bunun yerine ' Query 1 ' adı gelir.

Sorgu ve sonuçta elde edilen veri görselleştirme, pano her yüklendiğinde, Azure ortamınızda doğrudan iş akışınızda gerçek zamanlı ve dinamik Öngörüler sağlayan her seferinde çalışır ve güncelleştirir.

> [!NOTE]
> Bir liste ile sonuçlanan sorgular da panoya sabitlenebilir. Özellik, sorguların veri görselleştirmeleriyle sınırlı değildir.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Örnek kaynak grafik Gezgini panoları içeri aktarma

Kaynak Grafiği sorgularına örnek sağlamak ve kaynak Graph Explorer 'ın Azure portal iş akışınızı iyileştirmek için nasıl kullanılabileceğini sağlamak için bu örnek panoları deneyin.

- [Kaynak Grafiği Gezgini-örnek Pano #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  :::image type="content" source="./media/arge-sample1-small.png" alt-text="Örnek Pano için örnek resim #1" lightbox="./media/arge-sample1-large.png":::

- [Kaynak Grafiği Gezgini-örnek Pano #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  :::image type="content" source="./media/arge-sample2-small.png" alt-text="Örnek Pano için örnek resim #2" lightbox="./media/arge-sample2-large.png":::

> [!NOTE]
> Yukarıdaki örnek Pano ekran görüntülerinin sayısı ve grafikleri, Azure ortamınıza bağlı olarak farklılık gösterir.

1. Değerlendirmek istediğiniz örnek panoyu seçin ve indirin.

1. Azure portal sol bölmeden **Pano** ' yı seçin.

1. **Karşıya yükle**' yi seçin, ardından indirilen örnek Pano dosyasını bulun ve seçin. Sonra **Aç**' ı seçin.

İçeri aktarılan pano otomatik olarak görüntülenir. Azure portal artık mevcut olduğundan, takımınızla paylaşmak için, gerektiğinde değişiklikler yapabilir veya örnek üzerinde yeni panolar oluşturabilirsiniz. Panolarla çalışma hakkında daha fazla bilgi için, [Azure Portal panoları oluşturma ve paylaşma](../../azure-portal/azure-portal-dashboards.md)konusuna bakın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure portal ortamınızdan örnek kaynak Graflarını kaldırmak istiyorsanız, bunu aşağıdaki adımlarla yapabilirsiniz:

1. Sol bölmeden **Pano** ' yı seçin.

1. Pano açılır listesinden silmek istediğiniz örnek kaynak grafik panosunu seçin.

1. Panonun üstündeki Pano menüsünden **Sil** ' i seçin ve onaylamak için **Tamam** ' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, ilk sorgunuzu çalıştırmak ve kaynak Graph tarafından desteklenen Pano örneklerine bakarak Azure Kaynak Grafiği Gezginini kullandınız. Kaynak grafik dili hakkında daha fazla bilgi edinmek için sorgu dili ayrıntıları sayfasına ilerleyin.

> [!div class="nextstepaction"]
> [Sorgu dili hakkında daha fazla bilgi alın](./concepts/query-language.md)
