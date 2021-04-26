---
title: Geçici bağlantı hataları-MariaDB için Azure veritabanı
description: MariaDB için Azure veritabanı 'nın geçici bağlantı hatalarını nasıl işleyeceğinizi öğrenin.
keywords: MySQL bağlantısı, bağlantı dizesi, bağlantı sorunları, geçici hata, bağlantı hatası
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2a651d87411654f1a52c4a097f115ba848ce569c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660047"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı geçici bağlantı hatalarını işleme

Bu makalede, MariaDB için Azure veritabanı 'na bağlanan geçici hataların nasıl işleneceği açıklanır.

## <a name="transient-errors"></a>Geçici hatalar

Geçici arıza olarak da bilinen geçici bir hata, kendisini çözecek bir hatadır. Genellikle bu hatalar, bırakılmakta olan veritabanı sunucusuna bağlantı olarak bildirimde yer vardır. Ayrıca sunucuya yeni bağlantılar açılamaz. Geçici hatalar, örneğin, donanım veya ağ hatası oluştuğunda meydana gelebilir. Başka bir nedenden dolayı PaaS hizmetinin kullanıma alınmış yeni bir sürümü olabilir. Bu olayların çoğu, sistem tarafından 60 saniyeden daha az bir süre içinde otomatik olarak azalır. Bulutta uygulama tasarlamaya ve geliştirmeye yönelik en iyi yöntem geçici hataların beklenmektedir. Her zaman herhangi bir bileşende ve bu durumları işlemek için uygun mantığın yerine gelebileceğini varsayın.

## <a name="handling-transient-errors"></a>Geçici hataları işleme

Geçici hatalar, yeniden deneme mantığı kullanılarak işlenmelidir. Göz önünde bulundurulması gereken durumlar:

* Bir bağlantıyı açmaya çalıştığınızda bir hata oluşur
* Sunucu tarafında boştaki bir bağlantı bırakılır. Bir komut verilmenize çalıştığınızda yürütülemiyor
* Şu anda bir komutun yürütüldüğü etkin bir bağlantı bırakılır.

İlk ve ikinci durum, işlemek için oldukça ileri bir işlemdir. Bağlantıyı tekrar açmayı deneyin. Başarılı olduğunuzda, geçici hata sistem tarafından azaltılmıştır. MariaDB için Azure veritabanınızı yeniden kullanabilirsiniz. Bağlantıyı yeniden denemeden önce beklemeleri önerilir. İlk yeniden denemeler başarısız olursa geri dönün. Bu şekilde, sistem hata durumu aşmak için kullanılabilen tüm kaynakları kullanabilir. İzlenecek iyi bir örüntü şunlardır:

* İlk yeniden denemeden önce 5 saniye bekleyin.
* Aşağıdaki her yeniden denemede, bekleme süresini 60 saniyeye kadar artırın.
* Uygulamanızın işlemi başarısız olduğu noktada en fazla yeniden deneme sayısını ayarlayın.

Etkin bir işlem başarısız olursa, kurtarmanın doğru şekilde işlenmesi daha zordur. İki durum vardır: işlem, doğası halinde salt okunurdur, bağlantıyı yeniden açmak ve işlemi yeniden denemek güvenlidir. Ancak işlem veritabanına yazıyorsanız, işlemin geri alınması veya geçici hata yapılmadan önce başarılı olup olmadığını belirlemelisiniz. Bu durumda, veritabanı sunucusundan kayıt onayını almamış olabilirsiniz.

Bunu yapmanın bir yolu, istemci üzerinde tüm yeniden denemeler için kullanılan benzersiz bir KIMLIK oluşturmak içindir. Bu benzersiz KIMLIĞI işlemin bir parçası olarak sunucuya geçirin ve benzersiz bir kısıtlamaya sahip bir sütunda saklayın. Bu şekilde işlemi güvenle yeniden deneyebilirsiniz. Önceki işlem geri alınırsa ve istemci tarafından oluşturulan benzersiz KIMLIK henüz sistemde yoksa başarılı olur. Önceki işlem başarıyla tamamlandığından, benzersiz KIMLIK daha önce depolanıyorsa yinelenen bir anahtar ihlaline işaret eder.

Programınız, üçüncü taraf ara yazılım aracılığıyla MariaDB için Azure veritabanı ile iletişim kurduğunda, satıcıdan geçici hatalar için yeniden deneme mantığı içerip içermediğini öğrenin.

Yeniden deneme mantığını test ettiğinizden emin olun. Örneğin, MariaDB sunucusu için Azure veritabanı 'nın işlem kaynaklarını ölçeklendirirken veya azaltılarken kodunuzu yürütmeyi deneyin. Uygulamanız herhangi bir sorun olmadan bu işlem sırasında karşılaşılan kısa kapalı kalma süresini işlemelidir.

## <a name="next-steps"></a>Sonraki adımlar

* [MariaDB için Azure Veritabanı bağlantı sorunlarını giderme](howto-troubleshoot-common-connection-issues.md)
