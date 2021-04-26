---
title: Sanal çekirdek satın alma modeline genel bakış
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Sanal çekirdek satın alma modeli, işlem ve depolama kaynaklarını bağımsız olarak ölçeklendirmenize, şirket içi performansı eşleşmenize ve Azure SQL veritabanı ve Azure SQL yönetilen örneği için fiyata iyileştirmenize olanak tanır.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/15/2021
ms.openlocfilehash: 3bd617f052d52339ae35e5a088c6ee85b797fb48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779192"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Sanal çekirdek modeline genel bakış-Azure SQL veritabanı ve Azure SQL yönetilen örneği 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı ve Azure SQL yönetilen örneği tarafından kullanılan sanal çekirdek (vCore) satın alma modeli çeşitli avantajlar sunar:

- Daha yüksek işlem, bellek, g/ç ve depolama sınırları.
- İş yükünün işlem ve bellek gereksinimlerini daha iyi eşleştirmek için donanım oluşturma üzerinde denetim.
- [Azure hibrit avantajı (AHB)](../azure-hybrid-benefit.md) ve [ayrılmış örnek (RI)](reserved-capacity-overview.md)için fiyatlandırma iskontoları.
- Şirket içi dağıtımlardan geçiş planlamayı kolaylaştıran, işlem gücünü destekleyen donanım ayrıntılarında daha fazla saydamlık.

## <a name="service-tiers"></a>Hizmet katmanları

Sanal çekirdek modelindeki hizmet katmanı seçenekleri Genel Amaçlı, İş Açısından Kritik ve Hyperscale içerir. Hizmet katmanı genellikle depolama mimarisini, boşluk ve g/ç sınırlarını ve kullanılabilirlik ve olağanüstü durum kurtarma ile ilgili iş sürekliliği seçeneklerini tanımlar.

