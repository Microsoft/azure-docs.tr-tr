---
title: Azure geçişi ile şirket içi sunucularda yazılım envanterini bulma
description: Azure geçişi bulma ve değerlendirmesi ile şirket içi sunucularda yazılım envanterini bulmayı öğrenin.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: d68c3729e8a63f8342cd51b62413aec3276c6165
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871019"
---
# <a name="discover-installed-software-inventory-and-sql-server-instances-and-databases"></a>Yüklü yazılım envanterini bulma ve örnekleri ve veritabanlarını SQL Server

Bu makalede, Azure geçişi: bulma ve değerlendirme aracı kullanılarak yüklenen yazılım envanterinin ve VMware ortamınızda çalışan sunucularda SQL Server örnekleri ve veritabanlarının nasıl keşfedilmesi açıklanmaktadır.

Yazılım envanteri gerçekleştirmek, iş yükleriniz için Azure 'a geçiş yolunu belirlemenize ve uyarlamanıza yardımcı olur. Yazılım envanteri, sunucu kimlik bilgilerini kullanarak bulma işlemi gerçekleştirmek için Azure geçişi gereci kullanır. Tamamen aracısız değildir, bu verileri toplamak için sunuculara hiçbir aracı yüklenmez.

## <a name="before-you-start"></a>Başlamadan önce

