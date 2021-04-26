---
title: Blok Blobları için noktadan noktaya geri yükleme
titleSuffix: Azure Storage
description: Blok Blobları için belirli bir noktaya geri yükleme, bir depolama hesabını belirli bir zamanda önceki durumuna geri yüklemenize olanak tanıyarak yanlışlıkla silinmeye veya bozulmalara karşı koruma sağlar.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b959038753dd15282de357da746ef9b0e0cf2be5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802276"
---
# <a name="point-in-time-restore-for-block-blobs"></a>Blok Blobları için noktadan noktaya geri yükleme

Zaman içinde geri yükleme, Blok Blobu verilerini önceki bir duruma geri yüklemenize olanak tanıyarak yanlışlıkla silinmeye veya bozulmaya karşı koruma sağlar. Bir kullanıcının veya uygulamanın yanlışlıkla verileri sildiği veya bir uygulama hatasının verileri bozan senaryolarda, zaman içinde geri yükleme yararlı olur. Ayrıca, bir noktadan sonra geri yükleme, daha fazla testi çalıştırmadan önce bir veri kümesinin bilinen bir duruma geri döndürülmesi gereken test senaryolarına de olanak sağlar.

Yalnızca standart performans katmanındaki genel amaçlı v2 depolama hesapları için noktadan noktaya geri yükleme desteklenir. Yalnızca sık ve Seyrek Erişimli katmanlardaki veriler, zaman içinde geri yükleme ile geri yüklenebilir.

