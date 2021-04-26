---
title: Azure Defender ve kullanılabilir planlara genel bakış
description: Azure Defender 'ın planları, korumaları ve uyarıları hakkında bilgi edinin. Ardından, gelişmiş güvenlik için aboneliklerinizde Azure Defender 'ı etkinleştirin.
author: memildin
ms.author: memildin
ms.date: 9/30/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 0bd0d9c2230b8400aa3197044f944daceb93c715
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718530"
---
# <a name="introduction-to-azure-defender"></a>Azure Defender'a giriş

Azure Güvenlik Merkezi 'nin özellikleri, bulut güvenliğinin iki geniş kapsamlı boyutunu kapsar:

- **Bulut güvenliği gönderme yönetimi (CSPM)** -Güvenlik Merkezi, tüm Azure kullanıcıları için **ücretsiz** olarak kullanılabilir. Ücretsiz deneyim, güvenli puan, Azure makinelerinizdeki güvenlik yapılandırması yapılandırmalarını, varlık envanterinizi ve daha fazlasını içeren CSPM özelliklerini içerir. Karma bulut durunuzu güçlendirin ve yerleşik ilkelerle uyumluluğu izlemek için bu CSPM özelliklerini kullanın.

- **Bulut iş yükü koruması (CWP)** -Güvenlik Merkezi 'nin tümleşik bulut iş yükü koruma platformu (cwpp), **Azure Defender**, Azure ve hibrit kaynaklarınızı ve iş yüklerinizi gelişmiş, akıllı ve korumalı hale getirir. Azure Defender 'ın etkinleştirilmesi, bu sayfada açıklanan ek güvenlik özelliklerinin bir aralığını sunar. Yerleşik ilkelere ek olarak, herhangi bir Azure Defender planını etkinleştirdiğinizde, özel ilkeler ve girişimler ekleyebilirsiniz. Uyumluluğun gerçek anlamda özelleştirilmiş bir görünümü için NıST ve Azure CIS gibi mevzuat standartları ve [Azure Güvenlik kıyaslaması](https://docs.microsoft.com/security/benchmark/azure/introduction) ekleyebilirsiniz.

Güvenlik Merkezi 'ndeki Azure Defender panosu, ortamınız için CWP özelliklerinin görünürlüğünü ve denetimini sağlar:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Azure Defender panosu örneği" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Azure Defender hangi kaynak türlerini güvende yapabilir?

Azure Defender, sanal makineler, SQL veritabanları, kapsayıcılar, Web uygulamaları, ağınız ve daha fazlası için güvenlik uyarıları ve Gelişmiş tehdit koruması sağlar.

Azure Defender 'ı Azure Güvenlik Merkezi 'nin **fiyatlandırma ve ayarlar** alanından etkinleştirdiğinizde, aşağıdaki Defender planlarının hepsi aynı anda etkinleştirilmiştir ve ortamınızın işlem, veri ve hizmet katmanları için kapsamlı savunma sağlar:

- [Sunucular için Azure Defender](defender-for-servers-introduction.md)
- [App Service için Azure Defender](defender-for-app-service-introduction.md)
- [Depolama için Azure Defender](defender-for-storage-introduction.md)
- [SQL için Azure Defender](defender-for-sql-introduction.md)
- [Kubernetes için Azure Defender](defender-for-kubernetes-introduction.md)
- [Kapsayıcı kayıt defterleri için Azure Defender](defender-for-container-registries-introduction.md)
- [Key Vault için Azure Defender](defender-for-key-vault-introduction.md)
- [Resource Manager için Azure Defender](defender-for-resource-manager-introduction.md)
- [DNS için Azure Defender](defender-for-dns-introduction.md)

Bu planların her biri, güvenlik merkezi belgelerinde ayrı olarak açıklanmıştır.

> [!TIP]
> IoT için Azure Defender (Önizleme), ayrı bir üründür. [IoT Için Azure Defender (Önizleme) ile ilgili](../defender-for-iot/overview.md)tüm ayrıntıları bulacaksınız. 

## <a name="hybrid-cloud-protection"></a>Karma bulut koruması

Ayrıca Azure ortamınızı erteler, hibrit bulut ortamınıza Azure Defender olanakları ekleyebilirsiniz:

- Azure olmayan sunucularınızı koruyun
- Diğer bulutlardaki sanal makinelerinizi koruyun (AWS ve GCP gibi)

En önemli şeylere odaklanabilmeniz için, özelleştirilmiş tehdit zekasından, özel ortamınıza göre öncelikli uyarılar alırsınız.

Korumayı, diğer bulutlarda veya şirket içinde bulunan sanal makinelere ve SQL veritabanlarına genişletmek için, [Azure Arc](https://azure.microsoft.com/services/azure-arc/) dağıtın ve Azure Defender 'ı etkinleştirin. Sunucular için Azure Arc ücretsiz bir hizmettir, ancak Azure Defender gibi Arc etkin sunucularda kullanılan hizmetler, bu hizmet için fiyatlandırmaya göre ücretlendirilir. Azure [Arc Ile Azure olmayan makineler ekleme](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc)hakkında daha fazla bilgi edinin.

> [!TIP]
> AWS için yerel bağlayıcı, Azure Arc dağıtımını sizin için saydam şekilde işler. [AWS hesaplarınızı Azure Güvenlik Merkezi 'Ne bağlama](quickstart-onboard-aws.md)bölümünde daha fazla bilgi edinin.



## <a name="azure-defender-security-alerts"></a>Azure Defender güvenlik uyarıları 

Azure Defender, ortamınızın herhangi bir alanında bir tehdit algıladığında, bir güvenlik uyarısı oluşturur. Bu uyarılar, etkilenen kaynakların ayrıntılarını, önerilen düzeltme adımlarını ve bazı durumlarda bir mantıksal uygulamayı yanıt olarak tetiklemeye yönelik bir seçenek anlatmaktadır.

Bir uyarının Güvenlik Merkezi tarafından oluşturulup oluşturulmayacağını veya Güvenlik Merkezi tarafından bir tümleşik güvenlik ürününden alınıp alınmayacağını, dışarı aktarabilirsiniz. Uyarılarınızı Azure Sentinel 'e, herhangi bir üçüncü taraf SıEM 'e veya herhangi bir harici araca aktarmak için, [BIR SıEM, SOAR veya BT hizmet yönetimi çözümüne akış uyarıları](export-to-siem.md)içindeki yönergeleri izleyin.

> [!NOTE]
> Farklı kaynaklardaki uyarıların görünmesi farklı miktarda zaman alabilir. Örneğin, ağ trafiğinin analizini gerektiren uyarıların, sanal makinelerde çalışan şüpheli işlemlerle ilgili uyarılardan görünmesi daha uzun sürebilir.


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender gelişmiş koruma özellikleri

Azure Defender, kaynaklarınızla ilgili özel öneriler için gelişmiş analiz kullanır. 

Korumalar, makinelerinizde hangi uygulamaların çalışması gerektiğini ve hangi uygulamaların çalıştırılmaması gerektiğini belirlemek için, tam zamanında erişim ve Uyarlamalı uygulama denetimleriyle VM 'nizin yönetim bağlantı noktalarının güvenliğini sağlar. 

Bu korumaların her birini izlemek ve yapılandırmak için Azure Defender panosundaki gelişmiş koruma kutucukları ' nı kullanın. 

## <a name="vulnerability-assessment-and-management"></a>Güvenlik açığı değerlendirmesi ve yönetimi

Azure Defender, sanal makineleriniz ve kapsayıcı kayıt defterleri için ek ücret olmadan güvenlik açığı taraması içerir. Tarayıcılar Qualys tarafından desteklenmektedir, ancak bir Qualys lisansına gerek kalmaz ve hatta bir Qualys hesabınız yoktur. her şey güvenlik merkezi 'Nde sorunsuz şekilde işlenir. 

Bu güvenlik açığı tarayıcılarından bulguları gözden geçirin ve bunların tümünü Güvenlik Merkezi içinden yanıtlayın. Bu, güvenlik merkezini tüm bulut güvenlik çabalarınız için tek bir cam bölmesi olacak şekilde daha yakın hale getirir.

Aşağıdaki sayfalarla daha fazla bilgi edinin:

- [Azure sanal makineleri için Güvenlik Merkezi 'nin tümleşik güvenlik açığı değerlendirme çözümü](deploy-vulnerability-assessment-vm.md)
- [Azure Container Registry 'deki görüntülerde güvenlik açıklarını tanımla](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Defender 'ın avantajları hakkında bilgi edindiniz. 

> [!div class="nextstepaction"]
> [Azure Defender’ı etkinleştirme](enable-azure-defender.md)