- Azure geçişi: bulma ve değerlendirme aracı eklenmiş [bir proje oluşturduğunuzdan](./create-manage-projects.md) emin olun.
- Yazılım envanterini gerçekleştirmek için [VMware gereksinimlerini](migrate-support-matrix-vmware.md#vmware-requirements) gözden geçirin.
- Gereci ayarlamadan önce [gereç gereksinimlerini](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) gözden geçirin.
- Sunucularda yazılım envanterini başlatmadan önce [uygulama bulma gereksinimlerini](migrate-support-matrix-vmware.md#application-discovery-requirements) gözden geçirin.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Azure geçişi gereci dağıtma ve yapılandırma

1. Azure geçişi gerecini dağıtmaya yönelik gereksinimleri [gözden geçirin](migrate-appliance.md#appliance---vmware) .
2. Gereçlerin [ortak](migrate-appliance.md#public-cloud-urls) ve [kamu bulutlarında](migrate-appliance.md#government-cloud-urls)erişmesi gereken Azure URL 'lerini gözden geçirin.
3. Bulma ve değerlendirme sırasında gereç tarafından toplanan [verileri gözden geçirin](migrate-appliance.md#collected-data---vmware) .
4. Gereç [için bağlantı noktası](migrate-support-matrix-vmware.md#port-access-requirements) erişim gereksinimleri.
5. Bulmayı başlatmak için [Azure geçişi gereci dağıtın](how-to-set-up-appliance-vmware.md) . Gereci dağıtmak için, bir OVA şablonunu indirip vCenter Server çalıştıran bir sunucu oluşturmak için VMware 'ye içeri aktarabilirsiniz. Gereci dağıttıktan sonra projeyi projeye kaydetmeniz ve bulmayı başlatacak şekilde yapılandırmanız gerekir.
6. Gereci yapılandırırken, Gereç Yapılandırma Yöneticisi 'nde aşağıdakileri belirtmeniz gerekir:
    - Bağlanmak istediğiniz vCenter Server ayrıntıları.
    - VMware ortamınızdaki sunucuları bulmaya yönelik kapsamlı kimlik bilgileri vCenter Server.
    - Etki alanı/Windows (etki alanı olmayan)/Linux (etki alanı olmayan) kimlik bilgileri olabilen sunucu kimlik bilgileri. Kimlik bilgilerini ve bunları nasıl işleyeceğinizi [öğrenmek hakkında daha fazla bilgi edinin](add-server-credentials.md) .

## <a name="verify-permissions"></a>İzinleri doğrulama

- Bulma ve değerlendirme için [vCenter Server salt okunurdur bir hesap oluşturmanız](./tutorial-discover-vmware.md#prepare-vmware) gerekir.   >  Yazılım envanterini gerçekleştirmek üzere sunucularla etkileşime geçmek için, salt okuma hesabının, sanal makineler **Konuk işlemleri** için etkinleştirilmiş ayrıcalıkları olması gerekir.
- Uygulama bulma için gereç Yapılandırma Yöneticisi 'nde birden çok etki alanı ve etki alanı olmayan (Windows/Linux) kimlik bilgilerini ekleyebilirsiniz. Windows Server için bir Konuk Kullanıcı hesabına ve tüm Linux sunucuları için normal/normal kullanıcı hesabına (sudo erişimi olmayan) ihtiyacınız vardır. Kimlik bilgilerini ve bunları nasıl işleyeceğinizi [öğrenmek hakkında daha fazla bilgi edinin](add-server-credentials.md) .

### <a name="add-credentials-and-initiate-discovery"></a>Kimlik bilgileri ekleme ve bulmayı başlatma

1. Gereç yapılandırma yöneticisini açın, gerecin önkoşul denetimlerini ve kaydını doldurun.
2. **Kimlik bilgilerini ve bulma kaynaklarını yönetme** paneline gidin.
1.  **1. Adım: vCenter Server kimlik bilgilerini belirtin** bölümünde, gerecin vCenter Server çalıştıran sunucuları keşfetmesi için kullanacağı vCenter Server hesabı için kimlik bilgilerini sağlamak üzere **kimlik bilgileri ekle** ' ye tıklayın.
1. **2. Adım: vCenter Server ayrıntıları belirtin** bölümünde, açılan listeden kimlik bilgileri için kolay ad seçmek üzere **bulma kaynağı Ekle** ' ye tıklayın, :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="vCenter Server Ayrıntılar için gereç Yapılandırma Yöneticisi"::: ' nde vCenter Server örneği panel 3 ' ün **IP adresini/FQDN** 'sini belirtin
1. **3. Adım: yazılım envanterini gerçekleştirmek için sunucu kimlik bilgilerini sağlama, aracısız bağımlılık Analizi ve SQL Server örnekleri ve veritabanlarının bulunması**, yazılım envanterini başlatmak için birden çok sunucu kimlik bilgisi sağlamak üzere **kimlik bilgileri ekle** ' ye tıklayın.
1. VCenter Server bulmayı **başlatmak için bulmayı Başlat**' a tıklayın.

 VCenter Server bulma işlemi tamamlandıktan sonra, Gereç yüklü uygulamalar, roller ve Özellikler (yazılım envanteri) bulmayı başlatır. Süre, bulunan sunucu sayısına bağlıdır. 500 sunucularında, bulunan stokun Azure geçişi portalında görünmesi yaklaşık bir saat sürer.

## <a name="review-and-export-the-inventory"></a>Envanteri gözden geçirin ve dışa aktarın

Yazılım envanteri tamamlandıktan sonra Azure portal envanterini gözden geçirebilir ve dışarı aktarabilirsiniz.

1. **Azure geçişi-Windows, Linux ve SQL Server sunucuları**  >  **Azure geçişi: bulma ve değerlendirme**' da, **bulunan sunucular** sayfasını açmak için, görünen sayıya tıklayın.

    > [!NOTE]
    > Bu aşamada, değerlendirmek istediğiniz sunucular arasında bağımlılıkları görselleştirmek için, isteğe bağlı olarak bulunan sunucular için bağımlılık analizini de etkinleştirebilirsiniz. Bağımlılık analizi hakkında [daha fazla bilgi edinin](concepts-dependency-visualization.md) .

2. **Yazılım envanteri** sütununda, bulunan uygulamaları, rolleri ve özellikleri gözden geçirmek için, görünen sayıya tıklayın.
4. Envanteri dışarı aktarmak için **bulunan sunucularda**, **uygulama envanterini dışarı aktar**' a tıklayın.

Yazılım envanteri, Excel biçiminde verilir ve indirilir. **Yazılım envanteri** sayfasında tüm sunucularda bulunan tüm uygulamalar görüntülenir.

## <a name="discover-sql-server-instances-and-databases"></a>SQL Server örnekleri ve veritabanlarını bulma

- Yazılım envanteri Ayrıca VMware ortamınızda çalışan SQL Server örnekleri tanımlar.
- Gereç Yapılandırma Yöneticisi 'nde Windows kimlik doğrulaması veya SQL Server kimlik doğrulama kimlik bilgileri sağlamadıysanız, gerecin ilgili SQL Server örneklerine bağlanmak için onları kullanabilmesi adına kimlik bilgilerini ekleyin.

Bağlantı kurulduktan sonra, Gereç SQL Server örneklerinin ve veritabanlarının yapılandırma ve performans verilerini toplar. SQL Server yapılandırma verileri her 24 saatte bir güncelleştirilir ve performans verileri her 30 saniyede yakalanır. Bu nedenle, SQL Server örneğin özelliklerinde ve veritabanı durumu, uyumluluk düzeyi vb. gibi veritabanlarının her türlü değişikliği portalda güncelleştirmek 24 saate kadar sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

- Bulunan sunucular için [bir değerlendirme oluşturun](how-to-create-assessment.md) .
- [SQL Server](./tutorial-assess-sql.md) 'ı Azure SQL 'e geçiş için değerlendirin.
