---
title: Microsoft Azure StorSimple Veri Yöneticisi genel bakış | Microsoft Docs
description: StorSimple Veri Yöneticisi hizmetine genel bir bakış sağlar
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: alkohli
ms.openlocfilehash: 2ffe17bf7ef4f01c18d2c26f4a045add7302272d
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876114"
---
# <a name="storsimple-data-manager-solution-overview"></a>StorSimple Veri Yöneticisi çözüme genel bakış

## <a name="overview"></a>Genel Bakış

Microsoft Azure StorSimple, şirket içi çözümün bir uzantısı olarak bulut depolamayı kullanır ve verileri şirket içi depolama ve bulut genelinde otomatik olarak katmanlar. Veriler bulutta, en yüksek verimlilik ve maliyetleri düşürmek için Yinelenenleri kaldırılmış ve sıkıştırılmış bir biçimde depolanır. Veri StorSimple biçiminde depolandığından, kullanmak isteyebileceğiniz diğer bulut uygulamaları tarafından kolayca tüketilebilir.

StorSimple Veri Yöneticisi, bulutta StorSimple biçim verilerini sorunsuzca erişmenize ve kullanmanıza olanak sağlar. Bunu, StorSimple biçimini yerel bloblara ve dosyalara dönüştürerek Azure Media Services, Azure Hdınsights ve Azure Machine Learning gibi diğer hizmetlerle birlikte kullanabileceğiniz şekilde yapar.

Bu makalede StorSimple Veri Yöneticisi çözümüne genel bir bakış sunulmaktadır. Ayrıca, bu hizmeti kullanarak, bulutta StorSimple verileri ve diğer Azure hizmetlerini kullanan uygulamalar yazmak için de açıklanmaktadır.

## <a name="how-it-works"></a>Nasıl çalışıyor?

StorSimple Veri Yöneticisi hizmeti, buluttaki StorSimple verilerini bir StorSimple 8000 serisi şirket içi cihazdan tanımlar. Buluttaki StorSimple verileri yinelenenleri kaldırılmış, sıkıştırılmış StorSimple biçimidir. Veri Yöneticisi hizmeti, StorSimple biçim verilerini ayıklamak ve Azure Blob 'ları ve Azure dosyaları gibi diğer biçimlere dönüştürmek için API 'Ler sağlar. Dönüştürülen bu veriler daha sonra Azure HDInsight ve Azure Media Services tarafından kullanılır. Bu nedenle veri dönüştürme, bu hizmetlerin StorSimple 8000 serisi şirket içi cihazdan dönüştürülmüş StorSimple verileri üzerinde çalışmasını sağlar. Bu akış aşağıdaki diyagramda gösterilmiştir.

