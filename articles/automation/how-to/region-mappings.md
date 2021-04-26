---
title: Bağlı Log Analytics çalışma alanı için desteklenen bölgeler
description: Bu makalede, bir Otomasyon hesabı ile Log Analytics çalışma alanı arasındaki desteklenen bölge eşlemeleri, Azure Otomasyonu 'nun belirli özellikleriyle ilişkili olarak açıklanmaktadır.
ms.date: 04/01/2021
services: automation
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 4ac2a575c9fe551d5b1b396ab06b2735a749f9da
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221883"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Bağlı Log Analytics çalışma alanı için desteklenen bölgeler

Azure Otomasyonu 'nda sunucularınız ve sanal makineleriniz için Güncelleştirme Yönetimi, Değişiklik İzleme ve envanteri ve VM'leri çalışma saatleri dışında başlat/durdur özelliklerini etkinleştirebilirsiniz. Bu özelliklerin bir Log Analytics çalışma alanına bağımlılığı vardır ve bu nedenle çalışma alanını bir Otomasyon hesabıyla bağlamayı gerektirir. Ancak, bunları birbirine bağlamak için yalnızca belirli bölgeler desteklenir. Genellikle, bir Otomasyon hesabını bu özellikleri etkin olmayacak bir çalışma alanına bağlamayı planlıyorsanız eşleme geçerli *değildir* .

Burada açıklanan eşlemeler yalnızca Log Analytics çalışma alanını bir Otomasyon hesabına bağlamak için geçerlidir. Otomasyon hesabına bağlı olan çalışma alanına bağlı sanal makineler (VM) için uygulanamazlar. VM 'Ler, belirli bir Log Analytics çalışma alanı tarafından desteklenen bölgelerle sınırlı değildir. Bunlar herhangi bir bölgede olabilir. Farklı bir bölgedeki sanal makinelerin durum, yerel ve ülke mevzuatı gereksinimlerini veya şirketinizin uyumluluk gereksinimlerini etkileyebileceğini aklınızda bulundurun. VM 'Lerin farklı bir bölgede olması, veri bant genişliği ücretleri de getirebilir.

VM 'Leri farklı bir bölgedeki bir çalışma alanına bağlamadan önce, yasal ve maliyet etkilerini doğrulamak ve anlamak için gereksinimleri ve potansiyel maliyetleri gözden geçirmeniz gerekir.

Bu makalede, Otomasyon hesabınızda bu özellikleri başarılı bir şekilde etkinleştirmek ve kullanmak için desteklenen eşlemeler sağlanmaktadır.

