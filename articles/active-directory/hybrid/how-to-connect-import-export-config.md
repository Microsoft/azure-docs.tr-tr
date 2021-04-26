---
title: Azure AD Connect yapılandırma ayarlarını içeri ve dışarı aktarma
description: Bu makalede, bulut sağlaması için sık sorulan sorular açıklanmaktadır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e2bdaa2c7a7648124fbe0be60e5a0af2f83238f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226570"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings"></a>Azure AD Connect yapılandırma ayarlarını içeri ve dışarı aktarma 

Azure Active Directory (Azure AD) Connect dağıtımları, tek bir orman hızlı mod yüklemesinden, özel eşitleme kuralları kullanılarak birden çok orman genelinde eşitleme yapan karmaşık dağıtımlara farklılık gösterir. Çok sayıda yapılandırma seçeneği ve mekanizması nedeniyle, hangi ayarların geçerli olduğunu anlamak ve aynı yapılandırmaya sahip bir sunucuyu hızlı bir şekilde dağıtmak için gereklidir. Bu özellik, belirli bir eşitleme sunucusunun yapılandırmasını katalog haline getirme ve ayarları yeni bir dağıtıma aktarma özelliğini sunar. Farklı eşitleme ayarları anlık görüntüleri, iki sunucu veya zaman içinde aynı sunucu arasındaki farkları kolayca görselleştirebileceğiniz şekilde karşılaştırılabilir.

Yapılandırma Azure AD Connect sihirbazından her değiştirildiğinde, yeni bir zaman damgalı JSON ayarları dosyası **%ProgramData%\AADConnect**' e otomatik olarak verilir. Ayarlar dosya adı, **uygulanan-SynchronizationPolicy-* biçimindedir.** Dosya adının son bölümünün zaman damgası olduğu JSON.

> [!IMPORTANT]
> Yalnızca Azure AD Connect tarafından yapılan değişiklikler otomatik olarak verilir. Güncel bir kopyayı sürdürmek için gerektiğinde PowerShell, Synchronization Service Manager veya eşitleme kuralları Düzenleyicisi kullanılarak yapılan tüm değişiklikler isteğe bağlı olarak verilmelidir. İsteğe bağlı dışarı aktarma, bir ayarların bir kopyasını olağanüstü durum kurtarma amacıyla güvenli bir konuma yerleştirmek için de kullanılabilir.

## <a name="export-azure-ad-connect-settings"></a>Azure AD Connect ayarları dışarı aktar 

Yapılandırma ayarlarınızın bir özetini görüntülemek için Azure AD Connect aracını açın ve **geçerli yapılandırmayı görüntüle veya dışarı aktar** adlı ek görevi seçin. Ayarlarınızın hızlı bir özeti, sunucunuzun tam yapılandırmasını dışarı aktarma özelliğiyle birlikte gösterilir.

Varsayılan olarak, ayarlar **%ProgramData%\AADConnect**' e aktarılabilir. Ayrıca, bir olağanüstü durum oluşursa kullanılabilirliği sağlamak için ayarları korumalı bir konuma kaydetmeyi de tercih edebilirsiniz. Ayarlar JSON dosya biçimi kullanılarak verilir ve mantıksal tutarlılığı sağlamak için el ile oluşturuya da düzenlenmemelidir. El ile oluşturulmuş veya düzenlenmiş bir dosyanın içe aktarılması desteklenmez ve beklenmeyen sonuçlara yol açabilir.

## <a name="import-azure-ad-connect-settings"></a>Azure AD Connect ayarlarını içeri aktar

Daha önce dışarı aktarılan ayarları içeri aktarmak için:
 
1. **Azure AD Connect** yeni bir sunucuya yükler.
1. **Hoş geldiniz** sayfasından sonra **Özelleştir** seçeneğini belirleyin.
1. **Eşitleme ayarlarını Içeri aktar**' ı seçin. Daha önce aktarılmış JSON ayarları dosyasına gözatamazsınız.
1. **Yükle**'yi seçin.

   ![Gerekli bileşenleri yüklemeyi göster ekranını gösteren ekran görüntüsü](media/how-to-connect-import-export-config/import-1.png)

> [!NOTE]
> Bu sayfadaki ayarları LocalDB yerine SQL Server kullanımı veya varsayılan bir VSA yerine var olan bir hizmet hesabının kullanımı gibi geçersiz kılın. Bu ayarlar yapılandırma ayarları dosyasından içeri aktarılmaz. Bunlar, bilgi ve karşılaştırma amaçlarıyla mevcuttur.

### <a name="import-installation-experience"></a>Yükleme deneyimini içeri aktar 

İçeri aktarma yükleme deneyimi, kullanıcının var olan bir sunucunun reproducibility kolayca sağlanması için en az sayıda girişe sahip olarak basit bir şekilde depolanır.

