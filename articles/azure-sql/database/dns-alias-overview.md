---
title: DNS diğer adı
description: Uygulamalarınız, Azure SQL veritabanı için sunucu adı için bir diğer ada bağlanabilir. Bu arada, test etmeyi kolaylaştırmak için diğer adı işaret eden SQL veritabanını dilediğiniz zaman değiştirebilirsiniz.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 6ef268b349d5a21cdbadd55ffd2199a35f650e5b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376299"
---
# <a name="dns-alias-for-azure-sql-database"></a>Azure SQL veritabanı için DNS diğer adı
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL veritabanında bir etki alanı adı sistemi (DNS) sunucusu vardır. PowerShell ve REST API 'Leri, [MANTıKSAL SQL Server](logical-servers.md) ADıNıZ için [DNS diğer adları oluşturma ve yönetme çağrılarını](#anchor-powershell-code-62x) kabul eder.

Sunucu adı yerine bir *DNS diğer adı* kullanılabilir. İstemci programları, bağlantı dizelerindeki diğer adı kullanabilir. DNS diğer adı, istemci programlarınızı farklı sunuculara yönlendirebilmek için bir çeviri katmanı sağlar. Bu katman, tüm istemcileri ve bunların bağlantı dizelerini bulma ve düzenleme zorunluluğunu ortadan çıkar.

Bir DNS diğer adı için ortak kullanımlar aşağıdaki durumları içerir:

- Sunucu için kolay anımsanacak bir ad oluşturun.
- İlk geliştirme sırasında, diğer adınız bir test sunucusuna başvurabilir. Uygulama canlı kaldığında, diğer adı, üretim sunucusuna başvuracak şekilde değiştirebilirsiniz. Testten üretime geçiş, yapılandırmaya herhangi bir değişiklik yapılmasını gerektirmez ve sunucuya bağlanan birkaç istemci vardır.
- Uygulamanızdaki tek veritabanının başka bir sunucuya taşındığını varsayın. Birkaç istemcinin yapılandırmasını değiştirmek zorunda kalmadan diğer adı değiştirebilirsiniz.
- Bölgesel bir kesinti sırasında, veritabanınızı farklı bir sunucu ve bölgede kurtarmak için coğrafi geri yükleme kullanırsınız. Mevcut istemci uygulamanın yeniden bağlanabilmesi için mevcut diğer adınızı yeni sunucuyu işaret etmek üzere değiştirebilirsiniz.

## <a name="domain-name-system-dns-of-the-internet"></a>Internet 'in etki alanı adı sistemi (DNS)

Internet, DNS 'i kullanır. DNS, kolay adlarınızı sunucunuzun adına dönüştürür.

## <a name="scenarios-with-one-dns-alias"></a>Bir DNS diğer adına sahip senaryolar

Sisteminizi yeni bir sunucuya geçmeniz gerektiğini varsayalım. Geçmişte her istemci programında her bağlantı dizesini bulup güncelleştirmeniz gerekir. Ancak, bağlantı dizeleri bir DNS diğer adı kullanıyorsa, yalnızca bir diğer ad özelliği güncellenmelidir.

Azure SQL veritabanı 'nın DNS diğer adı özelliği, aşağıdaki senaryolarda yardımcı olabilir:

### <a name="test-to-production"></a>Üretime test etme

İstemci programlarını geliştirmeye başladığınızda, bunların bağlantı dizelerinde bir DNS diğer adı kullanmalarını sağlayabilirsiniz. Diğer ad noktasının özelliklerini sunucunuzun bir test sürümüne yaparsınız.

Daha sonra yeni sistem üretimde canlı kaldığında, diğer adın özelliklerini üretim sunucusuna işaret etmek için güncelleştirebilirsiniz. İstemci programlarında hiçbir değişiklik yapılması gerekmez.

### <a name="cross-region-support"></a>Çapraz bölge desteği

Olağanüstü durum kurtarma, sunucunuzu farklı bir coğrafi bölgeye kaydırabilirler. DNS diğer adı kullanan bir sistem için tüm istemciler için tüm bağlantı dizelerini bulma ve güncelleştirme gereksinimlerinden kaçınılabilir. Bunun yerine, artık Azure SQL veritabanınızı barındıran yeni sunucuya başvurmak üzere bir diğer ad güncelleştirebilirsiniz.

## <a name="properties-of-a-dns-alias"></a>DNS diğer adının özellikleri

Aşağıdaki özellikler, sunucunuzun her bir DNS diğer adı için geçerlidir:

