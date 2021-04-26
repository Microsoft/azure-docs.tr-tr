---
title: SSS-Azure veritabanı geçiş hizmeti
description: Veritabanı geçişleri gerçekleştirmek için Azure veritabanı geçiş hizmeti 'ni kullanma hakkında sık sorulan sorular.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 29240f371d39ef2d3234e3207cc91768478bf3ff
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748512"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Azure veritabanı geçiş hizmeti 'ni kullanma hakkında SSS

Bu makalede, Azure veritabanı geçiş hizmeti 'ni ilgili yanıtlarla birlikte kullanma hakkında sıkça sorulan sorular listelenmektedir.

## <a name="overview"></a>Genel Bakış

**Ç. Azure veritabanı geçiş hizmeti nedir?**
Azure veritabanı geçiş hizmeti, birden çok veritabanı kaynağından Azure veri platformları arasında kesintisiz geçiş sağlamak için tasarlanan, tam olarak yönetilen bir hizmettir. Hizmet şu anda genel kullanıma sunulmakta ve devam eden geliştirme çabalarına odaklanılmıştır:

* Güvenilirlik ve performans.
* Kaynak-hedef çiftlerinin yinelemeli eklenmesi.
* Sürekli geçişlerde sürekli yatırım.

**Ç. Azure veritabanı geçiş hizmeti şu anda hangi kaynak/hedef çiftleri destekliyor?**
Hizmet şu anda çeşitli kaynak/hedef çiftlerini veya geçiş senaryolarını desteklemektedir. Kullanılabilir her geçiş senaryosunun durum listesinin tamamı için, [Azure veritabanı geçiş hizmeti tarafından desteklenen geçiş senaryolarının](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/dms/resource-scenario-status.md)makalesine bakın.

**Ç. Azure veritabanı geçiş hizmeti hangi SQL Server sürümlerini kaynak olarak destekler?**
SQL Server geçiş yaparken, Azure veritabanı geçiş hizmeti için desteklenen kaynaklar SQL Server 2019 aracılığıyla SQL Server 2005 ' dir.

**S: Azure veritabanı geçiş hizmeti kullanılırken, çevrimdışı ve çevrimiçi geçiş arasındaki fark nedir?**
Azure veritabanı geçiş hizmeti 'ni kullanarak çevrimdışı ve çevrimiçi geçişler gerçekleştirebilirsiniz. *Çevrimdışı* bir geçişle geçiş başladığında uygulama kapalı kalma süresi başlar. *Çevrimiçi* bir geçişle geçiş sonunda kesilme süresiyle sınırlı olur. Çalışmama süresinin kabul edilebilir olup olmadığını belirlemek için bir çevrimdışı geçişini test etmenizi öneririz.

> [!NOTE]
> Çevrimiçi bir geçiş gerçekleştirmek için Azure veritabanı geçiş hizmeti 'nin kullanılması, Premium fiyatlandırma katmanını temel alan bir örnek oluşturulmasını gerektirir. Daha fazla bilgi için bkz. Azure veritabanı geçiş hizmeti [fiyatlandırma](https://azure.microsoft.com/pricing/details/database-migration/) sayfası.

**Ç. Azure veritabanı geçiş hizmeti, veritabanı Geçiş Yardımcısı (DMA) veya SQL Server Geçiş Yardımcısı (SSMA) gibi diğer Microsoft veritabanı geçiş araçlarıyla nasıl karşılaştırılır?**
Azure veritabanı geçiş hizmeti, veritabanı geçişinin ölçeğe Microsoft Azure için tercih edilen yöntemdir. Azure veritabanı geçiş hizmeti 'nin diğer Microsoft veritabanı geçiş araçlarıyla nasıl Karşılaştırıldığı hakkında daha fazla bilgi için ve hizmeti çeşitli senaryolar için kullanmayla ilgili öneriler için, [Microsoft 'un veritabanı geçiş araçları ve Hizmetleri 'Ni farklılaştırmayı](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)açıklayan blog gönderisine bakın.

