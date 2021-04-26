---
title: MongoDB hesabı için Azure Cosmos DB API 'nizin Mongo sürümünü yükseltin
description: MongoDB için mevcut Azure Cosmos DB API 'SI için MongoDB tel protokolü sürümünü sorunsuz şekilde yükseltme
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: chrande
ms.openlocfilehash: 8865a16c2840b65f432de679c6dd63b285b1f760
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771845"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>MongoDB hesabı için Azure Cosmos DB API 'nizin API sürümünü yükseltme
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Bu makalede, MongoDB hesabı için Azure Cosmos DB API 'sinin API sürümünün nasıl yükseltileceği açıklanır. Yükseltmeden sonra, MongoDB için Azure Cosmos DB API 'sindeki en son işlevselliği kullanabilirsiniz. Yükseltme işlemi, hesabınızın kullanılabilirliğini kesintiye uğramaz ve herhangi bir noktada, her zaman veritabanı kapasitesini azaltır. Bu işlemden, mevcut veri veya dizinlerin hiçbiri etkilenmeyecektir. 

Yeni bir API sürümüne yükseltirken, üretim iş yüklerini yükseltmeden önce geliştirme ve test iş yükleri ile başlayın. MongoDB hesabı için Azure Cosmos DB API 'nizi yükseltmeden önce istemcilerinizi, yükseltmekte olduğunuz API sürümüyle uyumlu bir sürüme yükseltmeniz önemlidir.

>[!Note]
> Şu anda yalnızca sunucu sürümü 3,2 ' i kullanan hesaplar 3,6 veya 4,0 sürümüne yükseltilebilir. Hesabınız yükseltme seçeneğini göstermezse lütfen [bir destek bileti dosyası](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)yapın.

## <a name="upgrading-to-40-or-36"></a>4\.0 veya 3.6'ya yükseltme

### <a name="benefits-of-upgrading-to-version-40"></a>Sürüm 4.0’a yükseltmenin avantajları

Sürüm 4,0 ' de bulunan yeni özellikler aşağıda verilmiştir:
- Parçalara ayrılmamış koleksiyonlar içinde çok belgeli işlemler için destek.
- Yeni toplama işleçleri
- Geliştirilmiş tarama performansı
- Daha hızlı, daha verimli depolama

### <a name="benefits-of-upgrading-to-version-36"></a>3\.6 sürümüne yükseltmenin avantajları

Sürüm 3,6 ' de bulunan yeni özellikler aşağıda verilmiştir:
- Gelişmiş performans ve kararlılık
- Yeni veritabanı komutları desteği
- Varsayılan ve yeni toplama aşamalarına göre toplama işlem hattı desteği
- Değişiklik Akışları Desteği
- Bileşik Dizin desteği
- Şu işlemler için bölümler arası destek: güncelleştirme, silme, sayma ve sıralama
- Şu toplama işlemleri için gelişmiş performans: $count, $skip, $limit ve $group
- Joker karakter dizinleme artık destekleniyor

### <a name="changes-from-version-32"></a>Sürüm 3.2'ye göre değişiklikler

- Varsayılan olarak [Sunucu Tarafında Yeniden Deneme (SSR)](prevent-rate-limiting-errors.md) özelliği etkinleştirilir; böylelikle istemci uygulamasından gelen istekler 16500 hataları döndürmez. Bunun yerine istekler tamamlanana veya 60 saniyelik zaman aşımı oluşana kadar sürdürülür.
- İstek başına zaman aşımı 60 saniye olarak ayarlanmıştır.
- Yeni kablo protokol sürümünde oluşturulan MongoDB koleksiyonlarında, varsayılan olarak yalnızca `_id` özelliği dizine alınır.

### <a name="action-required-when-upgrading-from-32"></a>3\.2'den yükseltme yapılırken gereken eylem

3\.2 sürümünden yükseltme yaparken veritabanı hesabı uç nokta son eki şu biçime güncelleştirilir:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Sürüm 3,2 ' den yükseltiyorsanız, bu veritabanı hesabıyla bağlanan uygulamalarınızda ve sürücülerinizin mevcut uç noktasını değiştirmeniz gerekir. **Yalnızca yeni uç noktayı kullanan bağlantılar yeni API sürümündeki özelliklere erişebilir**. Önceki 3.2 uç noktasının `.documents.azure.com` soneki olmalıdır.

