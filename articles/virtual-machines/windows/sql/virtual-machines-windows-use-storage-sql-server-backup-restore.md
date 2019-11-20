---
title: SQL Server Yedekleme ve geri yükleme için Azure Storage 'ı kullanma | Microsoft Docs
description: SQL Server Azure depolama 'ya nasıl yedekleyeceğinizi öğrenin. SQL veritabanlarını Azure depolama 'ya yedeklemenin avantajlarını açıklar.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: cb19dc7262721e672bd3f54b32db9188dad7fee0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101884"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>SQL Server Yedekleme ve geri yükleme için Azure Storage 'ı kullanma
## <a name="overview"></a>Genel Bakış
SQL Server 2012 SP1 CU2 UYGULAMAZSANıZ 'den başlayarak, artık doğrudan Azure Blob depolama hizmetine SQL Server yedeklemeleri yazabilirsiniz. Bu işlevi kullanarak Azure Blob hizmetinden şirket içi SQL Server veritabanı veya bir Azure sanal makinesindeki bir SQL Server veritabanı ile yedekleme ve geri yükleme yapabilirsiniz. Buluta yedekleme, kullanılabilirlik, sınırsız coğrafi olarak çoğaltılan, site dışı depolamanın ve buluta veri geçişi kolaylığının avantajları sunar. Transact-SQL veya SMO kullanarak yedekleme veya GERI yükleme deyimleri verebilirsiniz.