**Ç. Azure veritabanı geçiş hizmeti Azure geçiş teklifiyle nasıl karşılaştırılır?**
Azure geçişi, şirket içi sanal makinelerin Azure IaaS 'ye geçişine yardımcı olur. Service değerlendirir geçiş uygunluğu ve performans tabanlı boyutlandırma, şirket içi sanal makinelerinizi Azure 'da çalıştırmaya yönelik maliyet tahminleri sağlar. Azure geçişi, şirket içi VM tabanlı iş yüklerinin Azure IaaS VM 'lerine geçiş ve kaydırma geçişleri için yararlıdır. Ancak, Azure veritabanı geçiş hizmeti 'nin aksine Azure geçişi, Azure SQL veritabanı veya Azure SQL yönetilen örneği gibi Azure PaaS ilişkisel veritabanı platformları için özel bir veritabanı geçiş hizmeti teklifi değildir.

**Ç. Veritabanı geçiş hizmeti müşteri verilerini depolayacak mi?**
Hayır. Veritabanı geçiş hizmeti müşteri verilerini depolamaz.

## <a name="setup"></a>Kurulum

**Ç. Azure veritabanı geçiş hizmeti 'ni kullanma önkoşulları nelerdir?**
Azure veritabanı geçiş hizmeti 'nin veritabanı geçişleri gerçekleştirirken sorunsuz çalıştığından emin olmak için birkaç önkoşul vardır. Bazı Önkoşullar, hizmet tarafından desteklenen tüm senaryolarda (kaynak-hedef çiftleri) uygulanır, ancak diğer Önkoşullar belirli bir senaryoya özeldir.

Tüm desteklenen geçiş senaryolarında ortak olan Azure veritabanı geçiş hizmeti önkoşulları şunlar için gereklidir:

* [ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti için bir Microsoft Azure sanal ağ oluşturun.
* Sanal ağ ağ güvenlik grubu kurallarınızın ServiceBus, Storage ve AzureMonitor ServiceTags için 443 bağlantı noktasını engellemediğinden emin olun. Sanal ağ NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md)makalesine bakın.
* Kaynak veritabanınızın önünde bir güvenlik duvarı gereci kullanırken, Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanına erişmesine izin vermek üzere güvenlik duvarı kuralları eklemeniz gerekebilir.

Azure veritabanı geçiş hizmeti 'ni kullanarak belirli geçiş senaryolarına rekabet etmek için gereken tüm önkoşulların bir listesi için, docs.microsoft.com adresindeki Azure veritabanı geçiş hizmeti [belgelerindeki](./dms-overview.md) ilgili öğreticilere bakın.

**Ç. Geçiş için kaynak veritabanıma erişmek üzere kullanılan güvenlik duvarı kuralları için bir izin verilenler listesi oluşturabilmeniz için Azure veritabanı geçiş hizmeti 'nin IP adresini bulun Nasıl yaparım?.**
Azure veritabanı geçiş hizmeti 'nin geçiş için kaynak veritabanınıza erişmesine izin veren güvenlik duvarı kuralları eklemeniz gerekebilir. Hizmetin IP adresi dinamiktir, ancak ExpressRoute kullanıyorsanız, bu adres şirket ağınız tarafından özel olarak atanır. Uygun IP adresini tanımlamanın en kolay yolu, ilişkili ağ arabirimini bulmak için sağlanan Azure veritabanı geçiş hizmeti kaynağınız ile aynı kaynak grubunu görbilmenizdir. Genellikle, ağ arabirimi kaynağının adı NIC öneki ile başlar ve ardından benzersiz bir karakter ve numara sırası gelir; Örneğin, NIC-jj6tnztnmarpsskr82rbndyp. Bu ağ arabirimi kaynağını seçerek kaynağa genel bakış Azure portal sayfasında izin verilenler listesine dahil olması gereken IP adresini görebilirsiniz.

Ayrıca, SQL Server izin verilenler listesinde dinlediği bağlantı noktası kaynağını da eklemeniz gerekebilir. Varsayılan olarak, 1433 bağlantı noktasıdır, ancak kaynak SQL Server diğer bağlantı noktalarını dinlemek için de yapılandırılabilir. Bu durumda, bu bağlantı noktalarını izin verilenler listesine de eklemeniz gerekir. SQL Server bir dinamik yönetim görünümü sorgusu kullanarak dinlediği bağlantı noktasını belirleyebilirsiniz:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