>[!Note]
> Hesabınız bir Sogeign, kamu veya sınırlı Azure bulutu 'nda oluşturulduysa, bu uç nokta hafif farklılıklara sahip olabilir.

## <a name="how-to-upgrade"></a>Yükseltme

1. Azure portal oturum açın [.](https://portal.azure.com/)

1. MongoDB hesabı için Azure Cosmos DB API 'nize gidin. **Genel bakış** bölmesini açın ve geçerli **sunucu sürümünüzün** 3,2 ya da 3,6 olduğunu doğrulayın.

    :::image type="content" source="./media/mongodb-version-upgrade/check-current-version.png" alt-text="Azure portal MongoDB hesabınızın güncel sürümünü denetleyin." border="true":::

1. Sol menüden `Features` bölmeyi açın. Bu bölmede, veritabanı hesabınız için kullanılabilen hesap düzeyi özellikleri gösterilir.

1. `Upgrade MongoDB server version` satırını seçin. Bu seçeneği görmüyorsanız, hesabınız bu yükseltme için uygun olmayabilir. Bu durumda, lütfen [bir destek bileti](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) dosyası sağlayın.

    :::image type="content" source="./media/mongodb-version-upgrade/upgrade-server-version.png" alt-text="Özellikler dikey penceresini açın ve hesabınızı yükseltin." border="true":::

1. Yükseltme hakkında görüntülenen bilgileri gözden geçirin. `Set server version to 4.0`(Veya geçerli sürümünüze bağlı olarak 3,6) seçeneğini belirleyin.

    :::image type="content" source="./media/mongodb-version-upgrade/select-upgrade.png" alt-text="Yükseltme kılavuzunu gözden geçirin ve Yükselt ' i seçin." border="true":::

1. Yükseltmeyi başlattıktan sonra **özellik** menüsü gri renkte olur ve durum *Beklemede* olarak ayarlanır. Yükseltmenin tamamlanabilmesi 15 dakika sürer. Bu işlem, veritabanı hesabınızın mevcut işlevlerini veya işlemlerini etkilemez. Bu işlem tamamlandıktan sonra **MongoDB sunucu sürümü durumunu güncelleştir** yükseltilen sürümü gösterir. İsteğiniz işlenirken bir sorun oluşursa lütfen [desteğe başvurun](https://azure.microsoft.com/en-us/support/create-ticket/) .

1. Aşağıda, hesabınızı yükselttikten sonraki bazı noktalar verilmiştir:

    1. 3,2 ' den yükselttiyseniz **genel bakış** bölmesine dönün ve uygulamanızda kullanmak üzere yeni bağlantı dizesini kopyalayın. 3\.2'yi çalıştıran eski bağlantı dizesi kesintiye uğramaz. Tutarlı bir deneyim sağlamak için tüm uygulamalarınız yeni uç noktayı kullanmalıdır.

    1. 3\.6'dan yükseltme yaptıysanız mevcut bağlantı dizeniz belirtilen sürüme yükseltilir ve kullanılmaya devam edilmelidir.

## <a name="how-to-downgrade"></a>Eski sürüme düşürme

Ayrıca, ' yükseltme yöntemi ' bölümündeki adımlar aracılığıyla hesabınızı 4,0 ' den 3,6 ' e indirgeyede indirebilirsiniz.

3,2 'den (4,0 veya 3,6) sürümüne yükselttiyseniz ve 3,2 sürümüne geri dönmek istiyorsanız, önceki (3,2) Bağlantı dizenizi kullanarak `accountname.documents.azure.com` Etkin yükseltme sonrası sürümü olan ana bilgisayar ile yeniden geçmeniz yeterlidir.

## <a name="next-steps"></a>Sonraki adımlar

- [MongoDB sürüm 4.0'ın desteklenen ve desteklenmeyen özellikleri](mongodb-feature-support-40.md) hakkında bilgi edinin.
- [MongoDB sürüm 3.6'nın desteklenen ve desteklenmeyen özellikleri](mongodb-feature-support-36.md) hakkında bilgi edinin.
- Daha fazla bilgi için [Mongo 3.6 sürümü özelliklerini](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/) gözden geçirin
