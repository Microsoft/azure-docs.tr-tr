---
title: Azure veritabanı geçiş hizmeti ile ilgili SSS | Microsoft Docs
description: Veritabanı geçişleri gerçekleştirmek için Azure veritabanı geçiş hizmeti kullanma hakkında sık sorulan sorular hakkında bilgi edinin.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/10/2019
ms.openlocfilehash: 5077539f6f80784f865bd4c1b52e3b4c147107ed
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717992"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Azure veritabanı geçiş hizmeti kullanma hakkında SSS

Bu makalede, Azure veritabanı geçiş hizmeti ilgili yanıtları ile birlikte kullanma hakkında sık sorulan soruların listelenmektedir.

## <a name="overview"></a>Genel Bakış

**SORU. Azure veritabanı geçiş hizmeti nedir?**
Azure veritabanı geçiş hizmeti, birden çok veritabanı kaynağını sorunsuz geçiş için en düşük kapalı kalma süresi ile Azure Data platformlarına sağlamak için tasarlanmış tam olarak yönetilen bir hizmettir. Hizmet şu anda genel kullanılabilirlik, sürmekte olan geliştirme çalışmalarını odaklanan ile verilmiştir:

* Güvenilirlik ve performans.
* Kaynak-hedef çiftlerinin yinelemeli eklenmesi.
* Sorunsuz geçişler sürekli yatırım.