SQL Server hata günlüğünü sorgulayarak SQL Server dinlediği bağlantı noktasını da belirleyebilirsiniz:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**Ç. Nasıl yaparım? bir Microsoft Azure Sanal Ağ mi ayarlanıyor?**
Bir sanal ağ ayarlama sürecinde size yol gösterecek birden çok Microsoft öğreticilerinde, resmi belgeler [Azure sanal ağ](../virtual-network/virtual-networks-overview.md)makalesinde görüntülenir.

## <a name="usage"></a>Kullanım

**Ç. Veritabanı geçişi gerçekleştirmek için Azure veritabanı geçiş hizmeti 'ni kullanmak için gereken adımların Özeti nedir?**
Genellikle basit bir veritabanı geçişi sırasında şunları yapabilirsiniz:

1. Hedef veritabanı (ler) oluşturun.
2. Kaynak veritabanınızı değerlendirin.
    * Hogenler geçişleri için, [DMA](https://www.microsoft.com/download/details.aspx?id=53595)kullanarak var olan veritabanınızı değerlendirin.
    * Heterojen geçişler (rekabet kaynaklarından) için, var olan veritabanınızı [SSMA](/sql/ssma/sql-server-migration-assistant)ile değerlendirin. Ayrıca SSMA 'yı kullanarak veritabanı nesnelerini dönüştürüp şemayı hedef platformunuza geçirebilirsiniz.
3. Azure Veritabanı Geçiş Hizmeti örneği oluşturun.
4. Kaynak veritabanlarını, hedef veritabanlarını ve geçirilecek tabloları belirten bir geçiş projesi oluşturun.
5. Tam yüklemeyi başlatın.
6. Sonraki doğrulamayı seçin.
7. Üretim ortamınızın yeni bulut tabanlı veritabanına el ile geçiş gerçekleştirmesini sağlar.

## <a name="troubleshooting-and-optimization"></a>Sorun giderme ve iyileştirme

**Ç. DMS 'de bir geçiş projesi ayarlıyorum ve kaynak veritabanıma bağlanırken zorluk yaşıyorum. Ne yapmam gerekir?**
Geçiş üzerinde çalışırken kaynak veritabanı sisteminize bağlantı sorunu yaşıyorsanız, DMS örneğinizi ayarladığınız sanal ağın aynı alt ağında bir sanal makine oluşturun. Sanal makinede, MongoDB bağlantılarını test etmek için SQL Server bir bağlantıyı test etmek ya da Robo 3T 'yi indirmek üzere bir UDL dosyası kullanma gibi bir bağlantı testini çalıştırabilmelisiniz. Bağlantı testi başarılı olursa, kaynak veritabanınıza bağlanmada bir sorun olması gerekmez. Bağlantı testi başarılı olmazsa, ağ yöneticinize başvurun.

**Ç. Azure veritabanı geçiş hizmetim neden kullanılamıyor veya durdurulmuş?**
Kullanıcı Azure veritabanı geçiş hizmeti 'ni (DMS) açıkça durduruyor veya hizmet 24 saat boyunca etkin değilse, hizmet durdurulmuş veya otomatik olarak duraklatılmış durumda olur. Her durumda, hizmet kullanılamaz ve durdurulmuş durumda olur.  Etkin geçişleri duraklatmak için hizmeti yeniden başlatın.

**Ç. Azure veritabanı geçiş hizmeti 'nin performansını iyileştirmek için herhangi bir öneri var mı?**
Hizmetini kullanarak veritabanı geçişinizi hızlandırabilmeniz için birkaç şey yapabilirsiniz:

* Hizmetin paralelleştirme ve daha hızlı veri aktarımı için birden çok vCPU 'dan yararlanmasını sağlamak üzere hizmet örneğinizi oluştururken çoklu CPU Genel Amaçlı fiyatlandırma katmanını kullanın.
* Veri geçiş işlemi sırasında Azure SQL veritabanı hedef örneğinizi, daha düşük düzeydeki SKU 'Lar kullanılırken veri aktarımı etkinliklerini etkileyebilecek Azure SQL veritabanı azaltmasını en aza indirmek için Premium katman SKU 'suna geçici olarak ölçeklendirin.

## <a name="next-steps"></a>Sonraki adımlar

Azure veritabanı geçiş hizmeti ve bölgesel kullanılabilirliğine genel bakış için [Azure veritabanı geçiş hizmeti nedir](dms-overview.md)makalesine bakın.
