---
title: Otomatik ayarlamayı etkinleştirme
description: Azure portal kullanarak veritabanınızda otomatik ayarlamayı kolayca etkinleştirebilirsiniz.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/03/2021
ms.openlocfilehash: 1362d8c1f15b64b9d76b28fd354cdae8919504b0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558287"
---
# <a name="enable-automatic-tuning-in-the-azure-portal-to-monitor-queries-and-improve-workload-performance"></a>Sorguları izlemek ve iş yükü performansını geliştirmek için Azure portal otomatik ayarlamayı etkinleştirin
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı, sorgularınızı sürekli olarak izleyen veri hizmetlerini otomatik olarak yönetir ve iş yükünüzün performansını artırmak için gerçekleştirebileceğiniz eylemi tanımlar. Önerileri gözden geçirebilir ve bunları el ile uygulayabilir ya da Azure SQL veritabanı 'nın düzeltici eylemleri otomatik olarak uygulamasını sağlayabilirsiniz. Bu, **otomatik ayarlama modu** olarak bilinir.

Otomatik ayarlama, sunucuda veya veritabanı düzeyinde şu şekilde etkinleştirilebilir:

- [Azure Portal](automatic-tuning-enable.md#azure-portal)
- [REST API](automatic-tuning-enable.md#rest-api) çağrılar
- [T-SQL](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true) komutları

> [!NOTE]
> Azure SQL yönetilen örneği için desteklenen FORCE_LAST_GOOD_PLAN seçeneği yalnızca [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) ile yapılandırılabilir. Bu makalede açıklanan Azure portal tabanlı yapılandırma ve Otomatik Dizin ayarlama seçenekleri Azure SQL yönetilen örneği için uygulanmıyor.

> [!NOTE]
> ARM (Azure Resource Manager) şablonu aracılığıyla otomatik ayarlama seçeneklerinin yapılandırılması Şu anda desteklenmiyor.

## <a name="enable-automatic-tuning-on-server"></a>Sunucuda otomatik ayarlamayı etkinleştir

Sunucu düzeyinde, otomatik ayarlama yapılandırmasını "Azure Varsayılanları" ndan devralmayı veya yapılandırmayı devralma seçeneğini belirleyebilirsiniz. Azure Varsayılanları FORCE_LAST_GOOD_PLAN etkindir, CREATE_INDEX devre dışıdır ve DROP_INDEX devre dışıdır.

> [!IMPORTANT]
> Mart itibariyle, 2020 ' den başlayarak otomatik ayarlama için yeni Azure Varsayılanları aşağıdaki gibidir:
>
> - FORCE_LAST_GOOD_PLAN = etkin, CREATE_INDEX = devre dışı ve DROP_INDEX = devre dışı.
> - Yapılandırılmış otomatik ayarlama tercihleri olmayan mevcut sunucular, Azure varsayılanlarını DEVRALACAK şekilde otomatik olarak yapılandırılır. Bu, şu anda tanımsız bir durumda otomatik ayarlama için sunucu ayarlarına sahip olan tüm müşteriler için geçerlidir.
> - Oluşturulan yeni sunucular, Azure varsayılanlarını DEVRALACAK şekilde otomatik olarak yapılandırılacaktır (otomatik ayarlama yapılandırması yeni sunucu oluşturma sırasında tanımsız bir durumda olduğunda daha önce olduğu gibi).

### <a name="azure-portal"></a>Azure portalı

Azure SQL veritabanı 'nda bir [sunucuda](logical-servers.md) otomatik ayarlamayı etkinleştirmek için Azure Portal sunucuya gidin ve sonra menüdeki **otomatik ayarlama** ' yı seçin.

![Ekran görüntüsü, Azure portal bir sunucu için seçenekler uygulayabileceğiniz, otomatik ayarlamayı gösterir.](./media/automatic-tuning-enable/server.png)

> [!NOTE]
> Lütfen şu anda **DROP_INDEX** seçeneğinin bölüm değiştirme ve Dizin ipuçları kullanan uygulamalarla uyumlu olmadığından ve bu durumlarda etkinleştirilmemelidir. Kullanılmayan dizinleri bırakma, Premium ve İş Açısından Kritik hizmet katmanlarında desteklenmez.

Etkinleştirmek istediğiniz otomatik ayarlama seçeneklerini belirleyin ve **Uygula**' yı seçin.

Bir sunucudaki otomatik ayarlama seçenekleri, bu sunucudaki tüm veritabanlarına uygulanır. Varsayılan olarak, tüm veritabanları yapılandırma üst sunucusundan devralınır, ancak bu, her veritabanı için ayrı ayrı geçersiz kılınabilir ve belirtilebilir.

### <a name="rest-api"></a>REST API

Bir **sunucuda** otomatik ayarlamayı etkinleştirmek üzere bir REST API kullanma hakkında daha fazla bilgi edinmek için bkz. [sunucu otomatik ayarlama güncelleştirme ve http yöntemlerini alma](/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Tek bir veritabanında otomatik ayarlamayı etkinleştir

Azure SQL veritabanı, her bir veritabanı için otomatik ayarlama yapılandırmasını tek tek belirtmenizi sağlar. Veritabanı düzeyinde otomatik ayarlama yapılandırmasını "Azure Varsayılanları" ana sunucusundan devralmayı veya yapılandırmayı devralma seçeneğini belirleyebilirsiniz. Azure Varsayılanları FORCE_LAST_GOOD_PLAN etkinleştirilmiş, CREATE_INDEX devre dışı bırakılmış ve DROP_INDEX devre dışı bırakılmış olarak ayarlanmıştır.

> [!TIP]
> Genel öneri, otomatik ayarlama yapılandırmasını **sunucu düzeyinde** yönetmelidir, böylece aynı yapılandırma ayarları her veritabanına otomatik olarak uygulanabilir. Yalnızca veritabanının aynı sunucudan gelen ayarları devrallarından farklı ayarlara sahip olması gerekiyorsa, tek bir veritabanında otomatik ayarlamayı yapılandırın.

### <a name="azure-portal"></a>Azure portalı

**Tek bir veritabanında** otomatik ayarlamayı etkinleştirmek için Azure Portal veritabanına gidin ve **otomatik ayarlama**' yı seçin.

Bireysel otomatik ayarlama ayarları her veritabanı için ayrı olarak yapılandırılabilir. Tek bir otomatik ayarlama seçeneğini el ile yapılandırabilir veya bir seçeneğin ayarlarını sunucudan devralmasını belirtebilirsiniz.

![Ekran görüntüsü Azure portal otomatik ayarlamayı gösterir; burada tek bir veritabanı için seçenekler uygulayabilirsiniz.](./media/automatic-tuning-enable/database.png)

Lütfen şu anda DROP_INDEX seçeneğinin bölüm değiştirme ve Dizin ipuçları kullanan uygulamalarla uyumlu olmadığına ve bu durumlarda etkinleştirilmemelidir.

İstediğiniz yapılandırmayı seçtikten sonra **Uygula**' ya tıklayın.

### <a name="rest-api"></a>Rest API

Tek bir veritabanında otomatik ayarlamayı etkinleştirmek üzere bir REST API kullanma hakkında daha fazla bilgi edinmek için bkz. [Azure SQL veritabanı otomatik ayarlama güncelleştirme ve http YÖNTEMLERINI alma](/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

T-SQL aracılığıyla tek bir veritabanında otomatik ayarlamayı etkinleştirmek için veritabanına bağlanın ve aşağıdaki sorguyu yürütün:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Otomatik ayarlamayı OTOMATIK olarak ayarlamak, Azure varsayılanlarını uygular. DEVRALMA olarak ayarlandığında, otomatik ayarlama yapılandırması üst sunucudan devralınacaktır. Özel ' i seçtiğinizde otomatik ayarlamayı el ile yapılandırmanız gerekecektir.

Bireysel otomatik ayarlama seçeneklerini T-SQL aracılığıyla yapılandırmak için veritabanına bağlanın ve şunun gibi sorguyu yürütün:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = ON, DROP_INDEX = OFF)
```

Bireysel ayarlama seçeneğinin açık olarak ayarlanması, veritabanının Devralındığı tüm ayarları geçersiz kılar ve ayarlama seçeneğini etkinleştirir. Devre dışı olarak ayarlanması, veritabanının Devralındığı tüm ayarları geçersiz kılar ve ayarlama seçeneğini devre dışı bırakır. Varsayılan olarak belirtilen otomatik ayarlama seçeneği, sunucu düzeyi ayarlarından otomatik ayarlama yapılandırmasını devralacak.  

> [!IMPORTANT]
> [Etkin coğrafi çoğaltma](auto-failover-group-overview.md)durumunda, otomatik ayarlamaya yalnızca birincil veritabanında yapılandırılması gerekir. Örneğin, dizin oluşturma veya silme gibi otomatik olarak uygulanan ayarlama eylemleri, salt okunurdur. Salt okunabilir ikincil üzerinde T-SQL aracılığıyla otomatik ayarlamayı etkinleştirmeye çalışmak, salt okunurdur ikincil üzerinde farklı bir ayarlama yapılandırması desteklenmediğinden hata oluşmasına neden olur.
>

Otomatik ayarlamayı yapılandırmaya yönelik daha fazla bilgi edinmek için, bkz. [alter database set Options (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true).

## <a name="troubleshooting"></a>Sorun giderme

### <a name="automated-recommendation-management-is-disabled"></a>Otomatik öneri yönetimi devre dışı

Otomatikleştirilmiş öneri yönetiminin devre dışı bırakıldığı veya sistem tarafından devre dışı bırakıldığı hata iletileri söz konusu olduğunda, en yaygın nedenler şunlardır:
- Sorgu deposu etkin değil veya
- Sorgu deposu belirtilen bir veritabanı için salt okunurdur modunda veya
- Ayrılan depolama alanını kullandığı için sorgu deposu çalışmayı durdurdu.

Bu sorunu düzeltmek için aşağıdaki adımlar kabul edilebilir:
- Sorgu deposunu temizleyin veya veri saklama süresini T-SQL kullanarak "otomatik" olarak değiştirin. [Sorgu deposu için önerilen saklama ve yakalama ilkesini yapılandırma](./query-performance-insight-use.md#recommended-retention-and-capture-policy)konusuna bakın.
- SQL Server Management Studio (SSMS) kullanın ve şu adımları izleyin:
  - Azure SQL veritabanına bağlanma
  - Veritabanına sağ tıklayın
  - Özellikler ' e gidin ve sorgu deposu ' na tıklayın
  - Işlem modunu Read-Write olarak değiştirme
  - Mağaza yakalama modunu otomatik olarak değiştirme
  - Boyut tabanlı temizleme modunu otomatik olarak değiştirme

### <a name="permissions"></a>İzinler

Otomatik ayarlama bir Azure özelliği olduğundan, bunu kullanmak için Azure 'un yerleşik rollerini kullanmanız gerekir. Yalnızca SQL kimlik doğrulaması kullanmak Azure portal özelliği kullanmak yeterli olmaz.

Otomatik ayarlamayı kullanmak için, kullanıcıya verilmesi gereken en düşük izin Azure 'un yerleşik [SQL veritabanı katılımcısı](../../role-based-access-control/built-in-roles.md#sql-db-contributor) rolüdür. SQL Server katkıda bulunan, SQL yönetilen örnek katılımcısı, katkıda bulunan ve sahip gibi daha yüksek ayrıcalıklı rolleri kullanmayı da düşünebilirsiniz.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Otomatik ayarlama e-posta bildirimlerini yapılandırma

Otomatik olarak ayarlama tarafından yapılan önerilere yönelik otomatik e-posta bildirimleri almak için bkz. [otomatik olarak ayarlama e-posta bildirimleri](automatic-tuning-email-notifications-configure.md) Kılavuzu.

## <a name="next-steps"></a>Sonraki adımlar

- Otomatik ayarlama hakkında daha fazla bilgi edinmek ve performansı artırmanıza nasıl yardımcı olabileceğini öğrenmek için [otomatik ayarlama makalesini](automatic-tuning-overview.md) okuyun.
- Azure SQL veritabanı performans önerilerine genel bakış için bkz. [performans önerileri](database-advisor-implement-performance-recommendations.md) .
- En iyi sorguların performans etkisini görüntüleme hakkında bilgi edinmek için bkz. [sorgu Performans öngörüleri](query-performance-insight-use.md) .