Daha fazla bilgi için bkz. [Log Analytics çalışma alanı ve Otomasyon hesabı](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Desteklenen eşlemeler

> [!NOTE]
> Aşağıdaki tabloda gösterildiği gibi, Log Analytics ve Azure Otomasyonu arasında yalnızca bir eşleme bulunabilir.

Aşağıdaki tabloda desteklenen eşlemeler gösterilmektedir:

|**Log Analytics çalışma alanı bölgesi**|**Azure Otomasyonu bölgesi**|
|---|---|
|**ABD**||
|EastUS<sup>1</sup>|EastUS2|
|EastUS2<sup>2</sup>|EastUS|
|WestUS|WestUS|
|WestUS2|WestUS2|
|Kuzeydoğu ABD|Kuzeydoğu ABD|
|CentralUS|CentralUS|
|Güneydoğu ABD|Güneydoğu ABD|
|WestCentralUS|WestCentralUS|
|**Brezilya**||
|BrazilSouth|BrazilSouth|
|**Kanada**||
|Canadaorta|Canadaorta|
|**Çin**||
|ChinaEast2<sup>3</sup>|ChinaEast2|
|**Asya Pasifik**||
|Eastaya|Eastaya|
|Güneydoğu|Güneydoğu|
|**Hindistan**||
|Merkezileştirme Hindistan|Merkezileştirme Hindistan|
|**Japonya**||
|JapanEast|JapanEast|
|**Avustralya**||
|AustraliaEast|AustraliaEast|
|AustraliaSoutheast|AustraliaSoutheast|
|**Güney Kore**||
|KoreaCentral|KoreaCentral|
|**Norveç**||
|Norwaydoğu|Norwaydoğu|
|**Avrupa**||
|NorthEurope|NorthEurope|
|WestEurope|WestEurope|
|**Fransa**||
|Francecna al|Francecna al|
|**Birleşik Krallık**
|UKSouth|UKSouth|
|**İsviçre**||
|Geçiş|Geçiş|
|**Birleşik Arap Emirlikleri**||
|UAENorth|UAENorth|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|USGovArizona<sup>3</sup>|USGovArizona|

Log Analytics çalışma alanları için <sup>1</sup> EastUS eşlemesi, Otomasyon hesaplarında tam bir bölgeden bölgeye eşleme değildir, ancak doğru eşleme olur.

<sup>2</sup> EastUS2 çalışma alanları için Log Analytics, Otomasyon hesaplarında tam bir bölgeden bölgeye eşleme değildir, ancak doğru eşleme olur.

<sup>3</sup> bu bölgede yalnızca güncelleştirme yönetimi desteklenir ve değişiklik izleme ve envanter gibi diğer özellikler Şu anda kullanılamaz.

## <a name="unlink-a-workspace"></a>Çalışma alanının bağlantısını kaldır

Artık Otomasyon hesabınızı bir Log Analytics çalışma alanıyla tümleştirmenize karar verirseniz, Hesabınızın bağlantısını doğrudan Azure portal kaldırabilirsiniz. Devam etmeden önce, ilk olarak Güncelleştirme Yönetimi, Değişiklik İzleme ve envanteri ve bunları kullanıyorsanız VM'leri çalışma saatleri dışında başlat/durdur [kaldırmanız](move-account.md#remove-features) gerekir. Bunları kaldırmazsanız, bağlantı kaldırma işlemini tamamlayamazsınız.

Kaldırılan özellikler ile otomasyon Hesabınızın bağlantısını kaldırmak için aşağıdaki adımları izleyebilirsiniz.

> [!NOTE]
> Azure SQL izleme çözümünün önceki sürümleri de dahil olmak üzere bazı özellikler, çalışma alanının bağlantısı kaldırılmadan önce kaldırılması gereken Otomasyon varlıklarını oluşturmuş olabilir.

1. Azure portal Otomasyon hesabınızı açın. Otomasyon hesabı sayfasında, **Ilgili kaynaklar** altında **bağlantılı çalışma alanı** ' nı seçin.

2. Çalışma alanının bağlantısını Kaldır sayfasında, **çalışma alanının bağlantısını kaldır**' ı seçin. Devam etmek isteyip istemediğinizi doğrulayan bir istem alırsınız.

3. Azure Otomasyonu hesabın Log Analytics çalışma alanınızdan bağlantısını kaldırılırken, ilerleme durumunu menüdeki **Bildirimler** bölümünden izleyebilirsiniz.

4. Güncelleştirme Yönetimi kullandıysanız, isteğe bağlı olarak, artık gerekli olmayan aşağıdaki öğeleri kaldırmak isteyebilirsiniz:

    * Zamanlamayı güncelleştir: her birinin, oluşturduğunuz bir güncelleştirme dağıtımıyla eşleşen bir adı vardır.
    * Özelliği için oluşturulan karma çalışan grupları: her birinin şuna benzer bir adı vardır  `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` .

5. VM'leri çalışma saatleri dışında başlat/durdur kullandıysanız, isteğe bağlı olarak artık gerekli olmayan aşağıdaki öğeleri kaldırabilirsiniz:

    * VM runbook zamanlamalarını başlatma ve durdurma
    * VM runbook 'larını başlatma ve durdurma
    * Değişkenler

Alternatif olarak, çalışma alanınızın içindeki Otomasyon hesabınızdan çalışma alanınızın bağlantısını kaldırabilirsiniz.

1. Çalışma alanında **Ilgili kaynaklar** altında **Otomasyon hesabı** ' nı seçin.
2. Otomasyon hesabı sayfasında **Hesap bağlantısını kaldır**' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Güncelleştirme yönetimi genel bakışta](../update-management/overview.md)güncelleştirme yönetimi hakkında bilgi edinin.
* [Değişiklik izleme ve envanterde genel bakış](../change-tracking/overview.md)hakkında bilgi edinin değişiklik izleme.
* [VM'leri çalışma saatleri dışında Başlat/Durdur genel bakışta](../automation-solution-vm-management.md)VM'leri çalışma saatleri dışında Başlat/Durdur hakkında bilgi edinin.