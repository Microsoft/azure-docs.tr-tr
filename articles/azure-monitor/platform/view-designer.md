---
title: Azure Izleyici 'de günlük verilerini çözümlemek için görünümler oluşturun | Microsoft Docs
description: Azure Izleyici 'de Görünüm Tasarımcısı 'nı kullanarak, Azure portal görüntülenen ve Log Analytics çalışma alanındaki veriler üzerinde çeşitli görselleştirmeler içeren özel görünümler oluşturabilirsiniz. Bu makale, görünüm tasarımcısına genel bir bakış içerir ve özel görünümleri oluşturma ve düzenlemeyle ilgili yordamları sunar.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: a1a4dbffed37480178d1b94a77587ca251396db6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931946"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Azure Izleyici 'de Görünüm Tasarımcısı 'nı kullanarak özel görünümler oluşturma
Azure Izleyici 'de Görünüm Tasarımcısı 'nı kullanarak, Azure portal Log Analytics çalışma alanınızdaki verileri görselleştirmenize yardımcı olabilecek çeşitli özel görünümler oluşturabilirsiniz. Bu makalede, özel görünümleri oluşturma ve düzenlemeyle ilgili görünüm tasarımcısına ve yordamlarına ilişkin bir genel bakış sunulmaktadır.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Görünüm Tasarımcısı hakkında daha fazla bilgi için bkz.

* [Kutucuk başvurusu](view-designer-tiles.md): özel görünümlerinizin her bir kullanılabilir kutucuğun ayarlarına yönelik bir başvuru kılavuzu sağlar.
* [Görselleştirme bölümü başvurusu](view-designer-parts.md): özel görünümlerinizdeki kullanılabilir görselleştirme bölümlerinin ayarlarına bir başvuru kılavuzu sağlar.


## <a name="concepts"></a>Kavramlar
Görünümler, Azure portal Azure Izleyici **genel bakış** sayfasında görüntülenir. **Öngörüler** bölümünün altında **daha fazla** ' ya tıklayarak bu sayfayı **Azure izleyici** menüsünden açın. Her özel görünümdeki kutucuklar alfabetik olarak görüntülenir ve izleme çözümlerinin kutucukları aynı çalışma alanına yüklenir.

![Genel Bakış sayfası](media/view-designer/overview-page.png)

Görünüm Tasarımcısı ile oluşturduğunuz görünümler aşağıdaki tabloda açıklanan öğeleri içerir:

| Bölümüyle | Açıklama |
|:--- |:--- |
| Parça | , Azure Izleyici **genel bakış** sayfasında görüntülenir. Her kutucuk, gösterdiği özel görünümün görsel özetini görüntüler. Her döşeme türü, kayıtlarınızın farklı bir görselleştirmesini sağlar. Özel bir görünüm görüntülemek için bir kutucuk seçersiniz. |
| Özel görünüm | Bir kutucuk seçtiğinizde gösterilir. Her görünüm bir veya daha fazla görselleştirme bölümü içerir. |
| Görselleştirme parçaları | Bir veya daha fazla [günlük sorgusuna](../log-query/log-query-overview.md)göre Log Analytics çalışma alanında bir veri görselleştirmesi sunun. Çoğu bölüm, üst düzey bir görselleştirme sağlayan bir üst bilgi ve en üstteki sonuçları görüntüleyen bir liste içerir. Her bölüm türü, Log Analytics çalışma alanındaki kayıtların farklı bir görselleştirmesini sağlar. Ayrıntılı kayıtlar sağlayan bir günlük sorgusu gerçekleştirmek için bölümünde öğeleri seçersiniz. |