Yükleme deneyimi sırasında yapılabilecek tek değişiklikler aşağıda verilmiştir. Azure AD Connect sihirbazından yüklemeden sonra diğer tüm değişiklikler yapılabilir:
- **Azure Active Directory kimlik bilgileri**: özgün sunucuyu yapılandırmak Için kullanılan Azure genel yöneticisi hesap adı varsayılan olarak önerilir.    Bilgileri yeni bir dizinle eşleştirmek istiyorsanız bu değişiklik değiştirilmelidir.
- **Kullanıcı oturum açma**: özgün sunucunuz için yapılandırılmış oturum açma seçenekleri varsayılan olarak seçilidir ve kimlik bilgilerini veya yapılandırma sırasında gereken diğer bilgileri otomatik olarak ister. Nadir durumlarda, etkin sunucu 'nun davranışını değiştirmekten kaçınmak için farklı seçeneklere sahip bir sunucu ayarlamanız gerekebilir. Aksi takdirde, aynı ayarları kullanmak için **İleri** ' yi seçin.
- **Şirket içi dizin kimlik bilgileri**: eşitleme ayarlarınıza eklenen her şirket içi dizin için, eşitleme hesabı oluşturmak veya önceden oluşturulmuş bir özel eşitleme hesabı sağlamak üzere kimlik bilgilerini sağlamanız gerekir. Bu yordam, Dizin ekleyemez veya kaldıramıyorum özel durum ile temiz yüklemeye eşdeğerdir.
- **Yapılandırma seçenekleri**: temiz yüklemede olduğu gibi, otomatik eşitlemenin başlatılıp başlatılmayacağını veya hazırlama modunun etkinleştirilmesi için başlangıç ayarlarını yapılandırmayı tercih edebilirsiniz. Temel fark, hazırlama modunun, sonuçları Azure 'a etkin bir şekilde dışarı aktarmadan önce yapılandırma ve eşitleme sonuçlarının karşılaştırmasına izin vermek için, varsayılan olarak kasıtlı olarak etkinleştirilmiştir.

![Dizinlerinizin bağlantısını gösteren ekran görüntüsü](media/how-to-connect-import-export-config/import-2.png)

> [!NOTE]
> Birincil rolde yalnızca bir eşitleme sunucusu olabilir ve yapılandırma değişiklikleri Azure 'a etkin bir şekilde dışarı aktarabilirsiniz. Diğer tüm sunucuların hazırlama modunda yerleştirilmesi gerekir.

## <a name="migrate-settings-from-an-existing-server"></a>Ayarları var olan bir sunucudan geçir 

Var olan bir sunucu ayar yönetimini desteklemiyorsa, sunucuyu yerinde yükseltmeyi veya yeni bir hazırlama sunucusunda kullanılacak ayarları geçirmeyi seçebilirsiniz.

Geçiş için, Yeni yüklemede kullanılmak üzere var olan ayarları çıkaran bir PowerShell betiği çalıştırması gerekir.Mevcut sunucunuzun ayarlarını kataloglamak için bu yöntemi kullanın ve ardından bunları yeni yüklenen bir hazırlama sunucusuna uygulayın.Özgün sunucunun ayarlarını yeni oluşturulan bir sunucuyla karşılaştırmak, sunucular arasındaki değişiklikleri hızlıca görselleşmesini sağlar.Her zaman olduğu gibi, ek bir yapılandırmanın gerekli olmamasını sağlamak için kuruluşunuzun sertifika sürecini izleyin.

