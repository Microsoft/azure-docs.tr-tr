---
title: CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0 şema Sample
description: Yönetim bilgileri kıyaslama v 1.3.0 şema Sample Microsoft Azure CIS 'ye genel bakış. Bu şema örneği, müşterilerin belirli denetimleri değerlendirmesine yardımcı olur.
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: 7030b4e9e9ddd3d5bbf1a5a7deaf1e1aa241b38e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566005"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v130-blueprint-sample"></a>CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0 şema Sample

CIS Microsoft Azure Yönetim kıyaslaması, kıyaslama v 1.3.0 şema örneği, belirli Microsoft Azure CIS 'leri değerlendirmenize yardımcı olan [Azure ilkesi](../../policy/overview.md) kullanarak idare koruyucu-rayları sağlar ve kıyaslama v 1.3.0 önerilerini sunmaktadır. Bu şema, müşterilerin bir Azure dağıtılan mimari için Microsoft Azure, yönetim kıyaslaması kıyaslama v 1.3.0 önerilerini uygulaması gereken bir çekirdek ilke kümesi dağıtmakta yardımcı olur.

## <a name="recommendation-mapping"></a>Öneri eşlemesi

[Azure ilke önerisi eşleme](../../policy/samples/cis-azure-1-3-0.md) , bu şema içinde yer alan ilke tanımları hakkında ayrıntılar ve bu ilke **tanımlarının, yönetim** kıyaslama v 1.3.0 temel Microsoft Azure Bir mimariye atandığında kaynaklar, atanan ilke tanımlarına yönelik uyumsuzluk için Azure Ilkesi tarafından değerlendirilir. Daha fazla bilgi için bkz. [Azure İlkesi](../../policy/overview.md).

## <a name="deploy"></a>Dağıtma

Azure şemaları CIS 'yi, kıyaslama v 1.3.0 şema örneğini Microsoft Azure dağıtmak için aşağıdaki adımlar gerçekleştirilmelidir:

> [!div class="checklist"]
> - Örnekten yeni bir şema oluşturma
> - Örneğinizin kopyasını **Yayımlandı** olarak işaretleyin
> - Şemanızın kopyasını mevcut bir aboneliğe atayın

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

### <a name="create-blueprint-from-sample"></a>Örnekten şema oluştur

İlk olarak, başlangıç noktası olarak örneği kullanarak ortamınızda yeni bir şema oluşturup şema örneğini uygulayın.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Soldaki **Başlangıç** sayfasında, _Şema oluştur_ bölümündeki **Oluştur** düğmesini seçin.

1. _Diğer örnekler_ altında **kıyaslama v 1.3.0** şema örneğini Microsoft Azure CIS 'leri bulun ve **Bu örneği kullan**' ı seçin.

1. Şema örneğinin _Temel bilgiler_ bölümüne girin:

   - Şema **adı**: CIS Microsoft Azure temelleri şeması örneği için bir ad sağlayın.
   - **Tanım konumu**: üç noktayı kullanın ve örneğin kopyasını kaydetmek için yönetim grubunu seçin.

1. Sayfanın üst kısmındaki _Yapıtlar_ sekmesini seçin veya sayfanın en altındaki **Sonraki: Yapıtlar** seçeneğini belirleyin.

1. Şema örneğine dahil edilen yapıtların listesini gözden geçirin. Yapıtların çoğu parametreler içerir; bu parametreleri daha sonra tanımlayacağız. Şema örneğini gözden geçirmeyi tamamladığınızda **Taslağı Kaydet**’i seçin.

### <a name="publish-the-sample-copy"></a>Örnek kopyayı yayımlama

Şimdi şema örneği kopyanız, ortamınızda oluşturulmuştur. Bu, **Taslak** modunda oluşturulur ve atanabilmesi ve dağıtılabilmesi için önce **Yayımlandı** durumunda olmalıdır. Şema örneğinin kopyası ortamınıza ve gereksinimlerinize göre özelleştirilebilir, ancak bu değişiklik, yönetim, kıyaslama v 1.3.0 önerilerini kullanarak CIS Microsoft Azure

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Sayfanın üst kısmında **Şemayı yayımla**’yı seçin. Sağdaki yeni sayfada, şema örneği kopyanız için bir **Sürüm** sağlayın. Bu özellik daha sonra bir değişiklik yaptığınızda faydalı olacaktır. "CIS 'den yayımlanan Ilk sürüm Microsoft Azure temel kıyaslama kıyaslaması şeması örneği" gibi **değişiklik notları** sağlayın. " Sayfanın alt kısmında **Yayımla** düğmesini seçin.

