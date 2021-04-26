---
title: 'Azure AD Connect: LocalDB 10 GB sınırından kurtarma sorunu | Microsoft Docs'
description: Bu konuda, LocalDB 10 GB sınır sorunuyla karşılaştığında Azure AD Connect eşitleme hizmetinin nasıl kurtarılacağı açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e10aa5d96722b414d7384ceb81f393575d57e2a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688782"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: LocalDB 10 GB sınırından kurtarma
Azure AD Connect’e kimlik verilerini depolamak için bir SQL Server veritabanı gerekiyor. Azure AD Connect ile yüklenen varsayılan SQL Server 2012 Express LocalDB’yi kullanabileceğiniz gibi, kendi tam SQL’nizi de kullanabilirsiniz. SQL Server Express 10 GB boyut sınırını uygular. LocalDB’yi kullanırken bu sınıra ulaşıldığında, Azure AD Connect Eşitleme Hizmeti artık düzgün başlatılamaz veya eşitleme yapamaz. Bu makalede kurtarma adımları sağlanmaktadır.

## <a name="symptoms"></a>Belirtiler
İki genel belirti vardır:

* Azure AD Connect eşitleme hizmeti **çalışıyor** ancak *"durdurulan-Database-disk-Full"* hatası ile eşitlenemiyor.

* Azure AD Connect eşitleme hizmeti **başlatılamıyor**. Hizmeti başlatmaya çalıştığınızda, olay 6323 ile başarısız olur ve *"sunucu bir hatayla karşılaştı, çünkü SQL Server disk alanı yetersiz."*