Bir depolama hesabı için noktadan noktaya geri yüklemeyi etkinleştirme hakkında bilgi edinmek için bkz. [Blok Blobu verilerinde bir zaman içindeki bir noktaya geri yükleme gerçekleştirme](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Zaman noktası geri yükleme nasıl çalışacaktır?

Noktadan noktaya geri yüklemeyi etkinleştirmek için, depolama hesabı için bir yönetim ilkesi oluşturup bir bekletme dönemi belirtirsiniz. Saklama süresi boyunca, blok bloblarını mevcut durumdan önceki bir zaman noktasına geri yükleyebilirsiniz.

Bir zaman noktası geri yüklemesi başlatmak için [BLOB aralıklarını geri yükleme](/rest/api/storagerp/storageaccounts/restoreblobranges) işlemini ÇAĞıRıN ve UTC zamanında bir geri yükleme noktası belirtin. Geri yüklemek için, kapsayıcı ve BLOB adlarının lexıgraf aralıklarını belirtebilir veya depolama hesabındaki tüm kapsayıcıları geri yüklemek için aralığı atlayabilirsiniz. Geri yükleme işlemi başına en fazla 10 lexıgraf aralığı desteklenir.

Azure depolama, istenen geri yükleme noktası arasındaki belirtilen bloblarda UTC saatine ve mevcut anda belirtilen tüm değişiklikleri analiz eder. Geri yükleme işlemi atomik olduğundan tüm değişiklikleri geri yükleme işleminde tamamen başarılı olur ya da başarısız olur. Geri yüklenemeyecek blob varsa, işlem başarısız olur ve etkilenen kapsayıcılar sürdürülmesine okuma ve yazma işlemleri devam ettirir.

Aşağıdaki diyagramda, noktadan noktaya geri yüklemenin nasıl çalıştığı gösterilmektedir. Bir veya daha fazla kapsayıcı veya blob aralığı *n* gün öncesine geri yüklendi; burada *n* , zaman içinde nokta geri yükleme için tanımlanan saklama süresinden küçüktür veya eşittir. Bu efekt, bekletme döneminde oluşan yazma ve silme işlemlerini döndürmektir.

:::image type="content" source="media/point-in-time-restore-overview/point-in-time-restore-diagram.png" alt-text="Son nokta, kapsayıcının önceki bir duruma nasıl geri yükleneceğini gösteren diyagram":::

Tek seferde bir depolama hesabında yalnızca bir geri yükleme işlemi çalıştırılabilir. Devam eden bir geri yükleme işlemi iptal edilemez, ancak ilk işlemi geri almak için ikinci bir geri yükleme işlemi gerçekleştirilebilir.

**BLOB aralıklarını geri yükleme** işlemi, işlemi benzersiz şekilde tanımlayan bir GERI yükleme kimliği döndürüyor. Bir zaman noktası geri yükleme işleminin durumunu denetlemek için, **BLOB aralıklarını geri** yükle işleminden döndürülen GERI yükleme kimliği Ile **geri yükleme durumunu Al** işlemini çağırın.

> [!IMPORTANT]
> Geri yükleme işlemi gerçekleştirdiğinizde, Azure depolama, işlem süresince geri yüklenen aralıklardaki bloblarda veri işlemlerini engeller. Birincil konumda okuma, yazma ve silme işlemleri engellenir. Bu nedenle, Azure portal kapsayıcıları gibi işlemler geri yükleme işlemi devam ederken beklendiği gibi gerçekleştirilemeyebilir.
>
> Depolama hesabı coğrafi olarak çoğaltılırsa, ikincil konumdaki okuma işlemleri geri yükleme işlemi sırasında devam edebilir.

> [!CAUTION]
> Zaman içinde geri yükleme, yalnızca blok Blobları üzerinde işlem yapan işlemlere karşı geri yüklemeyi destekler. Kapsayıcılar üzerinde işlem yapan işlemler geri yüklenemez. Örneğin, kapsayıcıyı [silme](/rest/api/storageservices/delete-container) işlemini çağırarak depolama hesabından bir kapsayıcıyı silerseniz, o kapsayıcı bir zaman noktası geri yükleme işlemiyle geri yüklenemez. Bir kapsayıcının tamamını silmek yerine, daha sonra geri yüklemek istiyorsanız ayrı Blobları silin.

### <a name="prerequisites-for-point-in-time-restore"></a>Zaman içinde nokta geri yükleme önkoşulları

Zaman içinde geri yükleme, zaman içinde nokta geri yüklemeyi etkinleştirebilmeniz için aşağıdaki Azure depolama özelliklerinin etkinleştirilmesini gerektirir:

- [Geçici silme](soft-delete-blob-overview.md)
- [Akışı Değiştir](storage-blob-change-feed.md)
- [Blob sürümü oluşturma](versioning-overview.md)

Bu özelliklerin etkinleştirilmesi ek ücretler oluşmasına neden olabilir. Güncel nokta geri yükleme ve önkoşul özelliklerini etkinleştirmeden önce faturalandırma etkilerini anladığınızdan emin olun.

### <a name="retention-period-for-point-in-time-restore"></a>Zaman içinde bir noktaya geri yükleme için bekletme süresi

Bir depolama hesabı için noktadan noktaya geri yüklemeyi etkinleştirdiğinizde bir bekletme dönemi belirtirsiniz. Depolama hesabınızdaki blok Blobları Bekletme dönemi sırasında geri yüklenebilir.

Süre sonu geri yüklemeyi etkinleştirdikten sonra Bekletme dönemi birkaç dakika sonra başlar. Blob 'ları bekletme döneminin başlangıcından önceki bir duruma geri yükleyemezsiniz. Örneğin, 1 Mayıs 'ta 30 günlük bir bekletme tarihi ile bir süre sonra geri yükleme etkinleştirdiyseniz, 15 Mayıs 'ta en fazla 15 güne geri yükleme yapabilirsiniz. 1 Haziran 'da verileri 1 ila 30 gün içinde geri yükleyebilirsiniz.

Belirli bir noktaya geri yükleme için bekletme süresi, geçici silme için belirtilen saklama süresinden en az bir gün daha az olmalıdır. Örneğin, geçici silme Bekletme dönemi 7 gün olarak ayarlandıysa, zaman içinde geri yükleme saklama süresi 1 ile 6 gün arasında olabilir.

> [!IMPORTANT]
> Bir veri kümesini geri yüklemek için geçen süre, geri yükleme döneminde yapılan yazma ve silme işlemlerinin sayısına bağlıdır. Örneğin, 1.000.000 nesne başına 3.000 nesne ve gün başına silinen 1.000 olan bir hesap, geçmişte 30 günlük bir noktaya geri yüklemek için yaklaşık iki saat gerektirir. Geçmişte 90 günden fazla bir bekletme dönemi ve geri yükleme bu değişiklik oranına sahip bir hesap için önerilmez.

### <a name="permissions-for-point-in-time-restore"></a>Noktadan noktaya geri yükleme izinleri

Geri yükleme işlemini başlatmak için, bir istemcinin depolama hesabındaki tüm kapsayıcılar için yazma izinlerine sahip olması gerekir. Azure Active Directory (Azure AD) ile bir geri yükleme işlemini yetkilendirmek için izin vermek üzere depolama hesabı, kaynak grubu veya abonelik düzeyinde güvenlik sorumlusuna **depolama hesabı katılımcısı** rolünü atayın.

## <a name="limitations-and-known-issues"></a>Sınırlamalar ve bilinen sorunlar

Blok Blobları için bir noktadan noktaya geri yükleme aşağıdaki sınırlamalara ve bilinen sorunlara sahiptir:

- Yalnızca standart bir genel amaçlı v2 depolama hesabındaki blok Bloblar, bir zaman noktası geri yükleme işleminin parçası olarak geri yüklenebilir. Ekleme Blobları, sayfa Blobları ve Premium blok Blobları geri yüklenmez. 
- Saklama döneminde bir kapsayıcıyı sildiyseniz, bu kapsayıcı, zaman içinde geri yükleme işlemi ile geri yüklenmez. Silinen bir kapsayıcıda blob 'ları içeren bir dizi blobu geri yüklemeye çalışırsanız, zaman içinde geri yükleme işlemi başarısız olur. Kapsayıcıları silinmeye karşı koruma hakkında bilgi edinmek için bkz. [kapsayıcılar Için geçici silme (Önizleme)](soft-delete-container-overview.md).
- Bir blob, mevcut bir süre ve geri yükleme noktası arasındaki dönemdeki sık ve seyrek katmanlar arasında taşınırsa, blob önceki katmanına geri yüklenir. Arşiv katmanındaki blok bloblarının geri yüklenmesi desteklenmiyor. Örneğin, sık erişim katmanındaki bir blob iki gün önce arşiv katmanına taşınmışsa ve geri yükleme işlemi üç gün önceki bir noktaya geri yüklüyorsa blob sık erişim katmanına geri yüklenmez. Arşivlenmiş bir blobu geri yüklemek için önce Arşiv katmanının dışına taşıyın. Daha fazla bilgi için bkz. [Arşiv katmanından blob verilerini yeniden doldurma](storage-blob-rehydration.md).
- [URL 'den](/rest/api/storageservices/put-block-from-url) [PUT bloğu veya](/rest/api/storageservices/put-block) put bloğu aracılığıyla karşıya yüklenen ancak [PUT blok listesi](/rest/api/storageservices/put-block-list)aracılığıyla kaydedilmemiş bir blok, blob 'un bir parçası değildir ve bu nedenle geri yükleme işleminin bir parçası olarak geri yüklenmez.
- Etkin kiralamaya sahip bir blob geri yüklenemez. Etkin bir kiralamaya sahip bir blob geri yüklenecek blob aralığına dahil edilmezse geri yükleme işlemi otomatik olarak başarısız olur. Geri yükleme işlemini başlatmadan önce tüm etkin kiraları kesin.
- Anlık görüntüler, geri yükleme işleminin bir parçası olarak oluşturulmaz veya silinmez. Yalnızca temel blob önceki durumuna geri yüklendi.
- Azure Data Lake Storage 2. düz ve hiyerarşik ad alanlarını geri yükleme desteklenmez.

> [!IMPORTANT]
> Blok bloblarını 22 Eylül 2020 ' den önceki bir noktaya geri yüklerseniz, zaman içinde nokta geri yükleme için Önizleme sınırlamaları geçerli olur. Microsoft, genel kullanıma açık zaman noktası geri yükleme özelliğinden yararlanmak için 22 Eylül 2020 ' den veya sonraki bir sürüme eşit olan bir geri yükleme noktası seçmenizi önerir.

## <a name="pricing-and-billing"></a>Fiyatlandırma ve Faturalama

Zaman içinde geri yüklemeyi etkinleştirmek için ücret alınmaz. Ancak, zaman içinde geri yüklemeyi etkinleştirmek, blob sürümü oluşturma, geçici silme ve değişiklik akışını, bunların her biri ek ücretlere neden olabilecek şekilde etkinleştirir.

Bir noktadan noktaya geri yükleme için faturalandırma, geri yükleme işlemini gerçekleştirmek için işlenen veri miktarına bağlıdır. İşlenen veri miktarı, geri yükleme noktası ve var olan süre arasında gerçekleşen değişiklik sayısına göre belirlenir. Örneğin, bir depolama hesabındaki blob verilerini engellemek için görece sabit bir değişiklik oranının olduğunu varsayarsak, 1 gün içinde geri kalan geri yükleme işlemi 10 gün sonra geri kalan bir geri yükleme maliyeti 1/10 ' dur.

Geri yükleme işleminin maliyetini tahmin etmek için, geri yükleme döneminde değiştirilen veri miktarını tahmin etmek üzere değişiklik akışı günlüğünü gözden geçirin. Örneğin, değişiklik akışı için bekletme süresi 30 gün ise ve değişiklik akışı boyutu 10 MB ise, daha önce bir noktaya geri yükleme, o bölgedeki bir LRS hesabı için listelenen fiyatın yaklaşık bir üçte biri üzerinden ücretlendirilir. Daha önce 27 gün olan bir noktaya geri yükleme, listelenen fiyatın yaklaşık dokuz-onmalinin maliyetlidir.

Zaman içinde nokta geri yükleme fiyatlandırması hakkında daha fazla bilgi için bkz. [Blok Blobu fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="next-steps"></a>Sonraki adımlar

- [Blok Blobu verilerinde bir zaman içinde geri yükleme gerçekleştirin](point-in-time-restore-manage.md)
- [Azure Blob depolamada akış desteğini değiştirme](storage-blob-change-feed.md)
- [Bloblar için geçici silmeyi etkinleştirme](./soft-delete-blob-enable.md)
- [Blob sürüm oluşturmayı etkinleştirme ve yönetme](versioning-enable.md)