## <a name="required-permissions"></a>Gerekli izinler
Görünümleri oluşturmak veya değiştirmek için Log Analytics çalışma alanında en az [katkıda bulunan düzeyi izinlerinizin](manage-access.md#manage-access-using-azure-permissions) olması gerekir. Bu izne sahip değilseniz menüde Görünüm Tasarımcısı seçeneği gösterilmez.


## <a name="work-with-an-existing-view"></a>Mevcut bir görünümle çalışma
Görünüm Tasarımcısı ile oluşturulan görünümler aşağıdaki seçenekleri görüntüler:

![Genel Bakış menüsü](media/view-designer/overview-menu.png)

Seçenekler aşağıdaki tabloda açıklanmıştır:

| Seçenek | Açıklama |
|:--|:--|
| Yenile   | En son verilerle görünümü yeniler. | 
| Günlükler      | Günlük sorgularıyla verileri çözümlemek için [Log Analytics](../log-query/portals.md) açar. |
| Düzenle       | İçeriğini ve yapılandırmasını düzenlemek için Görünüm Tasarımcısı ' nda görünümü açar.  |
| Kopyala      | Yeni bir görünüm oluşturur ve onu Görünüm Tasarımcısı 'nda açar. Yeni görünümün adı özgün adla aynıdır, ancak buna *kopyalama* eklenir. |
| Tarih aralığı | Görünümde yer alan veriler için tarih ve saat aralığı filtresini ayarlayın. Bu tarih aralığı, görünümdeki sorgularda ayarlanan herhangi bir tarih aralığından önce uygulanır.  |
| +          | Görünüm için tanımlanan özel bir filtre tanımlayın. |


## <a name="create-a-new-view"></a>Yeni görünüm oluştur
Log Analytics çalışma alanınızın menüsündeki **Görünüm Tasarımcısı** ' nı seçerek görünüm Tasarımcısı ' nda yeni bir görünüm oluşturabilirsiniz.

![Tasarımcı kutucuğunu görüntüle](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Görünüm Tasarımcısı ile çalışma
Yeni görünümler oluşturmak veya varolanları düzenlemek için Görünüm Tasarımcısı 'nı kullanın. 

Görünüm tasarımcısının üç bölmesi vardır: 
* **Tasarım**: oluşturmakta veya düzenlemekte olduğunuz özel görünümü içerir. 
* **Denetimler**: **Tasarım** bölmesine eklediğiniz kutucukları ve parçaları içerir. 
* **Özellikler**: kutucukların veya seçili parçaların özelliklerini görüntüler.

![View Designer (Görünüm Tasarımcısı)](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Görünüm kutucuğunu yapılandırma
Özel bir görünüm yalnızca tek bir kutucuğa sahip olabilir. Geçerli kutucuğu görüntülemek veya alternatif bir tane seçmek için **Denetim** bölmesinde **kutucuk** sekmesini seçin. **Özellikler** bölmesi, geçerli kutucuğun özelliklerini görüntüler. 

Kutucuk özelliklerini [kutucuk başvurusunda](view-designer-tiles.md) bulunan bilgilere göre yapılandırabilir ve ardından değişiklikleri kaydetmek için **Uygula** ' ya tıklayabilirsiniz.

### <a name="configure-the-visualization-parts"></a>Görselleştirme parçalarını yapılandırma
Bir görünüm herhangi bir sayıda görselleştirme bölümü içerebilir. Bir görünüme parçalar eklemek için **Görünüm** sekmesini seçin ve ardından bir görselleştirme bölümü seçin. **Özellikler** bölmesi, seçili parçanın özelliklerini görüntüler. 

Görünüm özelliklerini [görselleştirme bölümü başvurusu](view-designer-parts.md) 'ndaki bilgilere göre yapılandırabilir ve ardından değişiklikleri kaydetmek için **Uygula** ' ya tıklayabilirsiniz.

Görünümler yalnızca bir görselleştirme bölümlerinin bir satırına sahiptir. Varolan bölümleri yeni bir konuma sürükleyerek yeniden düzenleyebilirsiniz.

Görünümün sağ üst köşesinde bulunan **X** ' i seçerek bir görselleştirme parçasını görünümden kaldırabilirsiniz.


### <a name="menu-options"></a>Menü seçenekleri
Düzenleme modunda görünümlerle çalışma seçenekleri aşağıdaki tabloda açıklanmıştır.

![Düzenle menüsü](media/view-designer/edit-menu.png)

| Seçenek | Açıklama |
|:--|:--|
| Kaydet        | Değişikliklerinizi kaydeder ve görünümü kapatır. |
| İptal      | Değişikliklerinizi atar ve görünümü kapatır. |
| Görünümü Sil | Görünümü siler. |
| Dışarı Aktar      | Görünümü, başka bir çalışma alanına aktarabileceğiniz bir [Azure Resource Manager şablonuna](../../azure-resource-manager/resource-group-authoring-templates.md) dışarı aktarır. Dosyanın adı, görünümün adıdır ve bir *omsview* uzantısına sahiptir. |
| İçeri Aktarma      | Başka bir çalışma alanından verdiğiniz *omsview* dosyasını içeri aktarır. Bu eylem, var olan görünümün yapılandırmasının üzerine yazar. |
| Kopyala       | Yeni bir görünüm oluşturur ve onu Görünüm Tasarımcısı 'nda açar. Yeni görünümün adı özgün adla aynıdır, ancak buna *kopyalama* eklenir. |

## <a name="next-steps"></a>Sonraki adımlar
* Özel görünüminizdeki [kutucukları](view-designer-tiles.md) ekleyin.
* [Görselleştirme parçalarını](view-designer-parts.md) özel görünümverilerinize ekleyin.
