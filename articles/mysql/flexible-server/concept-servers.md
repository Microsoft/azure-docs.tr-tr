---
title: Sunucu kavramları-MySQL için Azure veritabanı esnek sunucu
description: Bu konuda, MySQL için Azure veritabanı esnek sunucusu ile çalışmaya yönelik konular ve yönergeler sağlanmaktadır
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: a7978410dbe28a5da5dae81cb380d118fe13a159
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869387"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>MySQL için Azure veritabanı esnek sunucusu 'nda sunucu kavramları (Önizleme)

> [!IMPORTANT] 
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Bu makalede, MySQL için Azure veritabanı esnek sunucularıyla çalışmaya yönelik konular ve yönergeler sunulmaktadır.

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>MySQL için Azure veritabanı esnek sunucusu nedir?

MySQL için Azure veritabanı esnek sunucu, MySQL 'in topluluk sürümünü çalıştıran, tam olarak yönetilen bir veritabanı hizmetidir. Genel olarak, hizmet kullanıcı gereksinimlerine göre esneklik ve yapılandırma özelleştirmeleri sağlamak üzere tasarlanmıştır. Bu, şirket içi dünyada bildiğiniz aynı MySQL sunucu yapısıdır. Özellikle, esnek sunucu yönetilir, kullanıma hazır performans, daha iyi sunucu yönetilebilirliği ve denetimi sağlar ve sunucu düzeyinde erişim ve özellikler sunar.

MySQL için Azure veritabanı esnek sunucusu:

- Bir Azure aboneliği içinde oluşturulur.
- , Veritabanları için üst kaynaktır.
- Sunucu parametreleri aracılığıyla sunulan MySQL yapılandırmasına izin verir (sunucu parametresi kavramlarına bağlantı).
- Otomatik yedeklemeleri gerçekleştirir ve zaman içinde nokta geri yükleme destekler.
- Veritabanları için bir ad alanı sağlar.
- Güçlü yaşam süresi semantiğinin bulunduğu bir kapsayıcıdır; bir sunucuyu silin ve kapsanan veritabanlarını siler.
- Bir bölgedeki kaynakları birlikte bulur.
- Müşteri tarafından sağlanmış sunucu bakım zamanlaması desteği
- Esnek sunucuları, yüksek oranda kullanılabilirlik için yedekli bir kurulumda dağıtma olanağı
- Veritabanı sunucusu erişimi için bir sanal ağ tümleştirmesi sağlar
- , Kullanımda olmadığında esnek sunucuyu duraklatarak maliyetleri kaydetme yolu sağlar
- Veritabanlarına uygulanan yönetim ilkeleri için kapsam sağlar: oturum açma, güvenlik duvarı, kullanıcılar, roller, konfigürasyonlar vb.
- Ana sürüm MySQL 5,7 ve MySQL 8,0 ' i destekler. Daha fazla bilgi için bkz. [MySQL Için Azure veritabanı altyapısı sürümleri](./../concepts-supported-versions.md).

MySQL için Azure veritabanı esnek sunucusunda bir veya daha fazla veritabanı oluşturabilirsiniz. Tüm kaynakları kullanmak veya kaynakları paylaşmak için birden çok veritabanı oluşturmak üzere sunucu başına tek bir veritabanı oluşturmayı tercih edebilirsiniz. Fiyatlandırma, işlem katmanının yapılandırması, sanal çekirdekler ve depolama alanı (GB) temelinde sunucu başına yapılandırılır. Daha fazla bilgi için bkz. [işlem ve depolama](./concepts-compute-storage.md).

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>MySQL için Azure veritabanı esnek sunucusunu Durdur/Başlat

MySQL için Azure veritabanı esnek sunucu, kullanımda olmadığında sunucuyu **durdurma** ve etkinlik sürdürüleceği zaman sunucuyu **başlatma** olanakları sağlar. Bu aslında, veritabanı sunucularında maliyetleri kaydetmek ve yalnızca kullanımda olduğunda kaynak için ödeme yapmak amacıyla yapılır. Bu, geliştirme ve test iş yükleri için ve yalnızca günün bir parçası için sunucuyu kullanırken daha da önemli olur. Sunucuyu durdurduğunuzda, tüm etkin bağlantılar bırakılır. Daha sonra, sunucuyu yeniden çevrimiçi duruma getirmek istediğinizde [Azure Portal](how-to-stop-start-server-portal.md) veya CLI kullanabilirsiniz.

Sunucu **durdurulmuş** durumdaysa, sunucunun işlem faturalandırılmaz. Ancak, sunucu yeniden başlatıldığında veri dosyalarının kullanılabilir olduğundan emin olmak için, depolama alanı faturalandırılmaya devam eder.

> [!IMPORTANT]
> Sunucuyu **durdurduğunuzda** bir esnette sonraki 7 gün için bu durumda kalır. Bu süre içinde el ile **başlatmadıysanız** , sunucu otomatik olarak 7 gün sonunda başlatılır. Sunucuyu kullanmıyorsanız yeniden **durdurmayı** seçebilirsiniz.

Sunucu durdurulduğunda, sunucuda hiçbir yönetim işlemi gerçekleştirilemez. Sunucudaki yapılandırma ayarlarını değiştirmek için [sunucusunu başlatmanız](how-to-stop-start-server-portal.md)gerekir. [Durma/başlatma kısıtlamalarına](./concepts-limitations.md#stopstart-operation)bakın.

## <a name="how-do-i-manage-a-server"></a>Sunucu Nasıl yaparım? mi?

[Azure Portal](./quickstart-create-server-portal.md) veya [Azure CLI](./quickstart-create-server-cli.md)kullanarak oluşturma, silme, sunucu parametre yapılandırma (My. CNF), ölçeklendirme, ağ, güvenlik, yüksek kullanılabilirlik, yedekleme & geri yükleme, MySQL Için Azure veritabanı esnek sunucusunu izleme işlemini yönetebilirsiniz. Ayrıca, bu saklı yordamlar, MySQL için Azure veritabanı 'nda, süper kullanıcı ayrıcalığı sunucu üzerinde desteklenmediğinden bazı veritabanı yönetim görevlerini gerçekleştirmek için kullanılabilir.

|**Saklı yordam adı**|**Giriş parametreleri**|**Çıkış parametreleri**|**Kullanım notunun**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|Yok|Komuta eşdeğerdir [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) . , Bağlantının yürütüldüğü tüm deyimleri sonlandırdıktan sonra, belirtilen processlist_id ilişkili bağlantıyı sonlandırır.|
|*mysql.az_kill_query*|processlist_id|Yok|Komuta eşdeğerdir [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) . Bağlantı şu anda yürütülmekte olan ifadeyi sonlandırır. Bağlantıyı canlı bırakır.|
|*mysql.az_load_timezone*|Yok|Yok|Parametrenin adlandırılmış değerlere (örn.) ayarlanbilmesini sağlamak için [saat dilimi tablolarını](../howto-server-parameters.md#working-with-the-time-zone-parameter) yükler `time_zone` . "ABD/Pasifik").|


## <a name="next-steps"></a>Sonraki adımlar

-   [Sunucu oluşturma](./quickstart-create-server-portal.md) hakkında bilgi edinin
-   [İzleme ve uyarılar](./how-to-alert-on-metric.md) hakkında bilgi edinin

