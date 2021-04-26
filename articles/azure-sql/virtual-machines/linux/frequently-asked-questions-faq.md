---
title: Linux üzerinde SQL Server sanal makineler SSS | Microsoft Docs
description: Bu makalede Linux üzerinde SQL Server sanal makineler çalıştırma hakkında sık sorulan sorulara yanıtlar verilmektedir.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad991974df30060e552d21a44d5796cd2ba165e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792557"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-virtual-machines"></a>Linux üzerinde SQL Server sanal makineler için sık sorulan sorular
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/frequently-asked-questions-faq.md)
> * [Linux](frequently-asked-questions-faq.md)

Bu makalede [Linux üzerinde SQL Server sanal makineler](sql-server-on-linux-vm-what-is-iaas-overview.md)çalıştırmaya ilişkin en yaygın soruların yanıtları sunulmaktadır.

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Yansımasını

1. **SQL Server sanal makine galeri görüntüleri kullanılabilir?**

   Azure, hem Linux hem de Windows için tüm sürümlerde SQL Server desteklenen tüm ana sürümleri için sanal makine (VM) görüntülerini tutar. Daha fazla ayrıntı için bkz. [LINUX VM görüntülerinin](sql-server-on-linux-vm-what-is-iaas-overview.md#create) ve [Windows VM görüntülerinin](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)tamamı listesi.

1. **Mevcut SQL Server sanal makine galeri görüntüleri güncelleştirildi mi?**

   Her iki ayda bir sanal makine galerisinde SQL Server görüntüler en son Linux ve Windows güncelleştirmeleriyle güncelleştirilir. Linux görüntüleri için bu en son sistem güncelleştirmelerini içerir. Windows görüntüleri için, önemli SQL Server güvenlik güncelleştirmeleri ve hizmet paketleri de dahil olmak üzere Windows Update önemli olarak işaretlenen tüm güncelleştirmeleri içerir. SQL Server toplu güncelleştirmeler, Linux ve Windows için farklı şekilde işlenir. Linux için SQL Server toplu güncelleştirmeler de yenilemeye dahildir. Ancak şu anda Windows VM 'Ler SQL Server veya Windows Server toplu güncelleştirmeleriyle güncellenmez.

1. **Hangi ilgili SQL Server paketleri de yüklenir?**

   Linux üzerinde SQL Server VM 'lerde varsayılan olarak yüklenen SQL Server paketlerini görmek için bkz. [yüklü paketler](sql-server-on-linux-vm-what-is-iaas-overview.md#packages).

1. **SQL Server sanal makine görüntülerinin Galeriden kaldırılması olabilir mi?**

   Evet. Azure, ana sürüm ve sürüm başına yalnızca bir görüntü tutar. Örneğin, yeni bir SQL Server hizmet paketi yayınlandığında Azure bu hizmet paketinin galerisine yeni bir görüntü ekler. Önceki hizmet paketinin SQL Server görüntüsü hemen Azure portal kaldırılır. Ancak, sonraki üç ay için PowerShell 'den sağlama için hala kullanılabilir. Üç aydan sonra, önceki hizmet paketi görüntüsü artık kullanılamaz. SQL Server bir sürüm yaşam döngüsünün sonuna ulaştığında desteklenmiyorsa bu kaldırma ilkesi de geçerlidir.

## <a name="creation"></a>Oluşturma

1. **SQL Server bir Linux sanal makinesi oluşturmak Nasıl yaparım??**

   En kolay çözüm, SQL Server içeren bir Linux sanal makinesi oluşturmaktır. Azure 'a kaydolma ve portaldan SQL Server VM oluşturma hakkında bir öğretici için, bkz. [Azure portal SQL Server çalıştıran bir Linux sanal makinesi sağlama](sql-vm-create-portal-quickstart.md). Ayrıca, ücretsiz lisanslı bir sürüme (Geliştirici veya Express) sahip bir VM 'ye SQL Server el ile yükleme veya şirket içi bir lisans yeniden kullanma seçeneğiniz de vardır. Kendi lisansınızı getirayarladıysanız [Azure üzerinde Yazılım Güvencesiyle Lisans Taşınabilirliği](https://azure.microsoft.com/pricing/license-mobility)olması gerekir.

1. **Neden bir Azure aboneliğiyle harcama limiti olan bir RHEL veya SLES SQL Server VM sağlayamıyorum?**

   RHEL ve SLES sanal makineleri, harcama limiti olmayan bir abonelik ve abonelikle ilişkili doğrulanmış ödeme yöntemi (genellikle bir kredi kartı) gerektirir. Harcama limitini kaldırmadan bir RHEL veya SLES VM sağlarsanız, aboneliğiniz devre dışı bırakılır ve tüm VM 'Ler/hizmetler durdurulur. Bu durumda çalıştırırsanız, aboneliği yeniden etkinleştirmek için [harcama limitini kaldırın](https://account.windowsazure.com/subscriptions). Kalan kredileriniz geçerli fatura çevrimi için geri yüklenecek, ancak çalışmaya devam edip çalıştırmaya devam etmeyi seçerseniz, bir RHEL veya SLES VM görüntüsü ek maliyeti kredi kartınıza karşı alınacaktır.

## <a name="licensing"></a>Lisanslama

1. **Lisanslı SQL Server kopyamı bir Azure sanal makinesine nasıl yükleyebilirim?**

   İlk olarak, yalnızca bir Linux işletim sistemi sanal makinesi oluşturun. Ardından Linux dağıtım için [SQL Server yükleme adımlarını](/sql/linux/sql-server-linux-setup#platforms) çalıştırın. SQL Server ücretsiz lisanslı sürümlerinden birini yüklemediğiniz takdirde [Azure üzerinde](https://azure.microsoft.com/pricing/license-mobility/)bir SQL Server lisansınız ve Yazılım Güvencesiyle Lisans Taşınabilirliği de sahip olmanız gerekir.

1. **SQL Server için kendi lisansını getir (KLG) Linux sanal makine görüntüleri var mı?**

   Şu anda SQL Server için KLG Linux sanal makine görüntüsü yoktur. Ancak, önceki sorularnda anlatıldığı gibi yalnızca Linux VM 'ye SQL Server el ile yükleyebilirsiniz.

1. **Kullandıkça ödeme galeri görüntülerinden biri kullanılarak oluşturulan sanal makineyi kendi SQL Server lisansımı kullanmak için değiştirebilir miyim?**

   Hayır. Kendi lisansınızı kullanarak, saniye başına ödeme lisanslarından geçiş yapılamaz. Yeni bir Linux sanal makinesi oluşturmanız, SQL Server yükleyip verilerinizi geçirmeniz gerekir. Kendi lisansınızı getirme hakkında daha fazla bilgi için önceki soruya bakın.

## <a name="administration"></a>Yönetim

1. **SQL Server çalıştıran bir Linux sanal makinesini SQL Server Management Studio (SSMS) ile yönetebilir miyim?**

   Evet, ancak SSMS Şu anda yalnızca Windows 'un bir aracıdır. SQL Server çalıştıran Linux VM 'lerle SSMS 'yi kullanmak için bir Windows makinesinden uzaktan bağlanmanız gerekir. Linux 'ta yerel olarak, yeni [MSSQL-conf](/sql/linux/sql-server-linux-configure-mssql-conf) aracı birçok yönetim görevi gerçekleştirebilir. Platformlar arası veritabanı yönetim aracı için bkz. [Azure Data Studio](/sql/azure-data-studio/what-is).

1. **SQL Server'ı SQL Server VM'sinden tamamen kaldırabilir miyim?**

   Evet, ancak [SQL Server Azure VM 'leri Için fiyatlandırma Kılavuzu](../windows/pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json)' nda açıklandığı gibi SQL Server VM ücretlendirilmeye devam edersiniz. Artık SQL Server'a ihtiyacınız kalmadıysa yeni bir sanal makine dağıtabilir ve verilerle uygulamaları yeni sanal makineye geçirebilirsiniz. Ardından SQL Server sanal makinesini kaldırabilirsiniz.

## <a name="updating-and-patching"></a>Güncelleştirme ve düzeltme eki uygulama

1. **Azure VM 'deki SQL Server yeni sürüme/sürümüne yükseltme Nasıl yaparım?.**

   Şu anda bir Azure sanal makinesinde çalışan SQL Server için yerinde yükseltme mevcut değildir. İstediğiniz SQL Server sürümü ile yeni bir Azure sanal makinesi oluşturun ve ardından [standart veri geçirme tekniklerini](/sql/linux/sql-server-linux-migrate-overview) kullanarak veritabanlarınızı yeni sunucuya geçirin.

## <a name="general"></a>Genel

1. **Azure VM 'lerinde desteklenen yüksek kullanılabilirlik çözümleri SQL Server mı?**

   Şu anda değil. Her zaman açık kullanılabilirlik grupları ve yük devretme kümelemesi, Linux 'ta Paceyapıcısı gibi bir kümeleme çözümü gerektirir. SQL Server için desteklenen Linux dağıtımları, bulutta yüksek kullanılabilirlik eklentilerini desteklemez.

## <a name="resources"></a>Kaynaklar

**Linux VM 'leri**:

* [Linux VM 'de SQL Server genel bakış](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Linux VM üzerinde SQL Server sağlama](sql-vm-create-portal-quickstart.md)
* [Linux üzerinde SQL Server belgeleri](/sql/linux/sql-server-linux-overview)

**Windows VM 'leri**:

* [Windows VM 'de SQL Server genel bakış](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 'de SQL Server sağlama](../windows/sql-vm-create-portal-quickstart.md)
* [SSS (Windows)](../windows/frequently-asked-questions-faq.md)