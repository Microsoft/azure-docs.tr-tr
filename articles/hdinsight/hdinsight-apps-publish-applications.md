---
title: Azure HDInsight uygulamalarını yayımlama
description: HDInsight uygulaması oluşturmayı ve sonra Azure Marketi 'nde yayımlamayı öğrenin.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 05/14/2018
ms.openlocfilehash: d432d20660cd1b7ee59c9ef72f9db04bc0009481
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306594"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Azure Marketi 'nde HDInsight uygulaması yayımlama
Bir Azure HDInsight uygulamasını Linux tabanlı HDInsight kümesine yükleyebilirsiniz. Bu makalede, Azure Marketi 'nde bir HDInsight uygulaması yayımlamayı öğrenin. Azure Marketi 'nde yayımlama hakkında genel bilgi için bkz. [Azure Marketi 'nde teklif yayımlama](../marketplace/overview.md).

HDInsight uygulamaları *kendi lisansını getir (KLG)* modelini kullanır. Bir KLG senaryosunda, uygulamanın uygulama kullanıcılarına lisanslanması bir uygulama sağlayıcısı sorumludur. Uygulama kullanıcıları yalnızca kendi oluşturdukları, HDInsight kümesi ve kümenin VM 'Leri ve düğümleri gibi Azure kaynakları için ücretlendirilir. Şu anda, uygulamanın kendisi için faturalandırma Azure 'da gerçekleşmiyor.

Daha fazla bilgi için bkz. HDInsight uygulamasıyla ilgili bu makaleler:

* [HDInsight uygulamalarını yükler](hdinsight-apps-install-applications.md). Kümelerinize HDInsight uygulaması yüklemeyi öğrenin.
* [Özel HDInsight uygulamalarını yükler](hdinsight-apps-install-custom-applications.md). Özel HDInsight uygulamalarını yüklemeyi ve test yapmayı öğrenin.

## <a name="prerequisites"></a>Önkoşullar
Özel uygulamanızı Market 'te göndermek için öncelikle [özel uygulamanızı oluşturun ve test](hdinsight-apps-install-custom-applications.md)edin.

Geliştirici hesabınızı da kaydetmeniz gerekir. Daha fazla bilgi için bkz. [Azure Marketi 'nde teklif yayımlama](../marketplace/overview.md) ve [bir Microsoft Geliştirici hesabı oluşturma](../marketplace/overview.md).

## <a name="define-the-application"></a>Uygulamayı tanımlama
Market 'te uygulamaları yayımlamayla ilgili iki adım vardır. İlk olarak, dosyasında bir *createUiDef.js* tanımlayın. Dosyadaki createUiDef.js, uygulamanızın hangi kümelerle uyumlu olduğunu gösterir. Ardından, şablonu Azure portal yayımlayın. Dosyadaki bir örnek createUiDef.jsaşağıda verilmiştir:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| Alan | Açıklama | Olası değerler |
| --- | --- | --- |
| types |Uygulamanın uyumlu olduğu küme türleri. |Hadoop, HBase, fırtınası, Spark (veya bunların herhangi bir birleşimi) |
| versions |Uygulamanın uyumlu olduğu HDInsight küme türleri. |3.4 |

## <a name="application-installation-script"></a>Uygulama yükleme betiği
Bir uygulama bir kümeye yüklendiğinde (mevcut bir kümede veya yeni bir kümede), bir kenar düğümü oluşturulur. Uygulama yükleme betiği kenar düğümünde çalışır.

  > [!IMPORTANT]  
  > Uygulama yükleme komut dosyasının adı, belirli bir küme için benzersiz olmalıdır. Betik adı aşağıdaki biçimde olmalıdır:
  > 
  > "ad": "[Concat (' ton-Install-V0 ', '-', uniquestring (' applicationName ')]"
  > 
  > Betik adı üç bölümden oluşur:
  > 
  > * Uygulama adını ya da uygulamayla ilgili bir adı içermesi gereken bir betik adı ön eki.
  > * Okunabilirlik için kısa çizgi.
  > * Parametre olarak uygulama adına sahip benzersiz bir dize işlevi.
  > 
  > Kalıcı betik eylem listesinde, yukarıdaki örnek, **ton-Install-v0-4wkahss55hlas** olarak görüntülenir. Bkz. [Örnek BIR JSON yükü](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Yükleme betiği aşağıdaki özelliklere sahip olmalıdır:
* Betik ıdempotent. Betiğe birden çok çağrı aynı sonucu üretir.
* Betiğin sürümü doğru değil. Değişiklikleri yükseltirken veya test ederken betik için farklı bir konum kullanın. Bu, uygulamayı yükleyen müşterilerin güncelleştirmelerden veya test etmesinden etkilenmemesini sağlar. 
* Betiğin her noktada yeterli günlük kaydı vardır. Genellikle, komut dosyası günlükleri, uygulama yükleme sorunlarını ayıklamanın tek yoludur.
* Dış hizmetlere veya kaynaklara yapılan çağrıların, yüklemenin geçici ağ sorunlarından etkilenmemesi için yeterli yeniden denemeleri vardır.
* Komut dosyası düğümlerde Hizmetleri başlatırsanız, hizmetler izlenir ve bir düğümün yeniden başlatılması gerçekleşirse otomatik olarak başlayacak şekilde yapılandırılır.

## <a name="package-the-application"></a>Uygulamayı paketleyin
HDInsight uygulamanızı yüklemek için gereken tüm dosyaları içeren bir. zip dosyası oluşturun. Uygulamayı yayımlamak için. zip dosyasını kullanın. . Zip dosyası aşağıdaki dosyaları içerir:

* Üzerinde createUiDefinition.js
* mainTemplate.js(bir örnek Için bkz. [özel HDInsight uygulamalarını yüklemeyi](hdinsight-apps-install-custom-applications.md).)
* Tüm gerekli betikler

> [!NOTE]  
> Uygulama dosyalarını (herhangi bir Web uygulaması dosyası dahil) herkese açık olarak erişilebilen herhangi bir uç noktada barındırabilirsiniz.

## <a name="publish-the-application"></a>Uygulamayı yayımlama
HDInsight uygulaması yayımlamak için:

1. Azure yayımlaması ' nda oturum açın.

2. Sol taraftaki menüden **Çözüm şablonları**' nı seçin.
3. Bir başlık girin ve ardından **yeni çözüm şablonu oluştur**' u seçin.
4. Kuruluşunuzda henüz kaydolmadıysanız **Geliştirme Merkezi hesabı oluştur ' u seçin ve Azure programına katın**.  Daha fazla bilgi için bkz. [Microsoft Geliştirici hesabı oluşturma](../marketplace/overview.md).
5. Başlamak **için bazı topolojiler tanımla**' yı seçin. Bir çözüm şablonu, tüm Topolojilerine bir "üst" öğesidir. Tek bir teklifte veya çözüm şablonunda birden fazla topoloji tanımlayabilirsiniz. Bir teklif hazırlama için gönderildiğinde, tüm topolojilerle gönderilir. 
6. Bir topoloji adı girin ve ardından öğesini seçin **+** .
7. Yeni bir sürüm girin ve ardından öğesini seçin **+** .
8. Uygulamayı paketlediğiniz sırada oluşturduğunuz. zip dosyasını karşıya yükleyin.  
9. **Sertifika iste**' yi seçin. Microsoft sertifika ekibi, dosyaları inceler ve topolojiyi sertifikalandırın.

## <a name="next-steps"></a>Sonraki adımlar
* Kendi kümelerinizde [HDInsight uygulamaları yüklemeyi](hdinsight-apps-install-applications.md) öğrenin.
* HDInsight 'a [özel HDInsight uygulamaları yüklemeyi](hdinsight-apps-install-custom-applications.md) ve yayımlanmamış bir HDInsight uygulamasını dağıtmayı öğrenin.
* [Linux tabanlı HDInsight kümelerini özelleştirmek](hdinsight-hadoop-customize-cluster-linux.md) ve daha fazla uygulama eklemek Için betik eylemini nasıl kullanacağınızı öğrenin. 
* [HDInsight 'ta Azure Resource Manager şablonları kullanarak Linux tabanlı Apache Hadoop kümeleri oluşturmayı](hdinsight-hadoop-create-linux-clusters-arm-templates.md)öğrenin.
* HDInsight 'ta HDInsight kümelerine erişmek, HDInsight uygulamalarını test etmek ve HDInsight uygulamalarını barındırmak için [boş bir kenar düğümünü kullanmayı](hdinsight-apps-use-edge-node.md) öğrenin.
