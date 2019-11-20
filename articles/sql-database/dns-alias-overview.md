---
title: DNS diğer adı
description: Uygulamalarınız Azure SQL veritabanı sunucunuzun adı için bir diğer ada bağlanabilir. Bu arada, test etmeyi kolaylaştırmak için diğer adı işaret eden SQL veritabanını dilediğiniz zaman değiştirebilirsiniz.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 05fa542a0ad1c72f73148eefd304a9771798598d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820625"
---
# <a name="dns-alias-for-azure-sql-database"></a>Azure SQL veritabanı için DNS diğer adı

Azure SQL veritabanında bir etki alanı adı sistemi (DNS) sunucusu vardır. PowerShell ve REST API 'Leri, SQL veritabanı sunucunuzun adı için [DNS diğer adları oluşturma ve yönetme çağrılarını](#anchor-powershell-code-62x) kabul eder.

Azure SQL veritabanı sunucu adı yerine bir *DNS diğer adı* kullanılabilir. İstemci programları, bağlantı dizelerindeki diğer adı kullanabilir. DNS diğer adı, istemci programlarınızı farklı sunuculara yönlendirebilmek için bir çeviri katmanı sağlar. Bu katman, tüm istemcileri ve bunların bağlantı dizelerini bulma ve düzenleme zorunluluğunu ortadan çıkar.

Bir DNS diğer adı için ortak kullanımlar aşağıdaki durumları içerir:

- Azure SQL Server için kolay hatırlayabileceğiniz bir ad oluşturun.
- İlk geliştirme sırasında, diğer adınız bir test SQL veritabanı sunucusuna başvurabilir. Uygulama canlı kaldığında, diğer adı, üretim sunucusuna başvuracak şekilde değiştirebilirsiniz. Testten üretime geçiş, yapılandırma için veritabanı sunucusuna bağlanan birkaç istemciye herhangi bir değişiklik yapılmasını gerektirmez.
- Uygulamanızdaki tek veritabanının başka bir SQL veritabanı sunucusuna taşındığını varsayın. Burada, birkaç istemcinin yapılandırmasını değiştirmek zorunda kalmadan diğer adı değiştirebilirsiniz.
- Bölgesel bir kesinti sırasında, veritabanınızı farklı bir sunucu ve bölgede kurtarmak için coğrafi geri yükleme kullanırsınız. Mevcut istemci uygulamanın yeniden bağlanabilmesi için mevcut diğer adınızı yeni sunucuyu işaret etmek üzere değiştirebilirsiniz. 

## <a name="domain-name-system-dns-of-the-internet"></a>Internet 'in etki alanı adı sistemi (DNS)

Internet, DNS 'i kullanır. DNS, kolay adlarınızı Azure SQL veritabanı sunucunuzun adına dönüştürür.

## <a name="scenarios-with-one-dns-alias"></a>Bir DNS diğer adına sahip senaryolar

Sisteminizi yeni bir Azure SQL veritabanı sunucusuna geçmeniz gerektiğini varsayın. Geçmişte her istemci programında her bağlantı dizesini bulup güncelleştirmeniz gerekir. Ancak, bağlantı dizeleri bir DNS diğer adı kullanıyorsa, yalnızca bir diğer ad özelliği güncellenmelidir.

Azure SQL veritabanı 'nın DNS diğer adı özelliği, aşağıdaki senaryolarda yardımcı olabilir:

### <a name="test-to-production"></a>Üretime test etme

İstemci programlarını geliştirmeye başladığınızda, bunların bağlantı dizelerinde bir DNS diğer adı kullanmalarını sağlayabilirsiniz. Diğer ad noktasının özelliklerini Azure SQL veritabanı sunucunuzun bir test sürümüne yaparsınız.

Daha sonra yeni sistem üretimde etkin olduğunda, diğer adın özelliklerini üretim SQL veritabanı sunucusuna işaret etmek için güncelleştirebilirsiniz. İstemci programlarında hiçbir değişiklik yapılması gerekmez.

### <a name="cross-region-support"></a>Çapraz bölge desteği

Olağanüstü durum kurtarma SQL veritabanı sunucunuzu farklı bir coğrafi bölgeye kaydırabilirler. DNS diğer adı kullanan bir sistem için tüm istemciler için tüm bağlantı dizelerini bulma ve güncelleştirme gereksinimlerinden kaçınılabilir. Bunun yerine, artık veritabanınızı barındıran yeni SQL veritabanı sunucusuna başvurmak için bir diğer ad güncelleştirebilirsiniz.

## <a name="properties-of-a-dns-alias"></a>DNS diğer adının özellikleri

Aşağıdaki özellikler, SQL veritabanı sunucunuzun her bir DNS diğer adı için geçerlidir:

