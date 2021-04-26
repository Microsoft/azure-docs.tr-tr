---
title: StorSimple 8000 serisi cihazlarından veri geçiş seçenekleri
description: StorSimple 8000 serisinden veri geçirme seçeneklerine genel bakış sağlar.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/25/2020
ms.author: alkohli
ms.openlocfilehash: 4839f8211e678f5fc2fb3572c7eaa545fbee6c6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961201"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>StorSimple 8000 serisinden veri geçirme seçenekleri

> [!IMPORTANT]
> 31 Aralık 2022 ' de, StorSimple 8000 serisi destek (EOS) durumunun sonuna ulaşacaktır. StorSimple 8000 serisi müşterilerinin belgede açıklanan alternatifden birine geçiş yapmanızı öneririz.

StorSimple 8000 serisi, Aralık 2022 ' de [destek sonuna](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) ulaşıyor. StorSimple 8000 serisini çalıştıran müşterilerin diğer Azure birinci taraf karma hizmetlerine yükseltme seçeneği vardır. Bu makalede, verileri geçirmek için kullanılabilen Azure hibrit seçenekleri açıklanmaktadır.

## <a name="migration-options"></a>Geçiş seçenekleri

StorSimple 8000 serisini kullanan müşterilerin Azure veya üçüncü taraf seçenekleri vardır.

### <a name="azure-options"></a>Azure seçenekleri

#### <a name="migrate-to-azure-file-sync"></a>Azure Dosya Eşitleme’ye Geçiş

Bu yepyeni geçiş seçeneği, müşterilerin kuruluşunuzun dosya paylaşımlarını Azure dosyalarında depolamasına olanak sağlar. Bu dosya paylaşımları daha sonra Azure Dosya Eşitleme (AFS) kullanarak şirket içi erişim için merkezi hale getirilmiş. AFS, bir Windows Server konağında dağıtılabilir. Gerçek veri geçişi daha sonra bir konak kopyası olarak veya geçiş aracı kullanılarak gerçekleştirilir.

Azure Dosya Eşitleme verileri geçirme hakkında daha fazla bilgi için, [Azure dosya eşitleme Için StorSimple 8100 ve 8600 geçişine](../storage/files/storage-files-migration-storsimple-8000.md)gidin.

### <a name="third-party-options"></a>Üçüncü taraf seçenekleri

#### <a name="migrate-to-panzura-freedom-nas"></a>Panzura özgürlük 'e geçiş

StorSimple 5000-7000 Serisi ve StorSimple 8000 serisi müşteriler, verilerini Azure 'da tutmak için Panzura özgürlüğü 'e geçiş yapmak için seçim yapabilir. Panzura özgürlük çözümü, veri merkezlerini, ofislerini, ortak ve özel bulutları kapsayan bir NAS çözümü sağlar. Bu çözüm, NFS, SMB ve mobil istemciler için yerel, karma ve bulut içi veri iş akışlarını mümkün bir şekilde sunar.

Bu geçiş, Panzura tarafından desteklenir ve müşteriler [panzura Web sitesinden](https://panzura.com/migrate-storsimple-panzura/)geçiş desteği isteyerek çalışmaya başlayabilir.

#### <a name="migrate-to-nasuni"></a>Nasuni 'ya geçiş

StorSimple ortamınızın tamamını kararlı, güvenli, yüksek performanslı bir dosya hizmetleri platformuna taşımak Nasuni ile çok kolay. Nasuni, Azure 'un ölçeklenebilirlik ve dayanıklılık ile birleştirilirken şirket içi dosya depolamanın güvenlik ve performansını sunar.  Önde gelen bir Azure bağımsız yazılım satıcısı (ISV) olarak, Nasuni, StorSimple verilerinizi birden çok konumdaki dosyalarınıza paylaşmanıza ve bunlarla işbirliği yapmanızı sağlayan modern bir platforma taşımak için gereken tüm araçları getirir.

Bugün kullanmaya başlayın: [Nasuni Web sitesi](https://info.nasuni.com/storsimple8000-webinar).

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>Geçiş-sık sorulan sorular

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>S. StorSimple 8000 Series cihazları hizmet sonuna ulaştığında ne zaman?

A. StorSimple 8000 serisi [destek,](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) Aralık 2022 ' de sona amaın. Destek sonu, Microsoft 'un bu cihazların hem donanım hem de yazılım için Aralık 2022 ' den sonra destek sağlayamayacağı anlamına gelir. Şimdi cihazlarınızdan verileri geçirmek için bir planı formülle açmanızı öneririz.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>S. Azure 'da depoladığınız verilere ne olur?  

A. Verileri daha yeni bir hizmete geçirdikten sonra Azure 'da kullanmaya devam edebilirsiniz.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>S. StorSimple cihazım üzerinde yerel olarak depoladığınız verilere ne olur?

A. Yerel cihazdaki veriler, geçiş belgelerinde açıklandığı gibi yeni hizmete kopyalanabilir.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>S. StorSimple 8000 serisi gerecimi korumak istersem ne olur?

A. Hizmetler çalışmaya devam edebilirken, Microsoft artık donanım ve yazılım desteği sağlayamayacaktır. Geçiş, iş sürekliliği için önerilir.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>S. StorSimple 8000 serisi cihazlarından verileri geçirmek için hangi seçenekler kullanılabilir?

A. Bu senaryoya bağlı olarak, StorSimple 8000 serisi kullanıcıları aşağıdaki geçiş seçeneklerine sahiptir:

* **Azure dosya eşitleme geçir**: Azure yerel biçimine geçmek istediğinizde bu seçeneği kullanın. Dosya paylaşımlarının merkezi yönetimi için Azure Dosya Eşitleme kullanabilirsiniz.

* **Diğer seçenekler**: burada listelenmeyen geçiş seçeneklerini tartışmak için Microsoft desteği ile iletişim sağlayabilirsiniz.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>S. Diğer depolama çözümlerine geçiş destekleniyor mu?

A. Evet. Verilerin ana bilgisayar kopyası kullanılarak diğer depolama çözümlerine geçiş desteklenir.

### <a name="q-is-migration-supported-by-microsoft"></a>S. Geçiş Microsoft tarafından destekleniyor mu?

A. 8000 serisinden geçiş, tam olarak desteklenen bir işlemdir. Aslında, Microsoft, Geçişe başlamadan önce destek 'e ulaşmasını önerir. Geçiş Şu anda yardımlı bir işlemdir. StorSimple 8000 serisi cihazınızdan veri geçirmeyi planlıyorsanız, [StorSimple desteğine başvurun](mailto:storsimp@microsoft.com).

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>S. Azure Dosya Eşitleme geçişe yönelik fiyatlandırma modeli nedir?

A. Azure Dosya Eşitleme kullanırken hizmet için abonelik ücretleri uygulanabilir. Müşterilerin, devam eden depolama maliyetlerini de ödeyecektir. Tahmin için [AFS fiyatlandırması]( https://azure.microsoft.com/pricing/details/storage/files/) bölümüne bakın.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>S. Geçişin tamamlanması ne kadar sürer?

A. Verilerin geçirilmesi için geçen süre, veri miktarına ve yükseltme seçeneğinin seçili olmasına bağlıdır.

## <a name="next-steps"></a>Sonraki adımlar

* [StorSimple 8000 serilerinden verileri Azure Dosya Eşitleme 'e geçirme](../storage/files/storage-files-migration-storsimple-8000.md)