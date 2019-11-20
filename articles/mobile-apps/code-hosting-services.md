---
title: GitHub ve Azure DevOps ile mobil uygulama kaynak kodunuzu bulutta barındırın
description: Microsoft hizmetleriyle, mobil uygulama kodunuzu bulutta barındırmanıza yönelik hizmetler hakkında bilgi edinin.
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: f0af2ee5e62bbdbfb5d18cffc3e3ed62edec81f3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795633"
---
# <a name="cloud-hosted-source-code-management-services"></a>Bulut barındırılan kaynak kodu Yönetim Hizmetleri
Aynı kod tabanında çalışan birden fazla takım üyesi olan geliştirme ekipleriniz varsa, kodunuzu barındırmak için uygun bir yer bulmanız gerekir. Verilerin bulutta depolanması ve merkezi bir deponun olması, herkesin kod dosyalarını karşıya yüklemesine, düzenlemesine ve yönetmesine, projelerde diğer geliştiricilerle etkileşime girmesine ve bu kodun nerede olursanız olun Internet bağlantısına sahip olmanız gerekir.

Bulut barındırma, daha az donanım yapılandırması gerektirdiğinden ve kuruluşların uygulama işlemini daha çevik bir şekilde tamamlamalarına olanak sağlayan şirket içi seçeneklerden çok daha kolay.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Bulutta barındırmanın kaynak kodu avantajları
- Verilerinizi her yerden görüntülemek ve yönetmek için **Merkezi bulut depolama** .
- , Harika yazılımların sürekli teslimini sağlamak üzere kodu izlemek ve projeleri yönetmek için takımlar içinde **daha iyi işbirliği ve Temizleyici kod** sağlar.
- **Dahil edin** ve projelerinize **katkıda** bulunmak kolaylaşır.
- Takımınız **daha hızlı** çalışabilir ve projelerinize **kolayca katkıda** bulunabilir.
- Kendi donanımınızın, sunucularınızın, VPN 'in ve benzeri konularda endişelenmenize gerek kalmadan **maliyeti azaltın** .

Uygulama verilerinizi bulutta barındırmak için aşağıdaki hizmetleri kullanın.

## <a name="github"></a>GitHub
[GitHub](https://github.com/) , kaynak kodu projelerini birçok farklı programlama dilinde barındıran ve her yinelemede yapılan çeşitli değişiklikleri izleyen açık kaynaklı bir depo barındırma hizmetidir.

**Temel Özellikler**
- Tüm kodunuzla tek bir yerde **barındırma kodu** . Özel, genel veya açık kaynaklı tüm depolarda, kod barındırmanıza, sürümüne ve serbest bırakmaya yardımcı olacak araçlar bulunur.
- Sorunsuz **kod incelemesi** ve yerleşik inceleme araçları, herhangi bir takım işleminin önemli bir parçasını kod incelemesi yapar.
    - Dalları koruyun, değişiklikler önerin ve gözden geçirme isteği yapın.
    - Bağlamda fark ve açıklamaya bakın ve açık geri bildirim alın.
- GitHub 'ın **Proje Yönetim Araçları**ile erken koordine edin, hizalı kalın ve daha fazlasını yapın.
    - Projenin büyük resmine bakın.
    - GitHub içindeki kodunuzun hemen yanında yer alan **görev panoları** .
    - Ekip üyelerine sorunlar veya çekme istekleri atamanıza izin vermek için **kartları sürükleyip bırakın** .
    - İlerlemeyi düzenlemek ve izlemek için **kilometre taşları** .
    - Yararlı olabilecek ancak belirli bir sorun veya çekme isteğine ait olmayan fikirleri yakalamak için **Notlar** .
- **[GitHub marketi](https://github.com/marketplace)** 'nden uygulama satın alarak daha iyi iletişim ve iş otomasyonu için doğru araçları kolayca bulup seçin.
- **Ekip yönetimi** , takımları, ekip ve erişim izinlerini düzenlemenize yardımcı olmak için **Kullanıcı rollerini** , konu ve takım odaklı konuşmaları tutmaya yönelik **tartışma iş parçacıkları** araçları ve hızlı bir şekilde ayarlamaya yönelik **topluluk yönergeleri** bir hesapla yeni takım üyeleri.
- Bunları izlemek için popüler projelere **gidin** ve **yıldızı** yapın.
- **Ağ ve** sektör Içindeki diğerlerinden öğrenin.

**Başvur**
- [GitHub](https://github.com/)
- [GitHub kılavuzlarını](https://guides.github.com/)
- [GitHub topluluk Forumu](https://github.community/)
- [GitHub marketi](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/) , kaynak denetimi seçenekleri olarak [Azure Repos](https://azure.microsoft.com/services/devops/repos/) ve [Team Foundation sürüm denetimi (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) destekler. İşbirliğine dayalı kod incelemeleri, gelişmiş dosya yönetimi, kod arama ve Şube ilkeleri içeren sınırsız sayıda ücretsiz özel depoya sahiptir ve yüksek kaliteli kod sağlar. Azure Repos, küçük projeler ve yerel AAD desteği ve Gelişmiş İlkeler gerektiren büyük kuruluşlar için harika.
    
**Temel Özellikler**
- Ortak ve özel depolarınız için **sınırsız sayıda bulutta barındırılan Git kaynak kodu deposu**
    - Tüm git istemcileri için destek.
    - Web kancaları ve API Tümleştirmesi.
- Bir Repos çekme isteğinden bir **sonraki derlemenize başlayın**
    - Her değişiklik için iş parçacıklı tartışmayı ve sürekli tümleştirmeyi kullanarak daha iyi kod oluşturmak üzere işbirliği yapın.
    - Azure Pipelines'ı veya kendi seçtiğiniz araçları kullanarak tamamlanan her çekme isteğiyle derlemeleri, testleri ve dağıtımları otomatik olarak tetiklemek için sürekli tümleştirme/sürekli teslim (CI/CD) ayarlarını yapın.
    - Kod kalitesini dal ilkeleriyle koruyun.
- Kod Incelemesi dahil olmak üzere **Team Foundation sürüm denetimi Ile merkezi sürüm denetimi**.
- Xcode, tutulma, IntelliJ, Android Studio, Visual Studio, Visual Studio Code ve daha fazlasını kullanarak **kodunuza bağlanın** .
- **Güçlü anlamsal kod arama**.
- **Kurumsal** olarak, Azure ACTIVE DIRECTORY (ad) ile yerel tümleştirmeye sahip olduğundan, kod Depolarınıza erişimi yönetme sürecini basitleştirir.
- Minimum kod incelemesi sayısı, başarılı derlemeler gerektirme ve git birleştirme stratejilerini zorlama gibi dal ilkeleriyle **kod kalitesiyle emin olun** .
- Uygulamanıza erişmek ve işi yönetmek için en **sevdiğiniz geliştirme ortamınızı bağlayın** .

**Başvur**
- [Azure Repos kullanmaya başlayın](https://azure.microsoft.com/services/devops/repos/) 
- [Azure Repos belgeleri](/azure/devops/repos/?view=azure-devops)