- *Benzersiz ad:* Oluşturduğunuz her bir diğer ad, sunucu adlarının olduğu gibi tüm sunucular genelinde benzersizdir.
- *Sunucu gereklidir:* Bir DNS diğer adı, tam olarak bir sunucuya başvurmadığı ve sunucu zaten var olmalıdır. Güncelleştirilmiş bir diğer ad, her zaman tam olarak bir mevcut sunucuya başvurmalıdır.
  - Bir sunucuyu bıraktığınızda, Azure sistemi sunucuya başvuran tüm DNS diğer adlarını da bırakır.
- *Hiçbir bölgeye bağlanmadı:* DNS diğer adları bir bölgeye bağlanmamış. Herhangi bir DNS diğer adı, herhangi bir coğrafi bölgede yer alan bir sunucuya başvuracak şekilde güncelleştirilemeyebilir.
  - Ancak diğer bir sunucuya başvuracak bir diğer ad güncelleştirilirken her iki sunucu da aynı Azure *aboneliğinde* bulunmalıdır.
- *İzinler:* Bir DNS diğer adını yönetmek için, kullanıcının *sunucu katılımcısı* izinlerinin veya daha yüksek olması gerekir. Daha fazla bilgi için bkz. [Azure Portal Azure rol tabanlı erişim denetimi ile çalışmaya başlama](../../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>DNS diğer adlarınızı yönetin

Hem REST API 'Leri hem de PowerShell cmdlet 'leri, DNS diğer adlarını programlı bir şekilde yönetmenizi sağlar.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>DNS diğer adlarınızı yönetmek için REST API 'Leri

REST API 'Leri için belgeler aşağıdaki Web konumlarına yakın bir yerde mevcuttur:

- [Azure SQL veritabanı REST API](/rest/api/sql/)

Ayrıca REST API 'Leri GitHub ' da görülebilir:

- [Azure SQL veritabanı DNS diğer ad REST API 'Leri](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>DNS diğer adlarınızı yönetmek için PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

REST API 'leri çağıran PowerShell cmdlet 'leri kullanılabilir.

DNS diğer adlarını yönetmek için kullanılan PowerShell cmdlet 'lerinin kod örneği şu adreste belgelenmiştir:

- [DNS diğer adı için PowerShell Azure SQL veritabanı](dns-alias-powershell-create.md)

Kod örneğinde kullanılan cmdlet 'ler şunlardır:

- [New-Azsqlserverdnsalıas](/powershell/module/az.Sql/New-azSqlServerDnsAlias): Azure SQL veritabanı hizmet sisteminde yenı bir DNS diğer adı oluşturur. Diğer ad sunucu 1 ' e başvurur.
- [Get-Azsqlserverdnsalıas](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): sunucu 1 ' e atanan tüm DNS diğer adlarını alın ve listeleyin.
- [Set-Azsqlserverdnsalıas](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): diğer adın, sunucu 1 ' den sunucu 2 ' ye başvuracak şekilde yapılandırıldığı sunucu adını değiştirir.
- [Remove-Azsqlserverdnsalıas](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): DNS diğer adını sunucu 2 ' den diğer ad adını kullanarak kaldırın.

## <a name="limitations"></a>Sınırlamalar

Şu anda bir DNS diğer adı aşağıdaki sınırlamalara sahiptir:

- *2 dakikaya kadar gecikme:* Bir DNS diğer adının güncellenmesi veya kaldırılması 2 dakikaya kadar sürer.
  - Kısa bir gecikmeden bağımsız olarak, diğer ad eski sunucuya yönelik istemci bağlantılarını hemen sonlandırır.
- *DNS araması:* Şimdilik, belirli bir DNS diğer adının ne olduğunu kontrol etmenin tek yetkili yolu, [DNS araması](/windows-server/administration/windows-commands/nslookup)gerçekleştirmektedir.
- _Tablo denetimi desteklenmiyor:_ Bir veritabanında *tablo denetimi* etkinleştirilmiş BIR sunucuda DNS diğer adı kullanamazsınız.
  - Tablo denetimi kullanım dışıdır.
  - [BLOB denetimine](../../azure-sql/database/auditing-overview.md)taşımanızı öneririz.

## <a name="related-resources"></a>İlgili kaynaklar

- Olağanüstü durum kurtarma dahil olmak üzere [Azure SQL veritabanı ile iş sürekliliği 'Ne genel bakış](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- [Azure REST API başvurusu](/rest/api/azure/)
- [Sunucu DNS diğer adları API 'SI](/rest/api/sql/2020-11-01-preview/serverdnsaliases)

## <a name="next-steps"></a>Sonraki adımlar

- [DNS diğer adı için PowerShell Azure SQL veritabanı](dns-alias-powershell-create.md)