|-|**Genel Amaçlı**|**İş Açısından Kritik**|**Hiper Ölçek**|
|---|---|---|---|
|En iyi kullanım alanı:|Birçok iş yükü. Bütçe odaklı, dengeli ve ölçeklenebilir işlem ile depolama seçenekleri sunar. |, Birkaç yalıtılmış çoğaltma kullanarak ve en yüksek g/ç performansı sunan iş uygulamalarına en yüksek esnekliği sağlar.|Yüksek düzeyde ölçeklenebilir depolama ve okuma ölçeği gereksinimlerine sahip iş yüklerinin çoğu.  , Birden fazla yalıtılmış veritabanı çoğaltmasının yapılandırılmasına izin vererek daha yüksek esnekliği hatalara olanak sağlar. |
|Depolama|Uzak depolamayı kullanır.<br/>**SQL veritabanı sağlanan işlem**:<br/>5 GB – 4 TB<br/>**Sunucusuz işlem**:<br/>5 GB-3 TB<br/>**SQL yönetilen örneği**: 32 GB-8 TB |Yerel SSD depolama kullanır.<br/>**SQL veritabanı sağlanan işlem**:<br/>5 GB – 4 TB<br/>**SQL yönetilen örneği**:<br/>32 GB-4 TB |Gerektiğinde depolamanın esnek otomatik büyümesi. 100 TB 'a kadar depolamayı destekler. Yerel ara havuz önbelleği ve yerel veri depolaması için yerel SSD depolama kullanır. Son uzun süreli veri deposu olarak Azure uzak depolama kullanır. |
|IOPS ve aktarım hızı (yaklaşık)|**SQL veritabanı**: [tek veritabanları](resource-limits-vcore-single-databases.md) ve [elastik havuzlar](resource-limits-vcore-elastic-pools.md)için kaynak sınırlarına bakın.<br/>**SQL yönetilen örneği**: bkz. [Azure SQL yönetilen örnek kaynak sınırlarına genel bakış](../managed-instance/resource-limits.md#service-tier-characteristics).|[Tek veritabanları](resource-limits-vcore-single-databases.md) ve [elastik havuzlar](resource-limits-vcore-elastic-pools.md)için kaynak sınırlarına bakın.|Hiper ölçek, birden çok düzeyde önbelleğe alma özelliği olan çok katmanlı bir mimaridir. Etkin ıOPS ve aktarım hızı iş yüküne bağlıdır.|
|Kullanılabilirlik|1 çoğaltma, okuma ölçeğinde çoğaltmalar yok|3 çoğaltma, 1 [okuma ölçeği çoğaltma](read-scale-out.md),<br/>bölge yedekli yüksek kullanılabilirlik (HA)|1 okuma-yazma çoğaltması, artı 0-4 [okuma ölçekli çoğaltmalar](read-scale-out.md)|
|Yedeklemeler|[Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS)](../../storage/common/geo-redundant-design.md), 1-35 gün (varsayılan olarak 7 gün)|[RA-GRS](../..//storage/common/geo-redundant-design.md), 1-35 gün (varsayılan olarak 7 gün)|Azure uzak depolama 'da anlık görüntü tabanlı yedeklemeler. Geri yükleme bu anlık görüntüleri hızlı kurtarma için kullanır. Yedeklemeler anında gerçekleşir ve işlem g/ç performansını etkilemez. Geri yükleme işlemleri hızlıdır ve veri boyutu (saatler veya günler yerine dakikalar içinde).|
|Bellek içi|Desteklenmez|Desteklenir|Desteklenmez|
|||


### <a name="choosing-a-service-tier"></a>Hizmet katmanı seçme

Belirli bir iş yükünüz için bir hizmet katmanı seçme hakkında bilgi için aşağıdaki makalelere bakın:

- [Genel Amaçlı hizmet katmanını seçme](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [İş Açısından Kritik hizmet katmanını seçme](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Hiper ölçek hizmet katmanını seçme](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>İşlem katmanları

Sanal çekirdek modelindeki işlem katmanı seçenekleri sağlanan ve sunucusuz işlem katmanlarını içerir.


### <a name="provisioned-compute"></a>Sağlanan işlem

Sağlanan işlem katmanı, iş yükü etkinliğinden bağımsız olarak sürekli olarak sağlanan belirli bir işlem kaynakları ve bir saatlik sabit fiyata sağlanan işlem miktarına göre faturalandırılır.


### <a name="serverless-compute"></a>Sunucusuz işlem

[Sunucusuz işlem katmanı](serverless-tier-overview.md) , işlem kaynaklarını iş yükü etkinliğine göre otomatik olarak ölçeklendirir ve saniye başına kullanılan işlem miktarına göre faturalandırılır.



## <a name="hardware-generations"></a>Donanım nesilleri

VCore modelindeki donanım oluşturma seçenekleri gen 4/5, M serisi, Fsv2-Series ve DC-Series ' i içerir. Donanım oluşturma genellikle işlem ve bellek sınırlarını ve iş yükünün performansını etkileyen diğer özellikleri tanımlar.

### <a name="gen4gen5"></a>4. nesil/5. nesil

- 4. nesil/5. nesil donanımı, dengeli işlem ve bellek kaynakları sağlar ve Fsv2 serisi veya d serisi tarafından sağlanan daha yüksek bellek, daha yüksek sanal çekirdek veya daha hızlı tek bir sanal çekirdek gereksinimlerine sahip olmayan veritabanı iş yükleri için uygundur.

4. nesil/5. nesil kullanılabildiği bölgelerde, bkz. [4. nesil/5. nesil kullanılabilirliği](#gen4gen5-1).

### <a name="fsv2-series"></a>Fsv2 serisi

- Fsv2-Series, en düşük CPU gecikme süresi ve yüksek hızda yoğun iş yükleri sağlayan, işlem için iyileştirilmiş bir donanım seçeneğidir.
- Fsv2 serisi, iş yüküne bağlı olarak, 5. nesil 'den vCore başına daha fazla CPU performansı sunabilir ve 72 vCore boyutu 5. nesil üzerindeki 80 sanal çekirdekten daha az maliyet sağlamak için daha fazla CPU performansı sağlayabilir. 
- Fsv2, Diğer donanımlardan sanal çekirdek başına daha az bellek ve tempdb sağlar, bu sınırlara duyarlı iş yükleri bunun yerine 5. nesil veya d serisini düşünmek isteyebilir.  

Fsv2-Series yalnızca Genel Amaçlı katmanında desteklenir. Fsv2-Series 'in kullanılabildiği bölgeler için bkz. [Fsv2 serisi kullanılabilirliği](#fsv2-series-1).

### <a name="m-series"></a>M serisi

- D serisi, 5. nesil tarafından sağlanenden daha fazla bellek ve daha fazla işlem sınırı gerektiren iş yükleri için bellek için iyileştirilmiş bir donanım seçeneğidir.
- A serisi, vCore başına 29 GB ve 128 sanal çekirdek sağlar. Bu, 5. nesil ile 8X arasındaki bellek sınırını neredeyse 4 TB 'a yükseltir.

A serisi yalnızca İş Açısından Kritik katmanında desteklenir ve bölge yedekliliği desteklemez.  D serisi kullanılabilir olan bölgelerde, bkz. [d serisi kullanılabilirlik](#m-series-1).

#### <a name="azure-offer-types-supported-by-m-series"></a>D serisi tarafından desteklenen Azure teklif türleri

E-seriye erişmek için abonelik, Kullandıkça Öde veya Kurumsal Anlaşma (EA) dahil olmak üzere ücretli bir teklif türü olmalıdır.  A serisi tarafından desteklenen Azure teklif türlerinin tam listesi için, [harcama limitleri olmadan geçerli tekliflere](https://azure.microsoft.com/support/legal/offer-details)bakın.

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>DC serisi

> [!NOTE]
> DC-Series Şu anda **genel önizleme** aşamasındadır.

- DC Serisi donanım, yazılım koruyucu uzantıları (Intel SGX) teknolojisine sahip Intel işlemcileri kullanır.
- DC-Series, diğer donanım yapılandırmalarında desteklenmeyen [güvenli şifreleme ile Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves)için gereklidir.
- DC-Series, hassas verileri işleyen iş yükleri için ve güvenli şifreleme ile Always Encrypted tarafından sunulan isteğe bağlı gizli sorgu işleme yeteneklerini,
- DC Serisi donanım, dengeli işlem ve bellek kaynakları sağlar.

DC-Series yalnızca sağlanan işlem için desteklenir (sunucusuz desteklenmez) ve bölge yedekliliği desteklemez. DC Serisi kullanılabildiği bölgeler için bkz. [DC Serisi kullanılabilirliği](#dc-series-1).

#### <a name="azure-offer-types-supported-by-dc-series"></a>DC serisi tarafından desteklenen Azure teklif türleri

DC-Series 'e erişmek için abonelik, Kullandıkça Öde veya Kurumsal Anlaşma (EA) dahil olmak üzere ücretli bir teklif türü olmalıdır.  DC serisi tarafından desteklenen Azure teklif türlerinin tam listesi için, [harcama limitleri olmadan geçerli tekliflere](https://azure.microsoft.com/support/legal/offer-details)bakın.

### <a name="compute-and-memory-specifications"></a>İşlem ve bellek belirtimleri


|Donanım oluşturma  |İşlem  |Bellek  |
|:---------|:---------|:---------|
|4. nesil     |-Intel® E5-2673 v3 (Haswell) 2,4 GHz işlemcileri<br>-En fazla 24 sanal çekirdek sağlama (1 sanal çekirdek = 1 fiziksel çekirdek)  |-Sanal çekirdek başına 7 GB<br>-168 GB 'a kadar sağlama|
|5. nesil     |**Sağlanan işlem**<br>-Intel® E5-2673 v4 (çok Iyi) 2,3-GHz, Intel® SP-8160 (ufuk Gölü) \* ve ıntel® 8272CL (Cascade Lake) 2,5 GHz \* işlemcileri<br>-En fazla 80 sanal çekirdek sağlama (1 sanal çekirdek = 1 hiper iş parçacığı)<br><br>**Sunucusuz işlem**<br>-Intel® E5-2673 v4 (çok Iyi) 2,3 GHz ve Intel® SP-8160 (ufuk Gölü) * işlemciler<br>-40 sanal çekirdeğe kadar otomatik ölçeklendirme (1 sanal çekirdek = 1 hiper iş parçacığı)|**Sağlanan işlem**<br>-vCore başına 5,1 GB<br>-408 GB 'a kadar sağlama<br><br>**Sunucusuz işlem**<br>-VCore başına 24 GB 'a kadar otomatik ölçeklendirme<br>-En fazla 120 GB 'a kadar otomatik ölçeklendirme|
|Fsv2 serisi     |-Intel® 8168 (ufuk Gölü) işlemcileri<br>-Sürekli olarak 3,4 GHz 'nin tüm Core Turbo saat hızına ve en fazla 3,7 GHz bir adet tek çekirdekli Turbo saat hızına sahiptir.<br>-En fazla 72 sanal çekirdek sağlama (1 sanal çekirdek = 1 hiper iş parçacığı)|-vCore başına 1,9 GB<br>-136 GB 'a kadar sağlama|
|M serisi     |-Intel® E7-8890 v3 2,5 GHz ve Intel® 8280D 2,7 GHz (Cascade Lake) işlemcileri<br>-En fazla 128 sanal çekirdek sağlama (1 sanal çekirdek = 1 hiper iş parçacığı)|-vCore başına 29 GB<br>-3,7 TB 'a kadar sağlama|
|DC serisi     | -Intel XEON E-2288G işlemcileri<br>-Intel Software Guard uzantısı (Intel SGX))<br>-En fazla 8 sanal çekirdek sağlama (1 sanal çekirdek = 1 fiziksel çekirdek) | vCore başına 4,5 GB |

\*[Sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) dinamik yönetim görünümünde, ıNTEL® SP-8160 (ufuk Gölü) işlemcileri kullanan veritabanları için donanım oluşturma Gen6 olarak görünür, ancak ıNTEL® 8272CL (Cascade Lake) kullanan veritabanları için donanım oluşturma, Gen7 olarak görünür. Tüm 5. nesil veritabanlarının kaynak sınırları, işlemci türünden (çok Iyi, ufuk Gölü veya Cascade Lake) bağımsız olarak aynıdır.

Kaynak limitleri hakkında daha fazla bilgi için bkz. [tek veritabanları (sanal çekirdek) Için kaynak limitleri](resource-limits-vcore-single-databases.md)veya [elastik havuzlar (Vcore) için kaynak sınırları](resource-limits-vcore-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Donanım oluşturma seçme

Azure portal, oluşturma sırasında SQL veritabanında bir veritabanı veya havuz için donanım oluşturmayı seçebilir veya var olan bir veritabanının veya havuzun donanım üretimini değiştirebilirsiniz.

**Bir SQL veritabanı veya havuzu oluştururken bir donanım oluşturma seçmek için**

Ayrıntılı bilgi için bkz. [SQL veritabanı oluşturma](single-database-create-quickstart.md).

**Temel bilgiler** sekmesinde, **işlem + depolama** bölümünde **Veritabanını yapılandır** bağlantısını seçin ve ardından **yapılandırma bağlantısını değiştir** ' i seçin:

  ![veritabanı yapılandırma](./media/service-tiers-vcore/configure-sql-database.png)

İstediğiniz donanım üretimini seçin:

  ![donanım seçin](./media/service-tiers-vcore/select-hardware.png)


**Mevcut bir SQL veritabanının veya havuzunun donanım oluşturmayı değiştirmek için**

Bir veritabanı için genel bakış sayfasında, **fiyatlandırma katmanı** bağlantısını seçin:

  ![donanımı değiştirme](./media/service-tiers-vcore/change-hardware.png)

Bir havuz için genel bakış sayfasında **Yapılandır**' ı seçin.

Yapılandırmayı değiştirmek için adımları izleyin ve önceki adımlarda açıklandığı gibi donanım üretimini seçin.

**SQL yönetilen örneği oluştururken bir donanım oluşturma seçmek için**

Ayrıntılı bilgi için bkz. [SQL yönetilen örneği oluşturma](../managed-instance/instance-create-quickstart.md).

**Temel bilgiler** sekmesinde, **işlem + depolama** bölümünde **Veritabanını yapılandır** bağlantısını seçin ve ardından istenen donanım oluşturma ' yı seçin:

  ![SQL yönetilen örneği yapılandırma](./media/service-tiers-vcore/configure-managed-instance.png)
  
**Mevcut bir SQL yönetilen örneğinin donanım üretimini değiştirmek için**

# <a name="the-azure-portal"></a>[Azure portal](#tab/azure-portal)

SQL yönetilen örneği sayfasında, Ayarlar bölümünün altına yerleştirilmiş **fiyatlandırma katmanı** bağlantısı ' nı seçin.

![SQL yönetilen örnek donanımını değiştirme](./media/service-tiers-vcore/change-managed-instance-hardware.png)

Fiyatlandırma Katmanı sayfasında, önceki adımlarda açıklandığı gibi donanım oluşturmayı değiştirebilirsiniz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aşağıdaki PowerShell komut dosyasını kullanın:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Daha fazla ayrıntı için [set-Azsqlınstance](/powershell/module/az.sql/set-azsqlinstance) komutunu işaretleyin.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Aşağıdaki CLı komutunu kullanın:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Daha fazla ayrıntı için [az SQL mı Update](/cli/azure/sql/mi#az_sql_mi_update) komutunu işaretleyin.

---

### <a name="hardware-availability"></a>Donanım kullanılabilirliği

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> 4. nesil/5. nesil

4. nesil donanım [kullanıma alınıyor](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) ve Yeni dağıtımlar için artık kullanılamıyor. Tüm yeni veritabanlarının 5. nesil donanımında dağıtılması gerekir.

5. nesil tüm dünyada genel bölgelerde kullanılabilir.

#### <a name="fsv2-series"></a>Fsv2 serisi

Fsv2 serisi şu bölgelerde kullanılabilir: Avustralya Orta, Avustralya Orta 2, Avustralya Doğu, Avustralya Güneydoğu, Brezilya Güney, Kanada Orta, Doğu Asya, Doğu ABD, Fransa Orta, Hindistan Orta, Kore Orta, Kore Güney, Kuzey Avrupa, Güney Afrika Kuzey, Güneydoğu Asya, UK Güney, UK Batı, Batı Avrupa, Batı ABD 2.


#### <a name="m-series"></a>M serisi

A serisi şu bölgelerde kullanılabilir: Doğu ABD, Kuzey Avrupa, Batı Avrupa, Batı ABD 2.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

#### <a name="dc-series"></a>DC serisi

> [!NOTE]
> DC-Series Şu anda **genel önizleme** aşamasındadır.

DC-Series şu bölgelerde kullanılabilir: Kanada Orta, Kanada Doğu, Doğu ABD, Kuzey Avrupa, UK Güney, Batı Avrupa, Batı ABD.

Şu anda desteklenmeyen bir bölgede DC Serisi gerekiyorsa, [Azure SQL veritabanı ve SQL yönetilen örneği Için istek kotası artışlarına ilişkin](quota-increase-request.md)yönergeleri izleyerek [bir destek bileti gönderebilirsiniz](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="next-steps"></a>Sonraki adımlar

Başlamak için bkz.: 
- [Azure portal kullanarak SQL veritabanı oluşturma](single-database-create-quickstart.md)
- [Azure portal kullanarak bir SQL yönetilen örneği oluşturma](../managed-instance/instance-create-quickstart.md)

Fiyatlandırma ayrıntıları için bkz. [Azure SQL Veritabanı fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/sql-database/single/).

Genel amaçlı ve iş açısından kritik hizmet katmanlarında bulunan belirli işlem ve depolama boyutları hakkında daha fazla bilgi için bkz.:

- [Azure SQL veritabanı Için sanal çekirdek tabanlı kaynak sınırları](resource-limits-vcore-single-databases.md).
- [havuza alınmış Azure SQL veritabanı Için sanal çekirdek tabanlı kaynak sınırları](resource-limits-vcore-elastic-pools.md).
- [Azure SQL yönetilen örneği Için sanal çekirdek tabanlı kaynak sınırları](../managed-instance/resource-limits.md).
