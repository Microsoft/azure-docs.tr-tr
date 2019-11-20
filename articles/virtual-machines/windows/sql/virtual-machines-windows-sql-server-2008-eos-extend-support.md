---
title: SQL Server 2008 & 2008 R2 desteğini uzat
description: SQL Server örneğinizi Azure 'a geçirerek ve örnekleri şirket içinde tutmak için genişletilmiş destek satın alarak SQL Server 2008 ve SQL Server 2008 R2 desteğini genişletin.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6b2546226d31334592034cfe0455fc32644a3900
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132767"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Azure ile SQL Server 2008 ve SQL Server 2008 R2 desteğini genişletme

SQL Server 2008 ve SQL Server 2008 R2, [Destek (EOS) yaşam döngüsünün sonuna](https://www.microsoft.com/sql-server/sql-server-2008)yaklaşılıyor. Birçok müşteri hala her iki sürümü de kullandığından, destek almaya devam etmek için çeşitli seçenekler sunuyoruz. Şirket içi SQL Server örneklerinizi Azure sanal makinelerine (VM) geçirebilir, Azure SQL veritabanına geçirebilir veya şirket içinde kalabilir ve genişletilmiş güvenlik güncelleştirmeleri satın alabilirsiniz.

Yönetilen bir örnekten farklı olarak, bir Azure VM 'ye geçiş yapmak, uygulamalarınızı yeniden ayarlamayı gerektirmez. Şirket içinde kalmaktan farklı olarak, bir Azure VM 'ye geçiş yaparak ücretsiz genişletilmiş güvenlik düzeltme ekleri alacaksınız.

Bu makalenin geri kalanında SQL Server örneğinizi bir Azure VM 'sine geçirmeye yönelik konular sunulmaktadır.

## <a name="provisioning"></a>Sağlama

Azure Marketi 'nde bulunan **Windows Server 2008 R2** görüntüsünde bir kullandıkça öde SQL Server 2008 R2 vardır.

SQL Server 2008 ' de olan müşterilerin, SQL Server 2008 R2 'ye kendi kendine yüklemesi veya yükseltmesi gerekir. Benzer şekilde, Windows Server 2008 müşterilerinin sanal makinesini özel bir VHD 'den dağıtmaları veya Windows Server 2008 R2 sürümüne yükseltmesi gerekir.

Azure Marketi aracılığıyla dağıtılan görüntüler SQL IaaS uzantısı önceden yüklenmiş olarak gelir. SQL IaaS uzantısı, esnek lisanslama ve otomatik düzeltme eki uygulama gereksinimidir. Otomatik olarak yüklenen VM 'Leri dağıtan müşterilerin SQL IaaS uzantısını el ile yüklemesi gerekir. SQL IaaS uzantısı Windows Server 2008 ' de desteklenmez.

> [!NOTE]
> Dikey pencere **oluşturma** ve **yönetme** SQL Server, Azure Portal SQL Server 2008 R2 görüntüsüyle çalışacak olsa da, aşağıdaki özellikler _desteklenmez_: otomatik yedeklemeler, Azure Key Vault tümleştirme, R Hizmetleri ve depolama yapılandırması.

## <a name="licensing"></a>Lisanslama
Kullandıkça Öde SQL Server 2008 R2 dağıtımları [Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/)'e dönüştürülebilir.

Yazılım Güvencesi (SA) tabanlı bir lisansı Kullandıkça Öde 'ye dönüştürmek için, müşterilerin SQL VM [kaynak sağlayıcısına](virtual-machines-windows-sql-register-with-resource-provider.md)kaydolması gerekir. Bu kayıt sonrasında, SQL lisans türü Azure Hibrit Avantajı ve kullandıkça öde arasında çapraz bir şekilde yapılır.

Bir Azure VM 'de kendi kendine yüklenmiş SQL Server 2008 veya SQL Server 2008 R2 örnekleri, SQL VM kaynak sağlayıcısına kaydolabilir ve lisans türlerini Kullandıkça Öde olarak dönüştürebilir.

## <a name="migration"></a>Geçiş
El ile yedekleme/geri yükleme yöntemleriyle EOS SQL Server örneklerini bir Azure VM 'ye geçirebilirsiniz. Bu, Şirket içinden bir Azure VM 'ye en sık kullanılan geçiş yöntemidir.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Toplu geçişler için [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) hizmetini öneririz. Azure Site Recovery, müşteriler şirket içinden Azure VM 'ye SQL Server dahil olmak üzere tüm VM 'leri çoğaltabilirler.

SQL Server, kurtarmayı güvence altına almak için uygulamayla tutarlı Azure Site Recovery anlık görüntüler gerektirir. Azure Site Recovery, en az 1 saatlik aralığa sahip uygulamayla tutarlı anlık görüntüleri destekler. Azure Site Recovery geçişleri olan SQL Server için mümkün olan en düşük kurtarma noktası hedefi (RPO) 1 saattir. Kurtarma süresi hedefi (RTO) 2 saat ve kurtarma zamanı SQL Server.

### <a name="database-migration-service"></a>Veritabanı Geçiş Hizmeti

[Veritabanı geçiş hizmeti](/azure/dms/dms-overview) , SQL Server 2012 sürümüne veya sonrasına yükselterek şirket Içinden BIR Azure VM 'ye geçiş yapıyorsanız müşteriler için bir seçenektir.

## <a name="disaster-recovery"></a>Olağanüstü durum kurtarma

Azure VM 'de EOS SQL Server için olağanüstü durum kurtarma çözümleri şunlardır:

- **Yedeklemeleri SQL Server**: Azure Backup kullanarak eos SQL Server fidye, yanlışlıkla silme ve bozulmalara karşı korumanıza yardımcı olur. Çözüm şu anda EOS SQL Server için önizlemededir ve Windows 2008 R2 SP1 üzerinde çalışan SQL Server 2008 ve 2008 R2 'yi destekler. Daha ayrıntılı bilgi için [Bu makaleye](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support)bakın.
- **Günlük aktarma**: RTO 'ı azaltmak için sürekli geri yüklemeler içeren başka bir bölgede veya Azure bölgesinde bir günlük aktarma çoğaltması oluşturabilirsiniz. Günlük dağıtımını el ile yapılandırmanız gerekir.
- **Azure Site Recovery**: sanal makinenizin Azure Site Recovery çoğaltma aracılığıyla bölgeler ve bölgeler arasında çoğaltılmasını sağlayabilirsiniz. SQL Server, olağanüstü bir durum oluşması durumunda kurtarma sağlamak için uygulamayla tutarlı anlık görüntüler gerektirir. Azure Site Recovery, EOS SQL Server olağanüstü durum kurtarma için en az 1 saatlik RPO ve 2 saat (artı SQL Server kurtarma süresi) sağlar.

## <a name="security-patching"></a>Güvenlik Düzeltme eki uygulama
SQL Server VM 'Ler için genişletilmiş güvenlik güncelleştirmeleri, SQL Server VM SQL VM [kaynak sağlayıcısına](virtual-machines-windows-sql-register-with-resource-provider.md)kaydedildikten sonra Microsoft Update kanalları aracılığıyla dağıtılır. Düzeltme ekleri el ile veya otomatik olarak indirilebilir.

*Otomatik düzeltme eki uygulama* varsayılan olarak etkindir. Otomatik düzeltme eki uygulama Azure’un SQL Server’a ve işletim sistemine otomatik olarak düzeltme eki uygulamasını sağlar. SQL Server IaaS uzantısı yüklüyse, bakım penceresi için haftanın gününü, saatini ve süreyi belirtebilirsiniz. Azure düzeltme eki uygulamayı bu bakım penceresinde gerçekleştirir. Bakım penceresi zamanlaması saat için VM yerel saatini kullanır.  Daha fazla bilgi için bkz. [Azure sanal makinelerinde SQL Server Için otomatik düzeltme eki uygulama](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Sonraki adımlar

SQL Server VM Azure 'a geçirin:

* [Bir SQL Server veritabanını Azure VM’deki SQL Server’a geçirme](virtual-machines-windows-migrate-sql.md)

Azure sanal makineler 'de SQL Server kullanmaya başlayın:

* [Azure portalında SQL Server VM’i oluşturma](quickstart-sql-vm-create-portal.md)

SQL Server VM 'Ler hakkında sık sorulan soruların yanıtlarını alın:

* [Azure sanal makinelerinde SQL Server hakkında SSS](virtual-machines-windows-sql-server-iaas-faq.md)