**SORU. Hangi kaynak/hedef çiftleri Azure veritabanı geçiş hizmeti şu anda destekliyor mu?**
Hizmet şu anda çeşitli kaynak/hedef çiftleri veya geçiş senaryolarını destekler. Her bir kullanılabilir geçiş senaryosu durumunu tam listesi için bkz [Azure veritabanı geçiş hizmeti tarafından desteklenen geçiş senaryoları durumunu](https://docs.microsoft.com/azure/dms/resource-scenario-status).

Diğer geçiş senaryoları Önizleme aşamasındadır ve DMS Önizleme sitesi aracılığıyla ADAYLIK gönderme gerektirir. Senaryoları Önizleme aşamasındadır ve bu teklifleri biri katılmayı kaydolmak için tam bir listesi için bkz. [DMS Önizleme site](https://aka.ms/dms-preview/).

**SORU. SQL Server'ın hangi sürümleri Azure veritabanı geçiş hizmeti, bir kaynak olarak destekliyor mu?**
SQL Server'dan geçirirken, desteklenen kaynakları için Azure veritabanı geçiş hizmeti SQL Server 2017 ile SQL Server 2005 ' dir.

**S: Azure veritabanı geçiş hizmeti kullanırken çevrimdışı ve çevrimiçi bir geçiş arasındaki fark nedir?**
Çevrimdışı ve çevrimiçi geçişi gerçekleştirmek için Azure veritabanı geçiş hizmeti kullanabilirsiniz. İle bir *çevrimdışı* geçiş, uygulama kapalı kalma süresi başlar geçiş başlatıldığında. İle bir *çevrimiçi* geçiş, kapalı kalma süresi üzerinden geçiş sonunda kesme zamanı sınırlı olur. Çalışmama süresinin kabul edilebilir olup olmadığını belirlemek için bir çevrimdışı geçişini test etmenizi öneririz.

> [!NOTE]
> Azure veritabanı geçiş hizmeti çevrimiçi bir geçiş gerçekleştirmek için Premium fiyatlandırma katmanını temel alan bir örneği oluşturmanız gerekir. Azure veritabanı geçiş hizmeti daha fazla bilgi için bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/database-migration/) sayfası.

**SORU. Nasıl Azure veritabanı geçiş hizmeti veritabanı geçiş Yardımcısı (DMA) veya SQL Server Geçiş Yardımcısı (SSMA) gibi diğer Microsoft veritabanı geçiş araçları arasındaki fark nedir?**
Azure veritabanı geçiş hizmeti, uygun ölçekte Microsoft azure'a veritabanı geçişi için tercih edilen yöntemdir. Veritabanı Geçiş Araçları nasıl diğer Microsoft Azure veritabanı geçiş hizmeti karşılaştırır üzerinde daha fazla ayrıntı için ve Web günlüğü yayınlama çeşitli senaryolar için hizmetinin kullanılmasıyla ilgili öneriler için bkz. [farklılaştırılması Microsoft'un veritabanı geçişi Araçları ve Hizmetleri](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

**SORU. Nasıl Azure veritabanı geçiş hizmeti, Azure geçişi teklife arasındaki fark nedir?**
Azure geçişi ile şirket içi sanal makinelerin Azure ıaas'ye yardımcı olur. Hizmet, geçiş uygunluğunu ve performans tabanlı boyutlandırmayı değerlendirir ve şirket içi sanal makinelerinizi Azure'da çalıştırmak için maliyet tahminleri sağlar. Azure geçişi, şirket içi sanal makine tabanlı iş yüklerinin Azure ıaas'ye geçirilmesi lift-and-shift ile taşıma için kullanışlıdır. Ancak, Azure veritabanı geçiş hizmeti, Azure geçişi sunan Azure SQL veritabanı veya Azure SQL veritabanı yönetilen örneği gibi Azure PaaS ilişkisel veritabanı platformlar için bir özel veritabanı geçiş hizmeti değildir.

## <a name="setup"></a>Kurulum

**SORU. Azure veritabanı geçiş hizmeti kullanmak için Önkoşullar nelerdir?**
Azure veritabanı geçiş Hizmeti'nin veritabanı geçişlerini gerçekleştirirken sorunsuz çalıştığından emin olmak için gereken birkaç önkoşul vardır. Bazı Önkoşullar, diğer ön koşulları için belirli bir senaryoya benzersiz çalışırken service tarafından desteklenen tüm senaryolarda (kaynak-hedef çiftlerinin) arasında geçerlidir.

Tüm desteklenen geçiş senaryoları arasında ortak olan azure veritabanı geçiş hizmeti Önkoşullar dahil etme gereksinimi:

* Kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlar Azure Resource Manager dağıtım modelini kullanarak sanal ağ için Azure veritabanı geçiş hizmeti oluşturma [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) veya [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Azure sanal ağınız (VNet) emin ağ güvenlik grubu kurallarını yoksa aşağıdaki Engelle iletişim bağlantı noktası 443, 53, 9354, 445, 12000. Azure VNet NSG trafik filtreleme hakkında daha fazla ayrıntı için bkz [ağ güvenlik grupları ile ağ trafiğini filtreleme](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Kaynak veritabanlarınız önünde bir güvenlik duvarı Gereci kullanırken oluşan kaynak veritabanları geçiş için erişmek Azure veritabanı geçiş hizmeti izin vermek için güvenlik duvarı kuralları eklemeniz gerekebilir.

Azure veritabanı geçiş hizmetini kullanarak belirli bir geçiş senaryoları mücadele etmek için gereken tüm önkoşulları listesi için Azure veritabanı geçiş hizmeti ilgili öğreticilerde bkz [belgeleri](https://docs.microsoft.com/azure/dms/dms-overview) docs.microsoft.com'da.

**SORU. Böylece geçiş için kaynak Veritabanım erişmek için kullanılan güvenlik duvarı kuralları için bir izin verilenler listesi oluşturmak nasıl IP adresi için Azure veritabanı geçiş hizmeti bulabilirim?**
Geçiş için kaynak veritabanınıza erişmek Azure veritabanı geçiş hizmeti sağlayan güvenlik duvarı kuralları eklemeniz gerekebilir. Hizmeti için IP adresi dinamik, ancak Express Route kullanıyorsanız, bu adresi şirket ağınıza tarafından özel olarak atanır. Uygun IP adresi tanımlamak için en kolay yolu, ilişkili ağ arabirimi bulmak için aynı kaynak grubunda sağlanan Azure veritabanı geçiş Hizmeti'nin kaynak olarak aramaktır. Genellikle ağ arabirimi kaynağın adını NIC ön Ekle başlayan ve ardından bir benzersiz karakter ve sayı dizisi, örneğin NIC jj6tnztnmarpsskr82rbndyp. Bu ağ arabirimi kaynağı'nı seçerek kaynak genel bakış Azure portal sayfasındaki izin verilenler listesine dahil edilmesi gereken IP adresini görebilirsiniz.

İzin verilenler listesinde SQL sunucusunun dinleme yaptığı bağlantı noktası kaynağını dahil etmek gerekebilir. Varsayılan olarak, ancak diğer bağlantı noktalarında dinlemek için SQL Server yapılandırılabilir kaynak bağlantı noktası 1433 güçlendirin. Bu durumda, bu bağlantı noktalarına izin verilenler listesine de eklemeniz gerekir. SQL Server dinamik yönetim görünümünde sorgu kullanarak dinlediği bağlantı noktasını belirleyebilirsiniz:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

SQL Server için SQL Server hata günlüğünü sorgulayarak dinlediği bağlantı noktasını da belirleyebilirsiniz:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**SORU. Bir Azure sanal ağı nasıl ayarlayabilirim?**
Size bir Azure sanal ağı işleminde yol birden çok Microsoft öğretici sırasında resmi belgelerine makalesinde görünür [Azure sanal ağı](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="usage"></a>Kullanım

**SORU. Veritabanı geçişi gerçekleştirmek için Azure veritabanı geçiş hizmeti kullanmak için gerekli adımları özetini nedir?**
Bir normal, basit bir veritabanı geçiş sırasında:

1. Bir hedef veritabanları oluşturun.
2. Kaynak veritabanlarınız değerlendirin.
    * Kullanarak, var olan veritabanlarının homojen geçişler için değerlendirme [DMA](https://www.microsoft.com/download/details.aspx?id=53595).
    * İle var olan veritabanlarının (yüklemedeki kaynaklardan) heterojen geçişler için değerlendirme [SSMA](https://aka.ms/get-ssma). SSMA veritabanı nesnelerine dönüştürmek ve hedef platformunuzu geçiş şeması için de kullanabilirsiniz.
3. Azure veritabanı geçiş Hizmeti'nin bir örneğini oluşturun.
4. Kaynak veritabanları ve hedef veritabanlarını geçirilecek tabloları belirten bir geçiş projesi oluşturun.
5. Tam Yük başlatın.
6. Sonraki doğrulama seçin.
7. Üretim ortamınızın yeni bulut tabanlı veritabanına el ile bir geçiş gerçekleştirin.

## <a name="troubleshooting-and-optimization"></a>Sorun giderme ve en iyi duruma getirme

**SORU. DMS bir geçiş projesini ayarı ve benim kaynak veritabanı'na bağlanma konusunda sorun yaşıyorum. Ne yapmalıyım?**
Kaynak veritabanı sisteminize geçiş üzerinde çalışırken bağlanırken sorun yaşıyorsanız, DMS örneğiniz ile ayarladığınız VNet içindeki sanal makine oluşturun. Sanal makine, bir SQL Server bağlantısını test etmek için UDL dosyası kullanarak veya MongoDB bağlantıları sınamak için Robo 3T indirme gibi bir bağlantı testi çalıştırmak mümkün olması gerekir. Bağlantı testi başarılı olursa, kaynak veritabanına bağlanırken bir sorun olmamalıdır. Bağlantı testi başarılı olmazsa, ağ yöneticinize başvurun.

**SORU. Neden benim Azure veritabanı geçiş hizmeti kullanılamıyor veya durduruldu olduğunu?**
Kullanıcının Azure veritabanı geçiş hizmeti (DMS) açıkça durdurur veya hizmet 24 saatlik bir süre için etkin değilse, hizmeti durdurulmuş bir olması veya duraklatılmış otomatik. Her durumda, hizmet kullanılamıyor ve durdurulmuş durumda olacaktır.  Etkin geçişlerini devam ettirmek için hizmeti yeniden başlatın.

**SORU. Azure veritabanı geçiş Hizmeti performansını iyileştirmek için herhangi bir önerimiz var mı?**
Hizmetini kullanarak veritabanı geçişinizi hızlandırmak için bazı şeyler yapabilirsiniz:

* Hizmet örneğinizi paralelleştirme ve daha hızlı veri aktarımı için birden çok Vcpu yararlanmak izin verecek şekilde oluşturduğunuzda, birden çok CPU genel amaçlı fiyatlandırma katmanı kullanın.
* Geçici olarak ölçeği artırma, azaltma Azure SQL veritabanı en aza indirmek için veri geçiş işlemi sırasında Premium katmanına SKU, Azure SQL veritabanı hedef örneği düşük düzeyli SKU'ları kullanırken, veri taşıma etkinliklerini etkileyebilir.

## <a name="next-steps"></a>Sonraki adımlar

Bölgesel kullanılabilirlik ve Azure veritabanı geçiş Hizmeti'nin genel bakış için bkz [Azure veritabanı geçiş hizmeti nedir](dms-overview.md).