### <a name="assign-the-sample-copy"></a>Örnek kopyayı atama

Şema örneğinin kopyası başarıyla **yayımlandıktan** sonra, kaydedildiği yönetim grubu içindeki bir aboneliğe atanabilir. Bu adımda, şema örneği kopyasının her bir dağıtımını benzersiz hale getirmek için parametreler sağlanır.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Şema tanımı sayfasının en üstünde **Şema ata**’yı seçin.

1. Şema ataması için parametre değerlerini sağlayın:

   - Temel Bilgiler

     - **Abonelikler**: şema örneğinin kopyasını kaydettiğiniz yönetim grubundaki bir veya daha fazla abonelik seçin. Birden fazla abonelik seçerseniz, girilen parametreler kullanılarak her biri için bir atama oluşturulur.
     - **Atama adı**: ad, BLUEPRINT adına göre önceden doldurulur.
       Gerektiğinde değiştirin veya olduğu gibi bırakın.
     - **Konum**: yönetilen kimliğin oluşturulacağı bölgeyi seçin. Azure Blueprint bu yönetilen kimliği kullanarak tüm yapıtları atanmış şemaya dağıtır. Daha fazla bilgi için bkz. [Azure kaynakları için yönetilen kimlikler](../../../active-directory/managed-identities-azure-resources/overview.md).
     - Şema **tanımı sürümü**: şema örneğinin kopyasının **yayınlanmış** bir sürümünü seçin.

   - Kilit ataması

     Ortamınız için şema kilitleme ayarını seçin. Daha fazla bilgi için bkz. [şema kaynağı kilitleme](../concepts/resource-locking.md).

   - Yönetilen Kimlik

     Varsayılan sistem tarafından _atanmış_ yönetilen kimlik seçeneğini bırakın.

   - Yapıt parametreleri

     Bu bölümde tanımlanan parametreler, tanımlandığı yapıt için geçerlidir. Bu parametreler, Blueprint atama sırasında tanımlandıklarından [dinamik parametrelerdir](../concepts/parameters.md#dynamic-parameters) . Tam liste veya yapıt parametreleri ve açıklamaları için bkz. [yapıt parametreleri tablosu](#artifact-parameters-table).

1. Tüm parametreler girildikten sonra, sayfanın alt kısmında **Ata** seçeneğini belirleyin. Şema ataması oluşturulur ve yapıt dağıtımı başlar. Dağıtım kabaca bir saat sürer. Dağıtımın durumunu denetlemek için şema atamasını açın.

> [!WARNING]
> Azure Blueprints hizmeti ve yerleşik şema örnekleri **ücretsizdir**. Azure kaynakları, [ürüne göre fiyatlandırılır](https://azure.microsoft.com/pricing/). Bu şema örneği tarafından dağıtılan kaynakları çalıştırma maliyetini tahmin etmek için [fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/) kullanın.

### <a name="artifact-parameters-table"></a>Yapıt parametreleri tablosu

Aşağıdaki tabloda, şema yapıtı parametrelerinin bir listesi verilmiştir:

|Yapıt adı|Yapıt türü|Parametre adı|Açıklama|
|-|-|-|-|
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|Kullanım için onaylanan sanal makine uzantılarının listesi|Sanal makine uzantılarının noktalı virgülle ayrılmış listesi; uzantıların tüm listesini görmek için Azure PowerShell komutunu kullanın Get-AzVMExtensionImage|
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: SQL yönetilen örnekler, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: SQL Server için güvenlik açığı değerlendirmesi ayarları, tarama raporlarını alacak bir e-posta adresi içermelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Azure Data Lake Store içindeki tanılama günlükleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: disk şifrelemesi sanal makinelere uygulanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: Anahtar Kasası, temizleme koruması etkin olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: API uygulamasının ' açık ' olarak ayarlanmış ' Istemci sertifikaları (gelen istemci sertifikaları) ' olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: SQL Server 'lar, bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: yönetilen kimlik İşlev Uygulaması kullanılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Key Vault için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: özel abonelik sahibi rolleri mevcut olmamalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: anahtarların süre sonu tarihleri ayarlanmış olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: SQL yönetilen örneği 'nde güvenlik açığı değerlendirmesi etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: API uygulamasının bir parçası olarak kullanılıyorsa ' PHP Version ' öğesinin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: App Service için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: depolama hesapları, sanal ağ kurallarını kullanarak ağ erişimini kısıtlamalı|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: yönetilen kimliğin Web uygulamanızda kullanılması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Internet 'ten SSH erişiminin engellenmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: eklenmemiş diskler şifrelenmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: depolama için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: depolama hesapları ağ erişimini kısıtlamalı|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Logic Apps içindeki tanılama günlükleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: IoT Hub içindeki tanılama günlükleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: FTPS yalnızca İşlev Uygulaması gerekli olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: belirli güvenlik işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. Security/securitySolutions/Delete)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: belirli güvenlik işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. Security/securitySolutions/Write)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: depolama hesaplarına Güvenli aktarım etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Batch hesaplarındaki tanılama günlükleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: Log Analytics aracısının otomatik olarak sağlanması aboneliğinizde etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: Web uygulamasının bir parçası olarak kullanılıyorsa ' Java sürümü ' nin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Web uygulamanızda FTPS gerekli olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: sunucular için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke etkisi: abonelikler güvenlik sorunları için bir iletişim e-posta adresine sahip olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: depolama hesabı genel erişimine izin verilmemelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Kubernetes için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: PostgreSQL veritabanı sunucuları için bağlantı azaltma etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: WEB uygulamasında ' Istemci sertifikaları (gelen istemci sertifikaları) ', ' on ' olarak ayarlanmış olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: makinelerdeki SQL Server 'lar için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: yüksek önem derecesine sahip uyarılar için e-posta bildirimi etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: depolama hesabı, şifreleme için müşteri tarafından yönetilen anahtarı kullanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Web uygulamasının bir parçası olarak kullanılıyorsa, ' Python sürümü ' öğesinin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: Işlev uygulamasının bir parçası olarak kullanılıyorsa ' Python sürümü ' öğesinin en son olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: WEB uygulamasının bir parçası olarak kullanılıyorsa, ' PHP Version ' öğesinin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: API uygulamasının bir parçası olarak kullanılıyorsa ' Python sürümü ' nin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: sanal makine ölçek kümelerindeki tanılama günlükleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: Azure SQL veritabanı sunucuları için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: Olay Hub 'ındaki tanılama günlükleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: sistem güncelleştirmelerinin makinelerinizde yüklü olması gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: API uygulamasının bir parçası olarak kullanılıyorsa ' Java Version ' öğesinin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: SQL Server 'lar, 90 gün sonra bekletme veya daha yüksek bir denetim ile yapılandırılmalıdır.|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Web uygulamasını çalıştırmak için kullanılmışsa ' HTTP Version ' nin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: API uygulamanızda en son TLS sürümü kullanılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: MFA 'nın aboneliğinizde yazma izinleri olan hesaplar etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke etkisi: Web uygulamanızda kimlik doğrulaması etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: gizli dizileri, zaman aşımı tarihleri ayarlanmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: API uygulamasını çalıştırmak için kullanılmışsa ' HTTP Version ' nin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: FTPS yalnızca API uygulamanızda gerekli olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: Işlev uygulamasının bir parçası olarak kullanılıyorsa ' Java Version ' öğesinin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: SQL Server 'da denetim etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: MFA 'nın aboneliğinizdeki sahip izinlerine sahip hesaplarda etkinleştirilmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Gelişmiş veri güvenliği SQL sunucularınızda etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Gelişmiş veri güvenliği SQL yönetilen örneği üzerinde etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: Role-Based Access Control (RBAC), Kubernetes hizmetlerinde kullanılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Azure Güvenlik Merkezi 'nde eksik Endpoint Protection Izleyicisi|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: arama hizmetlerindeki tanılama günlükleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: uygulama hizmetlerindeki tanılama günlükleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. Network/networkSecurityGroups/Delete)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. Network/networkSecurityGroups/securityRules/Delete)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. Network/networkSecurityGroups/securityRules/Write)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. Network/networkSecurityGroups/Write)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. SQL/Servers/firewallRules/Delete)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: belirli yönetim işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. SQL/Servers/firewallRules/Write)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: yalnızca onaylanan VM uzantıları yüklenmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: kapsayıcı kayıt defterleri için Azure Defender etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: yönetilen kimlik API uygulamanızda kullanılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: API uygulamanızda kimlik doğrulaması etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: belirli Ilke işlemleri için bir etkinlik günlüğü uyarısı olmalıdır (Microsoft. Authorization/Policyaslar/Sil)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: belirli Ilke işlemleri için bir etkinlik günlüğü uyarısı bulunmalıdır (Microsoft. Authorization/Policyaslar/Write)|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Işlev uygulamanızda kimlik doğrulaması etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Data Lake Analytics içindeki tanılama günlükleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: depolama hesapları güvenilen Microsoft hizmetlerinden erişime izin verilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Key Vault içindeki tanılama günlükleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke etkisi: PostgreSQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: Işlev uygulamasını çalıştırmak için kullanılmışsa ' HTTP Version ' nin en son sürümü olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: MFA 'nın aboneliğinizdeki okuma izinlerine sahip hesaplarda etkinleştirilmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Internet 'ten RDP erişiminin engellenmesi gerekir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: MySQL veritabanı sunucuları için SSL bağlantısını zorla etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: Işlev uygulamasında ' Istemci sertifikaları (gelen istemci sertifikaları) ', ' on ' olarak ayarlanmış olduğundan emin olun|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: PostgreSQL veritabanı sunucuları için günlük kontrol noktaları etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için efekt: PostgreSQL veritabanı sunucuları için günlük bağlantıları etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etkinlik: PostgreSQL veritabanı sunucuları için bir bağlantı günlüğe kaydedilmez.|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: güvenlik açığı değerlendirmesi SQL sunucularınızda etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Web uygulamanızda en son TLS sürümü kullanılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Service Bus içindeki tanılama günlükleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: Azure Stream Analytics içindeki tanılama günlükleri etkinleştirilmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: İşlev Uygulaması en son TLS sürümü kullanılmalıdır|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|İlke için etki: etkinlik günlükleri ile kapsayıcıyı içeren depolama hesabı BYOK ile şifrelenmelidir|Efektler hakkında daha fazla bilgi için bkz. [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|Sanal makine ölçek kümesi tanılama günlüklerinin etkinleştirilip etkinleştirilmediği denetlenirken AKS kümelerini dahil et||
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|Uygulama Hizmetleri için en son Java sürümü|Uygulama Hizmetleri için desteklenen en son Java sürümü|
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|App Services için Linux için en son Python sürümü|Uygulama Hizmetleri için desteklenen en son Python sürümü|
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|Ağ Izleyicisi 'nin etkinleştirilmesi gereken bölgelerin listesi|Bölgelerin tüm listesini görmek için PowerShell komutunu çalıştırın Get-AzLocation|
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|Uygulama Hizmetleri için en son PHP sürümü|Uygulama Hizmetleri için desteklenen en son PHP sürümü|
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|Kaynak günlükleri için gerekli saklama süresi (gün)|Kaynak günlükleri hakkında daha fazla bilgi için şu adresi ziyaret edin [https://aka.ms/resourcelogs](../../../azure-monitor/essentials/resource-logs.md) |
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|Ağ Izleyicisi için kaynak grubunun adı|Ağ Izleyicileri bulunduğu kaynak grubunun adı|
|CIS Microsoft Azure temel geçişleri kıyaslama v 1.3.0|İlke Ataması|SQL sunucuları için gerekli denetim ayarı||

## <a name="next-steps"></a>Sonraki adımlar

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../how-to/update-existing-assignments.md) öğrenin.