![Üst düzey diyagram](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Veri Yöneticisi kullanım örnekleri

Azure Işlevleri, Azure Otomasyonu ve Azure Data Factory ile Veri Yöneticisi kullanarak, veri üzerinde iş akışlarının StorSimple 'a geldiği şekilde çalışmasını sağlayabilirsiniz. StorSimple üzerinde depoladığınız medya içeriğinizi Azure Media Services işlemek veya bu verilerde bir Machine Learning algoritması çalıştırmak veya StorSimple üzerinde depoladığınız verileri çözümlemek için bir Hadoop kümesi getirmek isteyebilirsiniz. Azure 'da sunulan çok sayıda hizmet, StorSimple 'daki verilerle birlikte kullanıldığında verilerinizin gücünden de yararlanın.


## <a name="region-availability"></a>Bölge kullanılabilirliği

StorSimple Veri Yöneticisi aşağıdaki 7 bölgede kullanılabilir:

 - Güneydoğu Asya
 - East US
 - Batı ABD
 - Batı ABD 2
 - Batı Orta ABD
 - Kuzey Avrupa
 - Batı Avrupa

Ancak, StorSimple Veri Yöneticisi aşağıdaki bölgelerdeki verileri dönüştürmek için kullanılabilir. 

![Veriler için kullanılabilir bölgeler](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Yukarıdaki bölgelerin herhangi birinde bulunan kaynak dağıtımı, aşağıdaki bölgelerde dönüşüm işlemini getirmek için uygun olduğundan, bu küme daha büyüktür. Bu nedenle, verileriniz 19 bölgeden birinde bulunduğu sürece bu hizmeti kullanarak verilerinizi dönüştürebilirsiniz.


## <a name="choosing-a-region"></a>Bölge seçme

Şunları öneririz:
 - Kaynak depolama hesabınız (StorSimple aygıtınızla ilişkili olan) ve hedef depolama hesabı (yerel biçimdeki verilerin bulunduğu konum) aynı Azure bölgesinde olmalıdır.
 - Veri Yöneticisi ve iş tanımınızı StorSimple depolama hesabını içeren bölgeye taşıyın. Bu mümkün değilse, en yakın Azure bölgesindeki Veri Yöneticisi alın ve ardından Iş tanımını StorSimple depolama hesabınızla aynı bölgede oluşturun. 

    StorSimple depolama hesabınız, iş tanımı oluşturmayı destekleyen 26 bölgede değilse, uzun gecikme süreleri ve olası çıkış ücretleri gördüğünüz için StorSimple Veri Yöneticisi çalıştırılmamalıdır.
    
Microsoft, tüm bölgelerde Azure hizmetlerinin her zaman kullanılabilir olduğundan emin olmaya çalışır. Ancak, belirli bir bölgedeki kısa dönemler için plansız hizmet kesintileri meydana gelebilir. Böyle durumlarda, kesinti tarafından etkilenmeyen bir bölgede Veri Yöneticisi ve iş tanımını alabilir ve dönüştürme işini çalıştırabilirsiniz. Böyle bir senaryoda bazı ek gecikme süreleriyle karşılaşabilirsiniz, ancak bu durum, bölgesel bir kesinti nedeniyle nadir bir olayda kurtarma stratejiniz olabilir.

## <a name="security-considerations"></a>Güvenlik konuları

StorSimple Veri Yöneticisi, StorSimple biçiminden yerel biçime dönüştürmek için hizmet veri şifreleme anahtarına ihtiyaç duyuyor. Hizmet veri şifreleme anahtarı, ilk cihaz StorSimple hizmetine kaydolduğunda oluşturulur. Bu anahtar hakkında daha fazla bilgi için, [StorSimple Security](storsimple-8000-security.md)'ye gidin.

Giriş olarak girilen hizmet veri şifreleme anahtarı, bir Veri Yöneticisi oluşturduğunuzda oluşturulan bir anahtar kasasında depolanır. Kasa StorSimple Veri Yöneticisi aynı Azure bölgesinde yer alır. Bu anahtar, Veri Yöneticisi hizmetinizi sildiğinizde silinir.

Bu anahtar, dönüştürmeyi gerçekleştirmek için işlem kaynakları tarafından kullanılır. Bu işlem kaynakları, iş tanımınız ile aynı Azure bölgesinde bulunur. Bu bölge, Veri Yöneticisi aldığınız bölgeyle aynı olabilir veya olmayabilir.

Veri Yöneticisi bölgeniz iş tanımı bölgeinizden farklıysa, bu bölgelerin her birinde hangi verilerin/meta verilerin bulunduğunu anlamanız önemlidir. Aşağıdaki diyagramda Veri Yöneticisi ve iş tanımı için farklı bölgelere sahip olmanın etkisi gösterilmektedir.

![Farklı bölgelerde hizmet ve iş tanımı](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Kişisel bilgileri yönetme

StorSimple Veri Yöneticisi herhangi bir kişisel bilgi toplamaz veya görüntülemez. Daha fazla bilgi için, [Güven Merkezi](https://www.microsoft.com/trustcenter)’nde Microsoft Gizlilik ilkesini gözden geçirin.

## <a name="known-limitations"></a>Bilinen Sınırlamalar

Hizmet şu anda aşağıdaki sınırlamalara sahiptir:
- StorSimple Veri Yöneticisi şu anda BitLocker şifreli birimlerle çalışmaz. Hizmeti şifrelenmiş bir sürücüyle çalıştırmayı denerseniz, iş arızalarını görürsünüz.
- Dosyaların bazı meta verileri (ACL 'Ler dahil) dönüştürülen verilerde korunmaz.
- Bu hizmet yalnızca NTFS birimleri ile birlikte kullanılabilir.
- Dosya yolu uzunluklarının 256 karakterden az olması gerekir, aksi takdirde iş başarısız olur.

## <a name="next-steps"></a>Sonraki adımlar

[Verilerinizi dönüştürmek için StorSimple veri Yöneticisi Kullanıcı arabirimini kullanın](storsimple-data-manager-ui.md).