- *Benzersiz ad:* Oluşturduğunuz her bir diğer ad, sunucu adlarının olduğu gibi tüm Azure SQL veritabanı sunucuları genelinde benzersizdir.
- *Sunucu gereklidir:* Bir DNS diğer adı, tam olarak bir sunucuya başvurmadığı ve sunucu zaten var olmalıdır. Güncelleştirilmiş bir diğer ad, her zaman tam olarak bir mevcut sunucuya başvurmalıdır.
  - Bir SQL veritabanı sunucusunu bıraktığınızda, Azure sistemi sunucuya başvuran tüm DNS diğer adlarını da bırakır.
- *Hiçbir bölgeye bağlanmadı:* DNS diğer adları bir bölgeye bağlanmamış. Herhangi bir DNS diğer adı, herhangi bir coğrafi bölgede bulunan bir Azure SQL veritabanı sunucusuna başvuracak şekilde güncelleştirilemeyebilir.
  - Ancak diğer bir sunucuya başvuracak bir diğer ad güncelleştirilirken her iki sunucu da aynı Azure *aboneliğinde*bulunmalıdır.
- *İzinler:* Bir DNS diğer adını yönetmek için, kullanıcının *sunucu katılımcısı* izinlerinin veya daha yüksek olması gerekir. Daha fazla bilgi için, bkz. [Azure Portal rol tabanlı Access Control kullanmaya başlama](../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>DNS diğer adlarınızı yönetin

Hem REST API 'Leri hem de PowerShell cmdlet 'leri, DNS diğer adlarını programlı bir şekilde yönetmenizi sağlar.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>DNS diğer adlarınızı yönetmek için REST API 'Leri

REST API 'Leri için belgeler aşağıdaki Web konumlarına yakın bir yerde mevcuttur:

- [Azure SQL veritabanı REST API](https://docs.microsoft.com/rest/api/sql/)

Ayrıca REST API 'Leri GitHub ' da görülebilir:

- [Azure SQL veritabanı sunucusu, DNS diğer adı REST API 'Leri](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>DNS diğer adlarınızı yönetmek için PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

REST API 'leri çağıran PowerShell cmdlet 'leri kullanılabilir.

DNS diğer adlarını yönetmek için kullanılan PowerShell cmdlet 'lerinin kod örneği şu adreste belgelenmiştir:

- [DNS diğer adı için PowerShell Azure SQL veritabanı](dns-alias-powershell.md)

Kod örneğinde kullanılan cmdlet 'ler şunlardır:

- [New-Azsqlserverdnsalıas](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Azure SQL veritabanı hizmet sisteminde yenı bir DNS diğer adı oluşturur. Diğer ad, Azure SQL veritabanı sunucusu 1 ' e başvurur.
- [Get-Azsqlserverdnsalıas](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): SQL DB Server 1 ' e atanan tüm DNS diğer adlarını alın ve listeleyin.
- [Set-Azsqlserverdnsalıas](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): diğer adın, Server 1 ' den SQL DB Server 2 ' ye başvuracak şekilde yapılandırıldığı sunucu adını değiştirir.
- [Remove-Azsqlserverdnsalıas](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): DNS DIĞER adını SQL DB sunucu 2 ' den diğer ad adını kullanarak kaldırın.

## <a name="limitations-during-preview"></a>Önizleme sırasında sınırlamalar

Şu anda bir DNS diğer adı aşağıdaki sınırlamalara sahiptir:

- *2 dakikaya kadar gecikme:* Bir DNS diğer adının güncellenmesi veya kaldırılması 2 dakikaya kadar sürer.
  - Kısa bir gecikmeden bağımsız olarak, diğer ad eski sunucuya yönelik istemci bağlantılarını hemen sonlandırır.
- *DNS araması:* Şimdilik, belirli bir DNS diğer adının ne olduğunu kontrol etmenin tek yetkili yolu, [DNS araması](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup)gerçekleştirmektedir.
- _Tablo denetimi desteklenmiyor:_ Bir veritabanında *tablo denetimi* etkinleştirilmiş BIR Azure SQL VERITABANı sunucusunda DNS diğer adı kullanamazsınız.
  - Tablo denetimi kullanım dışıdır.
  - [BLOB denetimine](sql-database-auditing.md)taşımanızı öneririz.

## <a name="related-resources"></a>İlgili kaynaklar

- Olağanüstü durum kurtarma dahil olmak üzere [Azure SQL veritabanı ile iş sürekliliği 'Ne genel bakış](sql-database-business-continuity.md).

## <a name="next-steps"></a>Sonraki adımlar

- [DNS diğer adı için PowerShell Azure SQL veritabanı](dns-alias-powershell.md)