SQL Server 2016 yeni özellikleri tanıtır; [dosya anlık görüntüsü yedeklemesini](https://msdn.microsoft.com/library/mt169363.aspx) , neredeyse anlık yedeklemeler ve inanılmaz hızlı geri yüklemeleri gerçekleştirmek için kullanabilirsiniz.

Bu konuda, SQL yedeklemeleri için Azure Storage 'ı kullanmayı tercih ettiğiniz ve ilgili bileşenleri açıklayan açıklanmaktadır. Bu hizmeti SQL Server yedeklemelerinizle kullanmaya başlamak üzere izlenecek yollara ve ek bilgilere erişmek için makalenin sonunda belirtilen kaynakları kullanabilirsiniz.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>SQL Server yedeklemeleri için Azure Blob hizmetini kullanmanın avantajları
SQL Server yedeklenirken birçok zorluk söz konusu olabilir. Bu güçlükler depolama yönetimi, depolama hatası riski, site dışı depolamaya erişim ve donanım yapılandırması içerir. Bu güçlüklerin birçoğu SQL Server yedeklemeleri için Azure Blob Mağazası hizmeti kullanılarak ele alınır. Aşağıdaki avantajları göz önünde bulundurun:

* **Kullanım kolaylığı**: Yedeklemelerinizi Azure Bloblarında depolamak kullanışlı, esnek ve site dışı bir seçeneğe sahip olabilir. SQL Server yedeklemeleriniz için site dışı depolama oluşturmak, var olan betiklerinizi/işlerinizi, **URL 'ye yedekleme** söz dizimini kullanacak şekilde değiştirmek kadar kolay olabilir. Site dışı depolamanın, genellikle site dışı ve üretim veritabanı konumlarını etkileyebilecek tek bir olağanüstü durum oluşmasını engellemek için üretim veritabanı konumundan yeterince fazla olması gerekir. [Azure bloblarınızı coğrafi olarak çoğaltmayı](../../../storage/common/storage-redundancy.md)seçerek, tüm bölgeyi etkileyebilecek bir olağanüstü durum durumunda ek bir koruma katmanınız vardır.
* **Yedekleme Arşivi**: Azure Blob Storage hizmeti, yedeklemeleri arşivlemek için sık kullanılan bant seçeneğine daha iyi bir alternatif sunar. Bant depolama, medyayı korumak için bir site dışı tesis ve ölçülere fiziksel taşıma gerektirebilir. Yedeklemelerinizi Azure Blob Storage 'da depolamak, anlık, yüksek oranda kullanılabilir ve dayanıklı bir arşivleme seçeneği sağlar.
* **Yönetilen donanım**: Azure hizmetleriyle donanım yönetiminin ek yükü yoktur. Azure hizmetleri donanımı yönetebilir ve donanım arızalarına karşı artıklık ve koruma için coğrafi çoğaltma sağlar.
* **Sınırsız depolama alanı**: Azure bloblarına doğrudan yedeklemeyi etkinleştirerek, neredeyse sınırsız depolamaya erişebilirsiniz. Alternatif olarak, bir Azure sanal makine diskine yedekleme, makine boyutuna bağlı sınırlara sahiptir. Yedeklemeler için bir Azure sanal makinesine iliştirebilmeniz için disk sayısıyla ilgili bir sınır vardır. Bu sınır, çok büyük bir örnek için 16 disk ve daha küçük örnekler için daha azdır.
* **Yedekleme kullanılabilirliği**: Azure Blobları 'nda depolanan yedeklemeler her yerden ve dilediğiniz zaman kullanılabilir ve veritabanı iliştirme/ayırma veya indirme gereksinimi olmadan bir Azure sanal makinesinde çalışan bir şirket içi SQL Server veya başka bir SQL Server geri yüklemeler için kolayca erişilebilir ve VHD 'YI iliştirme.
* **Maliyet**: Yalnızca kullanılan hizmet için ödeme yapın. , Site dışı ve yedekleme Arşivi seçeneği olarak uygun maliyetli olabilir. Daha fazla bilgi için bkz. [Azure Fiyatlandırma Hesaplayıcı](https://go.microsoft.com/fwlink/?LinkId=277060 "Fiyatlandırma Hesaplayıcı")ve [Azure fiyatlandırma makalesi](https://go.microsoft.com/fwlink/?LinkId=277059 "fiyatlandırma makalesi") .
* **Depolama anlık görüntüleri**: Veritabanı dosyaları bir Azure blobuna depolandığında ve SQL Server 2016 kullanıyorsanız, neredeyse anlık yedeklemeler ve inanılmaz hızlı geri yüklemeleri gerçekleştirmek için [dosya anlık görüntüsü yedeklemesini](https://msdn.microsoft.com/library/mt169363.aspx) kullanabilirsiniz.

Daha ayrıntılı bilgi için bkz. [Azure Blob Storage hizmeti Ile yedekleme ve geri yükleme SQL Server](https://go.microsoft.com/fwlink/?LinkId=271617).

Aşağıdaki iki bölüm, gerekli SQL Server bileşenleri dahil olmak üzere Azure Blob depolama hizmetini tanıtmaktadır. Azure Blob depolama hizmetinden yedekleme ve geri yükleme özelliklerini başarıyla kullanmaya yönelik etkileşimleri ve bunların etkileşimini anlamak önemlidir.

## <a name="azure-blob-storage-service-components"></a>Azure Blob depolama hizmeti bileşenleri
Aşağıdaki Azure bileşenleri, Azure Blob depolama hizmetine yedeklenirken kullanılır.

| Bileşen | Açıklama |
| --- | --- |
| **Depolama Hesabı** |Depolama hesabı, tüm depolama hizmetleri için başlangıç noktasıdır. Azure Blob depolama hizmetine erişmek için önce bir Azure depolama hesabı oluşturun. Azure Blob Storage hizmeti hakkında daha fazla bilgi için bkz [. Azure Blob depolama hizmetini kullanma](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Kapsayıcı** |Bir kapsayıcı bir blob kümesi gruplandırması sağlar ve sınırsız sayıda blob saklayabilir. Azure Blob hizmetine bir SQL Server yedeği yazmak için, en azından kök kapsayıcının oluşturulması gerekir. |
| **Blob** |Herhangi bir tür ve boyutun dosyası. Bloblar Şu URL biçimi kullanılarak adreslenebilir: **https://[depolama hesabı]. blob. Core. Windows. net/[Container]/[blob]** . Sayfa Blobları hakkında daha fazla bilgi için bkz. [blok ve sayfa Bloblarını anlama](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>SQL Server bileşenleri
Aşağıdaki SQL Server bileşenleri Azure Blob depolama hizmetine yedeklenirken kullanılır.

| Bileşen | Açıklama |
| --- | --- |
| **URL** |URL, benzersiz bir yedekleme dosyasına bir Tekdüzen Kaynak tanımlayıcısı (URI) belirtir. URL, SQL Server yedekleme dosyasının konumunu ve adını sağlamak için kullanılır. URL yalnızca bir kapsayıcıyı değil, gerçek bir bloba işaret etmelidir. Blob yoksa, oluşturulur. Mevcut bir blob belirtilmişse, > FORMAT seçeneği belirlenmediği takdirde yedekleme başarısız olur. Aşağıda, yedekleme komutunda belirlediğiniz URL 'nin bir örneği verilmiştir: **http [s]://[storageaccount]. blob. Core. Windows. net/[Container]/[FILENAME. bak]** . HTTPS önerilir, ancak gerekli değildir. |
| **Kimlik bilgisi** |Azure Blob depolama hizmetine bağlanmak ve kimlik doğrulaması yapmak için gereken bilgiler bir kimlik bilgisi olarak depolanır.  SQL Server bir Azure Blobuna yedekleme veya geri yükleme için bir SQL Server kimlik bilgisinin oluşturulması gerekir. Daha fazla bilgi için [SQL Server kimlik bilgileri](https://msdn.microsoft.com/library/ms189522.aspx)bölümüne bakın. |

> [!NOTE]
> SQL Server 2016, blok bloblarını destekleyecek şekilde güncelleştirildi. Lütfen öğreticiye bakın [: Daha fazla bilgi için SQL Server 2016 veritabanlarıyla](https://msdn.microsoft.com/library/dn466438.aspx) Microsoft Azure Blob depolama hizmetini kullanma.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
1. Henüz yoksa bir Azure hesabı oluşturun. Azure 'u değerlendiriyorsanız, [ücretsiz denemeyi](https://azure.microsoft.com/free/)göz önünde bulundurun.
2. Ardından, depolama hesabı oluşturma ve geri yükleme gerçekleştirme konusunda size kılavuzluk eden aşağıdaki öğreticilerden birini inceleyin.
   
   * **SQL Server 2014**: [Öğreticide SQL Server 2014 yedekleme ve Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx)'e geri yükleme.
   * **SQL Server 2016**: [Öğreticide SQL Server 2016 veritabanlarıyla Microsoft Azure Blob depolama hizmetini kullanma](https://msdn.microsoft.com/library/dn466438.aspx)
3. [SQL Server Yedekleme ve Microsoft Azure Blob Storage hizmeti Ile geri yükleme](https://msdn.microsoft.com/library/jj919148.aspx)ile başlayarak ek belgeleri gözden geçirin.

Herhangi bir sorununuz varsa, [URL 'Yi En Iyi uygulamalar ve sorun giderme SQL Server](https://msdn.microsoft.com/library/jj919149.aspx)konusunu gözden geçirin.

Yedekleme ve geri yükleme seçeneklerinin diğer SQL Server için bkz. [Azure sanal makinelerinde SQL Server Için Yedekleme ve geri yükleme](virtual-machines-windows-sql-backup-recovery.md).

