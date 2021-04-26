---
title: V3 RTM 'nin yedekleyebilecekleri Azure Backup Sunucusu
description: Bu makalede, v3 RTM korumalarına Azure Backup tüm iş yükleri, veri türleri ve yüklemelerin listelendiği bir koruma matrisi sunulmaktadır.
ms.date: 11/13/2018
ms.topic: conceptual
ms.openlocfilehash: 1ec8240844061b9b250a3cbf92ffcc5f2b3f474b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98986896"
---
# <a name="azure-backup-server-v3-rtm-protection-matrix"></a>Azure Backup Sunucusu v3 RTM koruma matrisi

Aşağıdaki matris, Azure Backup Sunucusu v3 RTM ve önceki sürümleriyle nelerin korunabilecek olduğunu listeler.

## <a name="protection-support-matrix"></a>Koruma destek matrisi

|İş Yükü|Sürüm|Azure Backup Sunucusu</br> installation|Desteklenen Azure Backup Sunucusu|Koruma ve kurtarma|
|------------|-----------|---------------|--------------|--------------|
|İstemci bilgisayarlar (64 bit ve 32 bit)|Windows 10|Fiziksel sunucu<br /><br />Hyper-V sanal makine<br /><br />VMware sanal makinesi|V3, V2|Birim, paylaşma, klasör, dosyalar, yinelenenleri kaldırılmış birimler<br /><br />Korumalı birimler NTFS olmalıdır. FAT ve FAT32 desteklenmez.<br /><br />Birimler en az 1 GB olmalıdır. Azure Backup Sunucusu, veri anlık görüntüsünü almak için Birim Gölge Kopyası Hizmeti (VSS) kullanır ve anlık görüntü yalnızca birim en az 1 GB olduğunda geçerlidir.|
|İstemci bilgisayarlar (64 bit ve 32 bit)|Windows 8.1|Fiziksel sunucu<br /><br />Hyper-V sanal makine|V3, V2|Dosyalar<br /><br />Korumalı birimler NTFS olmalıdır. FAT ve FAT32 desteklenmez.<br /><br />Birimler en az 1 GB olmalıdır. Azure Backup Sunucusu, veri anlık görüntüsünü almak için Birim Gölge Kopyası Hizmeti (VSS) kullanır ve anlık görüntü yalnızca birim en az 1 GB olduğunda geçerlidir.|
|İstemci bilgisayarlar (64 bit ve 32 bit)|Windows 8.1|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)|V3, V2|Birim, paylaşma, klasör, dosyalar, yinelenenleri kaldırılmış birimler<br /><br />Korumalı birimler NTFS olmalıdır. FAT ve FAT32 desteklenmez.<br /><br />Birimler en az 1 GB olmalıdır. Azure Backup Sunucusu, veri anlık görüntüsünü almak için Birim Gölge Kopyası Hizmeti (VSS) kullanır ve anlık görüntü yalnızca birim en az 1 GB olduğunda geçerlidir.|
|İstemci bilgisayarlar (64 bit ve 32 bit)|Windows 8|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi|V3, V2|Birim, paylaşma, klasör, dosyalar, yinelenenleri kaldırılmış birimler<br /><br />Korumalı birimler NTFS olmalıdır. FAT ve FAT32 desteklenmez.<br /><br />Birimler en az 1 GB olmalıdır. Azure Backup Sunucusu, veri anlık görüntüsünü almak için Birim Gölge Kopyası Hizmeti (VSS) kullanır ve anlık görüntü yalnızca birim en az 1 GB olduğunda geçerlidir.|
|İstemci bilgisayarlar (64 bit ve 32 bit)|Windows 8|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)|V3, V2|Birim, paylaşma, klasör, dosyalar, yinelenenleri kaldırılmış birimler<br /><br />Korumalı birimler NTFS olmalıdır. FAT ve FAT32 desteklenmez.<br /><br />Birimler en az 1 GB olmalıdır. Azure Backup Sunucusu, veri anlık görüntüsünü almak için Birim Gölge Kopyası Hizmeti (VSS) kullanır ve anlık görüntü yalnızca birim en az 1 GB olduğunda geçerlidir.|
|İstemci bilgisayarlar (64 bit ve 32 bit)|Windows 7|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi|V3, V2|Birim, paylaşma, klasör, dosyalar, yinelenenleri kaldırılmış birimler<br /><br />Korumalı birimler NTFS olmalıdır. FAT ve FAT32 desteklenmez.<br /><br />Birimler en az 1 GB olmalıdır. Azure Backup Sunucusu, veri anlık görüntüsünü almak için Birim Gölge Kopyası Hizmeti (VSS) kullanır ve anlık görüntü yalnızca birim en az 1 GB olduğunda geçerlidir.|
|İstemci bilgisayarlar (64 bit ve 32 bit)|Windows 7|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)|V3, V2|Birim, paylaşma, klasör, dosyalar, yinelenenleri kaldırılmış birimler<br /><br />Korumalı birimler NTFS olmalıdır. FAT ve FAT32 desteklenmez.<br /><br />Birimler en az 1 GB olmalıdır. Azure Backup Sunucusu, veri anlık görüntüsünü almak için Birim Gölge Kopyası Hizmeti (VSS) kullanır ve anlık görüntü yalnızca birim en az 1 GB olduğunda geçerlidir.|
|Sunucular (64-bit)|Windows Server 2019|Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)<br /><br />VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /><br />Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack|Yüklemesinde <br />Nano server harici|Birim, paylaşım, klasör, dosya, sistem durumu/tam kurtarma), yinelenenlerin kaldırıldığı birimler|
|Sunucular (32 bit ve 64 bit)|Windows Server 2016|Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)<br /><br />VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /><br />Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack|V3, V2<br />Nano server harici|Birim, paylaşım, klasör, dosya, sistem durumu/tam kurtarma), yinelenenlerin kaldırıldığı birimler|
|Sunucular (32 bit ve 64 bit)|Windows Server 2012 R2 - Datacenter ve Standard|Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)<br /> <br /> Azure Stack|V3, V2|Birim, paylaşım, klasör, dosya<br /><br />Windows Server 2012 yinelenenleri kaldırılmış birimleri korumak için Azure Backup Sunucusu en az Windows Server 2012 R2 üzerinde çalışıyor olmalıdır.|
|Sunucular (32 bit ve 64 bit)|Windows Server 2012 R2 - Datacenter ve Standard|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Birim, paylaşım, klasör, dosya, sistem durumu/tam kurtarma)<br /><br />Windows Server 2012 yinelenenleri kaldırılmış birimleri korumak için Azure Backup Sunucusu Windows Server 2012 veya 2012 R2 üzerinde çalışıyor olmalıdır.|
|Sunucular (32 bit ve 64 bit)|Windows Server 2012/2012 SP1 – Datacenter ve Standard|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack|V3, V2|Birim, paylaşım, klasör, dosya, sistem durumu/tam kurtarma<br /><br />Windows Server 2012 yinelenenleri kaldırılmış birimleri korumak için Azure Backup Sunucusu en az Windows Server 2012 R2 üzerinde çalışıyor olmalıdır.|
|Sunucular (32 bit ve 64 bit)|Windows Server 2012/2012 SP1 – Datacenter ve Standard|Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)<br /> <br /> Azure Stack|V3, V2|Birim, paylaşım, klasör, dosya<br /><br />Windows Server 2012 yinelenenleri kaldırılmış birimleri korumak için Azure Backup Sunucusu en az Windows Server 2012 R2 üzerinde çalışıyor olmalıdır.|
|Sunucular (32 bit ve 64 bit)|Windows Server 2012/2012 SP1 – Datacenter ve Standard|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Birim, paylaşım, klasör, dosya, sistem durumu/tam kurtarma<br /><br />Windows Server 2012 yinelenenleri kaldırılmış birimleri korumak için Azure Backup Sunucusu en az Windows Server 2012 R2 üzerinde çalışıyor olmalıdır.|
|Sunucular (32 bit ve 64 bit)|Windows Server 2008 R2 SP1 - Standard ve Enterprise|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack|V3, V2<br />SP1 çalıştırıyor ve [Windows Management Framework 'ü](https://www.microsoft.com/download/details.aspx?id=54616) yüklemeniz gerekiyor|Birim, paylaşım, klasör, dosya, sistem durumu/tam kurtarma|
|Sunucular (32 bit ve 64 bit)|Windows Server 2008 R2 SP1 - Standard ve Enterprise|Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)<br /> <br /> Azure Stack|V3, V2<br />SP1 çalıştırıyor ve [Windows Management Framework 'ü](https://www.microsoft.com/download/details.aspx?id=54616) yüklemeniz gerekiyor|Birim, paylaşım, klasör, dosya|
|Sunucular (32 bit ve 64 bit)|Windows Server 2008 R2 SP1 - Standard ve Enterprise|VMware 'de Windows sanal makinesi (VMWare 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2<br />SP1 çalıştırıyor ve [Windows Management Framework 'ü](https://www.microsoft.com/download/details.aspx?id=54616) yüklemeniz gerekiyor|Birim, paylaşım, klasör, dosya, sistem durumu/tam kurtarma|
|Sunucular (32 bit ve 64 bit)|Windows Server 2008 SP2|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack|Desteklenmez|Birim, paylaşım, klasör, dosya, sistem durumu/tam kurtarma|
|Sunucular (32 bit ve 64 bit)|Windows Server 2008 SP2|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Birim, paylaşım, klasör, dosya, sistem durumu/tam kurtarma|
|Sunucular (32 bit ve 64 bit)|Windows Storage Server 2008|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack|V3, V2|Birim, paylaşım, klasör, dosya, sistem durumu/tam kurtarma|
|SQL Server|SQL Server 2019|Fiziksel sunucu <br /><br /> Şirket içi Hyper-V sanal makinesi <br /> <br /> Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken) <br /><br /> VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|Yüklemesinde|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2017|Fiziksel sunucu <br /><br /> Şirket içi Hyper-V sanal makinesi <br /> <br /> Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken) <br /><br /> VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|Yüklemesinde|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2016 SP2|Fiziksel sunucu <br /><br /> Şirket içi Hyper-V sanal makinesi <br /> <br /> Azure sanal makine <br /><br /> VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2016 SP1|Fiziksel sunucu <br /><br /> Şirket içi Hyper-V sanal makinesi <br /> <br /> Azure sanal makine <br /><br /> VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2016|Fiziksel sunucu <br /><br /> Şirket içi Hyper-V sanal makinesi <br /> <br /> Azure sanal makine <br /><br /> VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2014|Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2014|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2012 SP2|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2012 SP2|Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2012 SP2|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2012, SQL Server 2012 SP1|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2012, SQL Server 2012 SP1|Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2012, SQL Server 2012 SP1|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2008 R2|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2008 R2|Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2008 R2|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2008|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2008|Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|SQL Server|SQL Server 2008|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Tüm dağıtım senaryoları: Veritabanı|
|Exchange|Exchange 2016|Fiziksel sunucu<br/><br/> Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack<br /> <br />Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)|V3, V2|Koruma (tüm dağıtım senaryolarında): Tek başına Exchange sunucusu, veritabanı kullanılabilirlik grubu (DAG) altındaki bir veritabanı<br /><br />Kurtarma (tüm dağıtım senaryolarında): Posta kutusu, DAG altındaki posta kutusu veritabanları<br/><br/> ReFS üzerinde Exchange yedeklemesi desteklenmiyor |
|Exchange|Exchange 2016|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Koruma (tüm dağıtım senaryolarında): Tek başına Exchange sunucusu, veritabanı kullanılabilirlik grubu (DAG) altındaki bir veritabanı<br /><br />Kurtarma (tüm dağıtım senaryolarında): Posta kutusu, DAG altındaki posta kutusu veritabanları<br/><br/> ReFS üzerinde Exchange yedeklemesi desteklenmiyor |
|Exchange|Exchange 2013|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack|V3, V2|Koruma (tüm dağıtım senaryolarında): Tek başına Exchange sunucusu, veritabanı kullanılabilirlik grubu (DAG) altındaki bir veritabanı<br /><br />Kurtarma (tüm dağıtım senaryolarında): Posta kutusu, DAG altındaki posta kutusu veritabanları<br/><br/> ReFS üzerinde Exchange yedeklemesi desteklenmiyor |
|Exchange|Exchange 2013|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Koruma (tüm dağıtım senaryolarında): Tek başına Exchange sunucusu, veritabanı kullanılabilirlik grubu (DAG) altındaki bir veritabanı<br /><br />Kurtarma (tüm dağıtım senaryolarında): Posta kutusu, DAG altındaki posta kutusu veritabanları<br/><br/> ReFS üzerinde Exchange yedeklemesi desteklenmiyor |
|Exchange|Exchange 2010|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack|V3, V2|Koruma (tüm dağıtım senaryolarında): Tek başına Exchange sunucusu, veritabanı kullanılabilirlik grubu (DAG) altındaki bir veritabanı<br /><br />Kurtarma (tüm dağıtım senaryolarında): Posta kutusu, DAG altındaki posta kutusu veritabanları<br/><br/> ReFS üzerinde Exchange yedeklemesi desteklenmiyor |
|Exchange|Exchange 2010|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Koruma (tüm dağıtım senaryolarında): Tek başına Exchange sunucusu, veritabanı kullanılabilirlik grubu (DAG) altındaki bir veritabanı<br /><br />Kurtarma (tüm dağıtım senaryolarında): Posta kutusu, DAG altındaki posta kutusu veritabanları<br/><br/> ReFS üzerinde Exchange yedeklemesi desteklenmiyor |
|SharePoint|SharePoint 2016|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /><br />Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)<br /><br />VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Koruma (tüm dağıtım senaryolarında): Grup, ön uç web sunucusu içeriği<br /><br />Kurtarma (tüm dağıtım senaryolarında): Grup, veritabanı, web uygulaması, dosya veya liste öğesi, SharePoint arama, ön uç web sunucusu<br /><br />İçerik veritabanları için SQL Server 2012 AlwaysOn özelliği kullanan bir SharePoint grubunu korumanın desteklenmediğini unutmayın.|
|SharePoint|SharePoint 2013|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack|V3, V2|Koruma (tüm dağıtım senaryolarında): Grup, ön uç web sunucusu içeriği<br /><br />Kurtarma (tüm dağıtım senaryolarında): Grup, veritabanı, web uygulaması, dosya veya liste öğesi, SharePoint arama, ön uç web sunucusu<br /><br />İçerik veritabanları için SQL Server 2012 AlwaysOn özelliği kullanan bir SharePoint grubunu korumanın desteklenmediğini unutmayın.|
|SharePoint|SharePoint 2013|Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)- <br /> <br /> Azure Stack|V3, V2|Koruma (tüm dağıtım senaryolarında): Grup, SharePoint arama, ön uç web sunucu içeriği<br /><br />Kurtarma (tüm dağıtım senaryolarında): Grup, veritabanı, web uygulaması, dosya veya liste öğesi, SharePoint arama, ön uç web sunucusu<br /><br />İçerik veritabanları için SQL Server 2012 AlwaysOn özelliği kullanan bir SharePoint grubunu korumanın desteklenmediğini unutmayın.|
|SharePoint|SharePoint 2013|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Koruma (tüm dağıtım senaryolarında): Grup, SharePoint arama, ön uç web sunucu içeriği<br /><br />Kurtarma (tüm dağıtım senaryolarında): Grup, veritabanı, web uygulaması, dosya veya liste öğesi, SharePoint arama, ön uç web sunucusu<br /><br />İçerik veritabanları için SQL Server 2012 AlwaysOn özelliği kullanan bir SharePoint grubunu korumanın desteklenmediğini unutmayın.|
|SharePoint|SharePoint 2010|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi<br /> <br /> Azure Stack|V3, V2|Koruma (tüm dağıtım senaryolarında): Grup, SharePoint arama, ön uç web sunucu içeriği<br /><br />Kurtarma (tüm dağıtım senaryolarında): Grup, veritabanı, web uygulaması, dosya veya liste öğesi, SharePoint arama, ön uç web sunucusu|
|SharePoint|SharePoint 2010|Azure sanal makinesi (iş yükü Azure sanal makinesi olarak çalışırken)<br /> <br /> Azure Stack|V3, V2|Koruma (tüm dağıtım senaryolarında): Grup, SharePoint arama, ön uç web sunucu içeriği<br /><br />Kurtarma (tüm dağıtım senaryolarında): Grup, veritabanı, web uygulaması, dosya veya liste öğesi, SharePoint arama, ön uç web sunucusu|
|SharePoint|SharePoint 2010|VMware 'de Windows sanal makinesi (VMware 'deki Windows sanal makinesinde çalışan iş yüklerini korur)<br /> <br /> Azure Stack|V3, V2|Koruma (tüm dağıtım senaryolarında): Grup, SharePoint arama, ön uç web sunucu içeriği<br /><br />Kurtarma (tüm dağıtım senaryolarında): Grup, veritabanı, web uygulaması, dosya veya liste öğesi, SharePoint arama, ön uç web sunucusu|
|Hyper-V konağı-MABS koruma Aracısı, Hyper-V konak sunucusu, küme veya VM üzerinde|Windows Server 2019|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi|Yüklemesinde|Koruma: Hyper-V bilgisayarları, küme paylaşılan birimleri (CSV)<br /><br />Kurtarma: Sanal makine, dosya ve klasörleri öğe düzeyinde kurtarma, birimler, sanal sabit sürücüler|
|Hyper-V konağı-MABS koruma Aracısı, Hyper-V konak sunucusu, küme veya VM üzerinde|Windows Server 2016|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi|V3, V2|Koruma: Hyper-V bilgisayarları, küme paylaşılan birimleri (CSV)<br /><br />Kurtarma: Sanal makine, dosya ve klasörleri öğe düzeyinde kurtarma, birimler, sanal sabit sürücüler|
|Hyper-V konağı-MABS koruma Aracısı, Hyper-V konak sunucusu, küme veya VM üzerinde|Windows Server 2012 R2 - Datacenter ve Standard|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi|V3, V2|Koruma: Hyper-V bilgisayarları, küme paylaşılan birimleri (CSV)<br /><br />Kurtarma: Sanal makine, dosya ve klasörleri öğe düzeyinde kurtarma, birimler, sanal sabit sürücüler|
|Hyper-V konağı-MABS koruma Aracısı, Hyper-V konak sunucusu, küme veya VM üzerinde|Windows Server 2012 - Datacenter ve Standard|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi|V3, V2|Koruma: Hyper-V bilgisayarları, küme paylaşılan birimleri (CSV)<br /><br />Kurtarma: Sanal makine, dosya ve klasörleri öğe düzeyinde kurtarma, birimler, sanal sabit sürücüler|
|Hyper-V konağı-MABS koruma Aracısı, Hyper-V konak sunucusu, küme veya VM üzerinde|Windows Server 2008 R2 SP1 - Enterprise ve Standard|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi|V3, V2|Koruma: Hyper-V bilgisayarları, küme paylaşılan birimleri (CSV)<br /><br />Kurtarma: Sanal makine, dosya ve klasörleri öğe düzeyinde kurtarma, birimler, sanal sabit sürücüler|
|Hyper-V konağı-MABS koruma Aracısı, Hyper-V konak sunucusu, küme veya VM üzerinde|Windows Server 2008 SP2|Fiziksel sunucu<br /><br />Şirket içi Hyper-V sanal makinesi|Desteklenmez|Koruma: Hyper-V bilgisayarları, küme paylaşılan birimleri (CSV)<br /><br />Kurtarma: Sanal makine, dosya ve klasörleri öğe düzeyinde kurtarma, birimler, sanal sabit sürücüler|
|VMware Sanal Makineleri|VMware vCenter/vSphere ESX/ESXi lisanslı sürüm 5.5/6.0/6.5 |Fiziksel sunucu, <br/>Şirket içi Hyper-V VM 'si, <br/> VMware 'de Windows VM|V3, V2|Küme Paylaşılan birimlerinde (CSV), NFS ve SAN depolamada VMware VM 'Leri<br /> Dosya ve klasörlerin öğe düzeyinde kurtarılması yalnızca Windows VM 'Leri için kullanılabilir, VMware vApps desteklenmez.|
|VMware Sanal Makineleri|[VMware vSphere lisanslı sürüm 6,7](backup-azure-backup-server-vmware.md#vmware-vsphere-67) |Fiziksel sunucu, <br/>Şirket içi Hyper-V VM 'si, <br/> VMware 'de Windows VM|Yüklemesinde|Küme Paylaşılan birimlerinde (CSV), NFS ve SAN depolamada VMware VM 'Leri<br /> Dosya ve klasörlerin öğe düzeyinde kurtarılması yalnızca Windows VM 'Leri için kullanılabilir, VMware vApps desteklenmez.|
|Linux|[Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) veya [VMware](backup-azure-backup-server-vmware.md) konuğu olarak çalışan Linux|Fiziksel sunucu, <br/>Şirket içi Hyper-V VM 'si, <br/> VMware 'de Windows VM|V3, V2|Hyper-V Windows Server 2012 R2 veya Windows Server 2016 üzerinde çalışıyor olmalıdır. Koruma: Tüm sanal makine<br /><br />Kurtarma: Tüm sanal makine <br/><br/> Yalnızca dosya tutarlı anlık görüntüler desteklenir. <br/><br/> Desteklenen Linux dağıtımları ve sürümlerinin tüm listesi için bkz. [Azure tarafından onaylanan dağıtımların Linux](../virtual-machines/linux/endorsed-distros.md)makalesi.|

## <a name="azure-expressroute-support"></a>Azure ExpressRoute desteği

Azure ExpressRoute üzerinden verilerinizi, genel eşleme (eski devreler için kullanılabilir) ve Microsoft eşlemesi ile birlikte yedekleyebilirsiniz. Özel eşleme üzerinde yedekleme desteklenmez.

Ortak eşleme ile: aşağıdaki etki alanlarına/adreslere erişim sağlayın:

* URL’ler
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP adresleri
  * 20.190.128.0/18
  * 40.126.0.0/18

Microsoft eşlemesi ile aşağıdaki hizmetleri/bölgeleri ve ilgili topluluk değerlerini seçin:

* Azure Active Directory (12076:5060)
* Microsoft Azure bölgesi (Kurtarma Hizmetleri kasanızın konumuna göre)
* Azure depolama (Kurtarma Hizmetleri kasanızın konumuna göre)

Daha fazla ayrıntı için bkz. [ExpressRoute yönlendirme gereksinimleri](../expressroute/expressroute-routing.md).

>[!NOTE]
>Ortak eşleme, yeni devreler için kullanım dışıdır.

## <a name="cluster-support"></a>Küme desteği

Azure Backup Sunucusu, aşağıdaki kümelenmiş uygulamalardaki verileri koruyabilir:

* Dosya sunucuları

* SQL Server

* Hyper-V-ölçekli MABS koruma Aracısı kullanarak bir Hyper-V kümesini koruyorduysanız, korunan Hyper-V iş yükleri için ikincil koruma ekleyemezsiniz.

    Windows Server 2008 R2 üzerinde Hyper-V çalıştırırsanız, BB [975354](https://support.microsoft.com/kb/975354)' de açıklanan güncelleştirmeyi yüklediğinizden emin olun.
    Windows Server 2008 R2 'de Hyper-V ' y i bir küme yapılandırmasında çalıştırırsanız, SP2 ve KB [971394](https://support.microsoft.com/kb/971394)' yi yüklediğinizden emin olun.

* Exchange Server-Azure Backup Sunucusu, desteklenen Exchange Server sürümleri (küme sürekli çoğaltma) için paylaşılmayan disk kümelerini koruyabilir ve ayrıca yerel sürekli çoğaltma için yapılandırılmış Exchange Server 'ı da koruyabilir.

* SQL Server-Azure Backup Sunucusu, Küme Paylaşılan birimlerinde (CSV) barındırılan SQL Server veritabanlarının yedeklenmesini desteklemez.

Azure Backup Sunucusu, MABS sunucusuyla aynı etki alanında ve alt veya güvenilen bir etki alanında bulunan küme iş yüklerini koruyabilir. Güvenilmeyen etki alanları veya çalışma gruplarındaki veri kaynaklarını korumak istiyorsanız, tek bir sunucu için NTLM veya sertifika kimlik doğrulaması ya da yalnızca bir küme için sertifika kimlik doğrulaması kullanın.
