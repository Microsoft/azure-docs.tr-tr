---
title: Microsoft Azure Backup Server 'daki yenilikler
description: Microsoft Azure Backup sunucusu, VM 'Leri, dosyaları ve klasörleri, iş yüklerini ve daha fazlasını korumak için gelişmiş yedekleme olanakları sağlar. V3 Azure Backup Sunucusu nasıl yükleneceğini veya yükselteceğinizi öğrenin.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 564b11ebf14652166733c226485dfab94e69dfbb
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172231"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Microsoft Azure Backup Server 'daki yenilikler

Microsoft Azure Backup Server sürüm 3 (MABS v3) en son yükseltmedir ve kritik hata düzeltmeleri, Windows Server 2019 desteği, SQL 2017 desteği ve diğer özellikleri ve geliştirmeleri içerir. Düzeltilen hataların listesini ve MABS v3 için yükleme yönergelerini görüntülemek için bkz. KB makalesi [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

MABS v3 'de aşağıdaki özellikler bulunur:

## <a name="volume-to-volume-migration"></a>Birim geçişi hacmi

MABS v2 'de Modern Yedekleme Alanı (MB) ile, belirli iş yüklerini depolama özelliklerine göre belirli bir depolama alanına yedeklenecek şekilde yapılandırdığınız Iş yükü algılayan depolama duyurduk. Ancak, yapılandırmadan sonra, belirli veri kaynaklarının yedeklerini iyileştirilmiş kaynak kullanımı için başka bir depolama alanına taşımanız gerektiğini görebilirsiniz. MABS v3, yedeklemelerinizi geçirme ve bunları [üç adımda](https://blogs.technet.microsoft.com/dpm/2017/10/24/storage-migration-with-dpm-2016-mbs/)farklı bir birime depolanacak şekilde yapılandırma olanakları sağlar.

## <a name="prevent-unexpected-data-loss"></a>Beklenmeyen veri kaybını önleme

Kurumlar ' de, MABS bir Yöneticiler ekibi tarafından yönetilir. Depolama üzerinde yedeklemeler için kullanılması gereken yönergeler olsa da, yedek depolama olarak MABS 'e verilen yanlış bir birim kritik verilerin kaybedilmesine neden olabilir. MABS v3 ile, [Bu birimleri bu PowerShell cmdlet 'lerini](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage)kullanarak depolama için kullanılamayan şekilde yapılandırarak bu senaryolara engel olabilirsiniz.

## <a name="custom-size-allocation"></a>Özel boyut ayırma

Modern Yedekleme Alanı (MB), gerektiğinde depolama ölçülü kaynak kullanır. Bunu yapmak için, MABS, koruma için yapılandırıldığında yedeklenen verilerin boyutunu hesaplar. Ancak, bir dosya sunucusu durumunda olduğu gibi birçok dosya ve klasör birlikte yedekleniyorsa, boyut hesaplaması uzun zaman alabilir. MABS v3 ile, MABS 'yi her bir dosyanın boyutunu hesaplamak yerine varsayılan olarak kabul edecek şekilde yapılandırabilirsiniz. bu nedenle zaman tasarrufu sağlayabilirsiniz.

## <a name="optimized-cc-for-rct-vms"></a>RCT VM 'Leri için iyileştirilmiş CC

MABS, sanal makine Kilitlenmelerinde senaryolarda zaman alan tutarlılık denetimleri gereksinimini azaltan RCT (Hyper-V ' d a yerel değişiklik izleme) kullanır. RCT, VSS anlık görüntü tabanlı yedeklemeler tarafından sağlanan değişiklik izlemeden daha iyi dayanıklılık sağlar. MABS v3, herhangi bir tutarlılık denetimi sırasında yalnızca değiştirilen verileri aktararak ağ ve depolama tüketimini iyileştirir.

## <a name="support-to-tls-12"></a>TLS 1,2 desteği

TLS 1,2, Microsoft tarafından en iyi sınıf şifrelemesi ile önerilen iletişimin güvenli bir yoludur. MABS artık MABS ve korunan sunucular arasındaki TLS 1,2 iletişimini, sertifika tabanlı kimlik doğrulaması ve bulut yedeklemeleri için desteklemektedir.

## <a name="vmware-vm-protection-support"></a>VMware VM koruması desteği

VMware VM yedeklemesi artık üretim dağıtımları için desteklenmektedir. MABS v3, VMware VM koruması için aşağıdakileri sunar:

- VCenter ve ESXi 6,5 desteği ve 5,5 ve 6,0 desteğiyle birlikte.
- VMware VM 'lerinin buluta otomatik koruması. Korumalı bir klasöre yeni VMware VM 'Leri eklenirse, bunlar otomatik olarak disk ve buluta korunur.
- VMware alternatif konum kurtarma için kurtarma verimliliği iyileştirmeleri.

## <a name="sql-2017-support"></a>SQL 2017 desteği

MABS v3, MABS veritabanı olarak SQL 2017 ile yüklenebilir. SQL Server 'ı SQL 2016 ' den SQL 2017 ' den yükseltebilir veya onu bir daha önce yeniden yükleyebilirsiniz. Ayrıca, SQL 2017 iş yükünü hem kümelenmiş hem de kümelenmemiş ortamda MABS v3 ile yedekleyebilirsiniz.

## <a name="windows-server-2019-support"></a>Windows Server 2019 desteği

MABS v3, Windows Server 2019 ' ye yüklenebilir. MABS v3 'i WS2019 ile birlikte kullanmak için, MABS v3 'e yüklemeden/yükseltmeden önce işletim sistemini WS2019 'e yükseltebilir ya da işletim sistemi sonrası v3 'yi yükleme/yükseltme işlemini WS2016 üzerinde yükseltmeniz sağlayabilirsiniz.

MABS v3 tam bir sürümdür ve doğrudan Windows Server 2016, Windows Server 2019 üzerine yüklenebilir veya MABS v2 'den yükseltilebilir. Yedekleme sunucusu v3 sürümüne yükseltmeden veya yüklemeden önce, yükleme önkoşulları hakkında bilgi edinin.
[Burada](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package)mabs yükleme/yükseltme adımları hakkında daha fazla bilgi bulabilirsiniz.

> [!NOTE]
>
> MABS, System Center Data Protection Manager ile aynı kod tabanına sahiptir. MABS v3, Data Protection Manager 1807 ile eşdeğerdir.

## <a name="next-steps"></a>Sonraki adımlar

Sunucunuzu nasıl hazırlayacağınızı veya bir iş yükünü korumaya nasıl başlayacağınızı öğrenin:

- [MABS v3 'de bilinen sorunlar](backup-mabs-release-notes-v3.md)
- [Yedekleme sunucusu iş yüklerini hazırlama](backup-azure-microsoft-azure-backup.md)
- [Bir VMware sunucusunu yedeklemek için yedekleme sunucusu kullanma](backup-azure-backup-server-vmware.md)
- [Yedekleme sunucusunu kullanarak yedekleme SQL Server](backup-azure-sql-mabs.md)
- [Yedekleme sunucusu ile Modern Yedekleme Alanı kullanma](backup-mabs-add-storage.md)