## <a name="short-term-recovery-steps"></a>Kısa vadeli kurtarma adımları
Bu bölümde, Azure AD Connect eşitleme hizmeti 'nin işlemi sürdürmesini sağlamak için gereken VERITABANı alanını geri kazanma adımları sağlanmaktadır. Adımlar şunlardır:
1. [Eşitleme hizmeti durumunu belirleme](#determine-the-synchronization-service-status)
2. [Veritabanını Küçült](#shrink-the-database)
3. [Çalışma geçmişi verilerini sil](#delete-run-history-data)
4. [Çalışma geçmişi verileri için saklama süresini kısaltın](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Eşitleme hizmeti durumunu belirleme
İlk olarak, Eşitleme hizmetinin hala çalışır durumda olup olmadığını belirleme:

1. Azure AD Connect sunucunuzda yönetici olarak oturum açın.

2. **Service Control Manager**'a gidin.

3. **Microsoft Azure AD eşitleme** durumunu denetleyin.


4. Çalışıyorsa, hizmeti durdurmayın veya yeniden başlatmayın. Atla [veritabanı adımını daraltın](#shrink-the-database) ve [çalışma geçmişi verilerini sil](#delete-run-history-data) adımına gidin.

5. Çalışmıyorsa, hizmeti başlatmayı deneyin. Hizmet başarıyla başlatılırsa [veritabanı adımını daraltın](#shrink-the-database) ve [çalışma geçmişi verilerini sil](#delete-run-history-data) adımını atlayın. Aksi takdirde, [veritabanı adımını küçültmeye](#shrink-the-database) devam edin.

### <a name="shrink-the-database"></a>Veritabanını Küçült
Eşitleme hizmetini başlatmak için yeterli VERITABANı alanı boşaltmak üzere küçültme işlemini kullanın. Veritabanındaki boşluklar kaldırılarak DB alanını boşaltır. Alanı her zaman kurtarabileceğiniz garanti edilmediği için bu adım en iyi çabadır. Küçültme işlemi hakkında daha fazla bilgi edinmek için [bir veritabanını küçülttüğünüzde](/sql/relational-databases/databases/shrink-a-database)bu makaleyi okuyun.

> [!IMPORTANT]
> Eşitleme hizmetini çalıştırmak için bu adımı atlayın. Daha fazla parçalanma nedeniyle zayıf performansa yol açacağından, SQL DB 'nin küçültülmesi önerilmez.

Azure AD Connect için oluşturulan veritabanının adı **ADSync**. Bir küçültme işlemi gerçekleştirmek için, veritabanında sysadmin veya DBO olarak oturum açmalısınız. Azure AD Connect yükleme sırasında, aşağıdaki hesaplara sysadmin hakları verilir:
* Yerel Yöneticiler
* Azure AD Connect yüklemeyi çalıştırmak için kullanılan Kullanıcı hesabı.
* Azure AD Connect eşitleme hizmetinin işletim bağlamı olarak kullanılan eşitleme hizmeti hesabı.
* Yükleme sırasında oluşturulan yerel grup ADSyncAdmins.

1. ' In altında bulunan **ADSync. mdf** ve **ADSync_log. ldf** dosyalarını `%ProgramFiles%\Microsoft Azure AD Sync\Data` güvenli bir konuma kopyalayarak veritabanını yedekleyin.

2. Yeni bir PowerShell oturumu başlatın.

3. Klasöre gidin `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn` .

4.  `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>` Bir sysadmin KIMLIK bilgisini veya dbo veritabanını kullanarak sqlcmd yardımcı programını çalıştırın.

5. Veritabanını küçültmek için, sqlcmd isteminde ( `1>` ), `DBCC Shrinkdatabase(ADSync,1);` ardından bir `GO` sonraki satıra yazın.

6. İşlem başarılı olursa, eşitleme hizmetini yeniden başlatmayı deneyin. Eşitleme hizmetini başlatabiliyorsanız, [çalışma geçmişi verilerini sil](#delete-run-history-data) adımına gidin. Aksi takdirde, desteğe başvurun.

### <a name="delete-run-history-data"></a>Çalışma geçmişi verilerini sil
Varsayılan olarak, Azure AD Connect en fazla yedi gün, çalışma geçmişi verilerini korur. Bu adımda, Azure AD Connect eşitleme hizmetinin yeniden eşitlemeye başlayabilmesi için, VERITABANı alanını geri kazanmak üzere çalışma geçmişi verilerini silebiliyoruz.

1. → Eşitleme hizmeti ' ne giderek **Synchronization Service Manager** başlatın.

2. **İşlemler** sekmesine gidin.

3. **Eylemler** altında **çalıştırmaları temizle**... seçeneğini belirleyin.

4. **Tümünü Temizle** veya **... \<date> seçeneğini yapmadan önce çalıştırmaları** Temizle seçeneğini belirleyebilirsiniz. İki günden eski olan çalıştırma geçmişi verilerini temizleyerek başlatmanız önerilir. DB boyutu sorunuyla çalışmaya devam ederseniz, **Tüm çalıştırmaları temizle** seçeneğini belirleyin.

### <a name="shorten-retention-period-for-run-history-data"></a>Çalışma geçmişi verileri için saklama süresini kısaltın
Bu adım, birden çok eşitleme döngüsü sonrasında 10 GB 'lik sınır sorunuyla çalışma olasılığını azaltmaktır.

1. Yeni bir PowerShell oturumu açın.

2. `Get-ADSyncScheduler`' İ çalıştırın ve geçerli saklama süresini belirten Purgerunizınterval özelliğini bir yere göz atın.

3. `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00`Saklama süresini iki güne ayarlamak için ' i çalıştırın. Saklama süresini uygun şekilde ayarlayın.

## <a name="long-term-solution--migrate-to-full-sql"></a>Uzun vadeli çözüm – tam SQL 'e geçiş
Genellikle, sorun, şirket içi Active Directory Azure AD 'ye Azure AD Connect için 10 GB veritabanı boyutunun artık yeterli olmadığını işaret eder. SQL Server 'ın tam sürümünü kullanarak geçmeniz önerilir. Mevcut Azure AD Connect dağıtımının LocalDB’sini doğrudan tüm SQL sürümünün veritabanıyla değiştiremezsiniz. Bunun yerine, tam SQL sürümü içeren yeni bir Azure AD Connect sunucusu dağıtmanız gerekir. Yeni Azure AD Connect sunucusunun (SQL DB ile), mevcut Azure AD Connect sunucusunun (LocalDB ile) yanında hazırlık sunucusu olarak dağıtıldığı durumlarda, Swing geçişi yapmanız önerilir. 
* Azure AD Connect ile uzak SQL’i yapılandırma yönergeleri için, [Azure AD Connect özel yüklemesi](./how-to-connect-install-custom.md) makalesine bakın.
* Azure AD Connect yükseltmesinde Swing geçişi için, [Azure AD Connect: Önceki bir sürümden en son sürümü yükseltme](./how-to-upgrade-previous-version.md#swing-migration) makalesine bakın.

## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