### <a name="migration-process"></a>Geçiş süreci 
Ayarları geçirmek için:

 1. Yeni hazırlama sunucusunda **AzureADConnect.msi** başlatın ve Azure AD Connect **hoş geldiniz** sayfasından durun.

 2. Microsoft Azure AD Connect\Tools dizininden **MigrateSettings.ps1** var olan sunucudaki bir konuma kopyalayın. Örneğin, kurulumun mevcut sunucuda oluşturulmuş bir dizin olduğu C:\setup örneğidir.</br>
     ![Azure AD Connect dizinleri gösteren ekran görüntüsü.](media/how-to-connect-import-export-config/migrate-1.png)

     >[!NOTE]
     > Bir ileti görürseniz: " **true** bağımsız değişkenini kabul eden bir Konumsal parametre bulunamıyor.", aşağıdaki gibi:
     >
     >
     >![Hata ekran görüntüsü ](media/how-to-connect-import-export-config/migrate-5.png) sonra MigrateSettings.ps1 dosyasını düzenleyin ve **$true** kaldırın ve betiği çalıştırın: ![ yapılandırmayı düzenlemek için ekran görüntüsü](media/how-to-connect-import-export-config/migrate-6.png)
 



 3. Betiği burada gösterildiği gibi çalıştırın ve tüm alt düzey sunucu yapılandırma dizinini kaydedin. Bu dizini yeni hazırlama sunucusuna kopyalayın. Tüm **aktarılmış-ServerConfiguration-*** klasörünü yeni sunucuya kopyalamanız gerekir.
     ![Windows PowerShell 'de betiği gösteren ekran görüntüsü. ](media/how-to-connect-import-export-config/migrate-2.png)![ Aktarılmış-ServerConfiguration-* klasörünün kopyalanmasını gösteren ekran görüntüsü.](media/how-to-connect-import-export-config/migrate-3.png)

 4. Masaüstündeki simgeye çift tıklayarak **Azure AD Connect** başlatın. Microsoft yazılımı lisans koşulları 'nı kabul edin ve sonraki sayfada **Özelleştir**' i seçin.
 5. **Eşitleme ayarlarını Içeri aktar** onay kutusunu seçin. Kopyalanabilir olan-ServerConfiguration-* klasörüne gitmek için **Araştır** ' ı seçin. Geçirilen ayarları içeri aktarmak için MigratedPolicy.jsseçin.

     ![Eşitleme ayarlarını Içeri aktarma seçeneğini gösteren ekran görüntüsü.](media/how-to-connect-import-export-config/migrate-4.png)

## <a name="post-installation-verification"></a>Yükleme sonrası doğrulama 

İlk olarak içeri aktarılan ayarlar dosyasını yeni dağıtılan sunucunun verilen ayarlar dosyasıyla karşılaştırmak, elde edilen dağıtıma karşı amaçlanan ile ilgili herhangi bir farkı anlamak için önemli bir adımdır. En sevdiğiniz yan yana metin karşılaştırma uygulamanızı kullanmak, istenen veya yanlışlıkla yapılan değişiklikleri hızlıca vurgulayan bir anlık görselleştirme oluşturur.

Daha önce el ile yapılan birçok yapılandırma adımı ortadan kalkmış olsa da, ek bir yapılandırmanın gerekli olmamasını sağlamak için kuruluşunuzun sertifika sürecini yine de izlemeniz gerekir. Bu yapılandırma, şu anda ayarlar yönetimi 'nin bu sürümünde yakalanmayan Gelişmiş ayarları kullanırsanız oluşabilir.

Bilinen sınırlamalar şunlardır:
- **Eşitleme kuralları**: Microsoft 'un standart kurallarla çakışmaları önlemek için özel bir kuralın önceliği 0 ile 99 arasında olmalıdır. Özel bir kuralın ayrılmış aralığın dışına yerleştirilmesi, yapılandırmaya standart kurallar eklendikçe özel kuralınızın etrafında kaydırılmasıyla sonuçlanabilir. Yapılandırmanız değiştirilmiş standart kuralları içeriyorsa, benzer bir sorun oluşur. Standart bir kuralı değiştirme önerilmez ve kural yerleşimi büyük olasılıkla yanlış olabilir.
- **Cihaz geri yazma**: Bu ayarlar kataloglanmış. Bunlar şu anda yapılandırma sırasında uygulanmıyor. Özgün sunucunuz için cihaz geri yazma etkinleştirildiyse, özelliği yeni dağıtılan sunucuda el ile yapılandırmanız gerekir.
- **Eşitlenmiş nesne türleri**: Synchronization Service Manager kullanarak eşitlenmiş nesne türleri listesini (kullanıcılar, kişiler ve gruplar gibi) kısıtlamak mümkün olsa da, bu özellik şu anda eşitleme ayarları aracılığıyla desteklenmez. Yüklemeyi tamamladıktan sonra, gelişmiş yapılandırmayı el ile yeniden uygulamalısınız.
- **Özel çalıştırma profilleri**: Synchronization Service Manager kullanarak varsayılan çalıştırma profilleri kümesini değiştirmek mümkün olsa da, bu özellik şu anda eşitleme ayarları aracılığıyla desteklenmez. Yüklemeyi tamamladıktan sonra, gelişmiş yapılandırmayı el ile yeniden uygulamalısınız.
- **Sağlama hiyerarşisi yapılandırılıyor**: Synchronization Service Manager Bu gelişmiş özelliği eşitleme ayarları aracılığıyla desteklenmez. İlk dağıtımı tamamladıktan sonra el ile yeniden yapılandırılması gerekir.
- **Active Directory Federasyon Hizmetleri (AD FS) (AD FS) ve PingFederate kimlik doğrulaması**: Bu kimlik doğrulama özellikleriyle ilişkili oturum açma yöntemleri otomatik olarak önceden seçilmiştir. Tüm gerekli yapılandırma parametrelerini etkileşimli olarak sağlamanız gerekir.
- **Devre dışı bırakılmış bir özel eşitleme kuralı, etkin olarak içeri** aktarılacak: devre dışı bir özel eşitleme kuralı, etkin olarak içeri aktarılır. Yeni sunucuda da devre dışı bıraktığınızdan emin olun.

 ## <a name="next-steps"></a>Sonraki adımlar

- [Donanım ve önkoşullar](how-to-connect-install-prerequisites.md) 
- [Hızlı ayarlar](how-to-connect-install-express.md)
- [Özelleştirilmiş ayarlar](how-to-connect-install-custom.md)
- [Azure AD Connect Health aracılarını yükleme](how-to-connect-health-agent-install.md) 
