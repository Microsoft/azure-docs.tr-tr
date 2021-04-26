---
title: Azure Güvenlik kıyaslama Foundation şema örneğini dağıtma
description: Şema yapıt parametresi ayrıntıları dahil olmak üzere Azure Security kıyaslama Foundation şeması örneği için adımları dağıtın.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: af41dd50c976ac6c0570b8a089211fa310ef4ef1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232622"
---
# <a name="deploy-the-azure-security-benchmark-foundation-blueprint-sample"></a>Azure Güvenlik kıyaslama Foundation şema örneğini dağıtma

Azure Security kıyaslama Foundation şema örneğini dağıtmak için aşağıdaki adımlar gerçekleştirilmelidir:

> [!div class="checklist"]
> - Örnekten yeni bir şema oluşturma
> - Örneğinizin kopyasını **Yayımlandı** olarak işaretleyin
> - Şemanızın kopyasını mevcut bir aboneliğe atayın

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

## <a name="create-blueprint-from-sample"></a>Örnekten şema oluştur

İlk olarak, başlangıç noktası olarak örneği kullanarak ortamınızda yeni bir şema oluşturup şema örneğini uygulayın.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Soldaki **Başlangıç** sayfasında, _Şema oluştur_ bölümündeki **Oluştur** düğmesini seçin.

1. _Diğer örnekler_ altında **Azure Security kıyaslama Foundation** şema örneğini bulun ve **Bu örneği kullan**' ı seçin.

1. Şema örneğinin _Temel bilgiler_ bölümüne girin:

   - **Şema adı**: Azure Security kıyaslama Foundation şema örneğinin kopyasına bir ad verin.
   - **Tanım konumu**: üç noktayı kullanın ve örneğin kopyasını kaydetmek için yönetim grubunu seçin.

1. Sayfanın üst kısmındaki _Yapıtlar_ sekmesini seçin veya sayfanın en altındaki **Sonraki: Yapıtlar** seçeneğini belirleyin.

1. Şema örneğini oluşturan yapıtların listesini gözden geçirin. Yapıtların çoğu parametreler içerir; bu parametreleri daha sonra tanımlayacağız. Şema örneğini gözden geçirmeyi tamamladığınızda **Taslağı Kaydet**’i seçin.

## <a name="publish-the-sample-copy"></a>Örnek kopyayı yayımlama

Şimdi şema örneği kopyanız, ortamınızda oluşturulmuştur. Bu, **Taslak** modunda oluşturulur ve atanabilmesi ve dağıtılabilmesi için önce **Yayımlandı** durumunda olmalıdır. Şema örneğinin kopyası ortamınıza ve gereksinimlerinize göre özelleştirilebilir, ancak bu değişiklik bu değişikliği Azure Security kıyaslama Foundation şema öğesinden uzağa taşıyabilir.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Sayfanın üst kısmında **Şemayı yayımla**’yı seçin. Sağdaki yeni sayfada, şema örneği kopyanız için bir **Sürüm** sağlayın. Bu özellik daha sonra bir değişiklik yaptığınızda faydalı olacaktır. "Azure Security kıyaslama Foundation şema örneğinden yayımlanan ilk sürüm" gibi **değişiklik notları** sağlayın. Sayfanın alt kısmında **Yayımla** düğmesini seçin.

## <a name="assign-the-sample-copy"></a>Örnek kopyayı atama

Şema örneğinin kopyası başarıyla **yayımlandıktan** sonra, kaydedildiği yönetim grubu içindeki bir aboneliğe atanabilir. Bu adımda, şema örneği kopyasının her bir dağıtımını benzersiz hale getirmek için parametreler sağlanır.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Şemalar**’ı arayıp seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Filtreleri kullanarak şema örneği kopyanızı bulup seçin.

1. Şema tanımı sayfasının en üstünde **Şema ata**’yı seçin.

1. Şema ataması için parametre değerlerini sağlayın:

   - Temel Bilgiler
       - **Abonelikler**: şema örneğinin kopyasını kaydettiğiniz yönetim grubundaki bir veya daha fazla abonelik seçin. Birden fazla abonelik seçerseniz, girilen parametreler kullanılarak her biri için bir atama oluşturulur.
     - **Atama adı**: ad, BLUEPRINT adına göre önceden doldurulur.
       Gerektiğinde değiştirin veya olduğu gibi bırakın.
     - **Konum**: yönetilen kimliğin oluşturulacağı bölgeyi seçin.
     - Azure Blueprint bu yönetilen kimliği kullanarak tüm yapıtları atanmış şemaya dağıtır.
       Daha fazla bilgi için bkz. [Azure kaynakları için yönetilen kimlikler](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - Şema **tanımı sürümü**: şema örneğinin kopyasının **yayınlanmış** bir sürümünü seçin.

   - Kilit ataması

     Ortamınız için şema kilitleme ayarını seçin. Daha fazla bilgi için bkz. [şema kaynağı kilitleme](../../concepts/resource-locking.md).

   - Yönetilen Kimlik

     Varsayılan _sistem tarafından atanan_ yönetilen kimlik seçeneğini veya _kullanıcı tarafından atanan_ kimlik seçeneğini belirleyin.

   - Şema parametreleri

     Bu bölümde tanımlanan parametreler, tutarlılık sağlamak için şemadaki yapıtların birçoğu tarafından kullanılır.
    
     - **Kaynaklar ve kaynak grupları Için ön ek**: Bu dize tüm kaynak ve kaynak grubu adları için bir ön ek olarak kullanılır
     - **Hub adı**: hub adı
     - **Günlük tutma (gün)**: günlüklerin saklanacağı gün sayısı; ' 0 ' girildiğinde Günlükler süresiz olarak saklanır
     - **Dağıtımı dağıt**: atamanın mimarinin hub bileşenlerini dağıtıp dağıtmadığını belirtmek için ' true ' veya ' false ' girin
     - **Hub konumu**: Merkez kaynak grubunun konumu
     - **Hedef IP adresleri**: giden BAĞLANTıNıN hedef IP adresleri; IP adresleri veya IP aralığı önekleri için virgülle ayrılmış liste
     - **Ağ İzleyicisi adı**: Ağ İzleyicisi kaynağının adı
     - **Ağ İzleyicisi kaynak grubu adı**: Ağ İzleyicisi kaynak grubunun adı
     - **DDoS korumasını etkinleştir**: DDoS korumasının sanal ağda etkinleştirilip etkinleştirilmeyeceğini belirtmek için ' true ' veya ' false ' girin
     
    > [!NOTE] 
    > Ağ Izleyicisi zaten etkinse, var olan ağ Izleyicisi kaynak grubunu kullanmanız önerilir. Yapıt parametresi **Ağ İzleyicisi kaynak grubu konumu** Için mevcut ağ izleyicisi kaynak grubunun konumunu da sağlamanız gerekir.

   - Yapıt parametreleri

     Bu bölümde tanımlanan parametreler, tanımlandığı yapıt için geçerlidir. Bu parametreler, Blueprint atama sırasında tanımlandıklarından [dinamik parametrelerdir](../../concepts/parameters.md#dynamic-parameters) . Tam liste veya yapıt parametreleri ve açıklamaları için bkz. [yapıt parametreleri tablosu](#artifact-parameters-table).

1. Tüm parametreler girildikten sonra, sayfanın alt kısmında **Ata** seçeneğini belirleyin. Şema ataması oluşturulur ve yapıt dağıtımı başlar. Dağıtım kabaca bir saat sürer. Dağıtımın durumunu denetlemek için şema atamasını açın.

> [!WARNING]
> Azure Blueprints hizmeti ve yerleşik şema örnekleri **ücretsizdir**. Azure kaynakları, [ürüne göre fiyatlandırılır](https://azure.microsoft.com/pricing/). Bu şema örneği tarafından dağıtılan kaynakları çalıştırma maliyetini tahmin etmek için [fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/) kullanın.

## <a name="artifact-parameters-table"></a>Yapıt parametreleri tablosu

Aşağıdaki tabloda, şema parametrelerinin bir listesi verilmiştir:

|Yapıt adı|Yapıt türü|Parametre adı|Açıklama|
|-|-|-|-|
|Merkez kaynak grubu|Kaynak grubu|Kaynak grubu adı|Kilitli-hub adı olan ön ek ekler|
|Merkez kaynak grubu|Kaynak grubu|Kaynak grubu konumu|Kilitli-hub konumunu kullanır|
|Azure Güvenlik Duvarı şablonu|Resource Manager şablonu|Azure Güvenlik Duvarı özel IP adresi||
|Azure Log Analytics ve tanılama şablonu|Resource Manager şablonu|Log Analytics çalışma alanı konumu|Log Analytics çalışma alanının oluşturulduğu konum; `Get-AzLocation | Where-Object Providers -like 'Microsoft.OperationalInsights' | Select DisplayName` kullanılabilir bölgeleri görmek Için Azure PowersShell 'de çalıştırın|
|Azure Log Analytics ve tanılama şablonu|Resource Manager şablonu|Azure Otomasyonu hesap KIMLIĞI (isteğe bağlı)|Otomasyon hesabı kaynak KIMLIĞI; Log Analytics ile Otomasyon hesabı arasında bağlı bir hizmet oluşturmak için kullanılır|
|Azure ağ güvenlik grubu şablonu|Resource Manager şablonu|NSG akış günlüklerini etkinleştir|NSG akış günlüklerini etkinleştirmek veya devre dışı bırakmak için ' true ' veya ' false ' girin|
|Azure sanal ağ hub 'ı şablonu|Resource Manager şablonu|Sanal ağ adresi ön eki|Hub sanal ağı için sanal ağ adresi ön eki|
|Azure sanal ağ hub 'ı şablonu|Resource Manager şablonu|Güvenlik Duvarı alt ağ adresi ön eki|Hub sanal ağı için güvenlik duvarı alt ağ adresi ön eki|
|Azure sanal ağ hub 'ı şablonu|Resource Manager şablonu|Savunma alt ağ adresi ön eki|Hub sanal ağı için savunma alt ağ adresi ön eki|
|Azure sanal ağ hub 'ı şablonu|Resource Manager şablonu|Ağ geçidi alt ağ adresi ön eki|Hub sanal ağı için ağ geçidi alt ağ adresi ön eki|
|Azure sanal ağ hub 'ı şablonu|Resource Manager şablonu|Yönetim alt ağ adresi ön eki|Hub sanal ağı için yönetim alt ağ adresi ön eki|
|Azure sanal ağ hub 'ı şablonu|Resource Manager şablonu|Sıçrama kutusu alt ağ adresi öneki|Hub sanal ağı için sıçrama kutusu alt ağ adresi öneki|
|Azure sanal ağ hub 'ı şablonu|Resource Manager şablonu|Alt ağ adresi adları (isteğe bağlı)|Hub sanal ağına dağıtılacak alt ağ adları dizisi; Örneğin, "subnet1", "subnet2"|
|Azure sanal ağ hub 'ı şablonu|Resource Manager şablonu|Alt ağ adresi önekleri (isteğe bağlı)|Hub sanal ağı için isteğe bağlı alt ağların IP adresi önekleri dizisi; Örneğin, "10.0.7.0/24", "10.0.8.0/24"|
|Bağlı bileşen kaynak grubu|Kaynak grubu|Kaynak grubu adı|Kilitli-bağlı bileşen adı olan ön ekler|
|Bağlı bileşen kaynak grubu|Kaynak grubu|Kaynak grubu konumu|Kilitli-hub konumunu kullanır|
|Azure sanal ağ bağlı bileşen şablonu|Resource Manager şablonu|Bağlı bileşen dağıt|Atamanın mimarinin bağlı bileşen bileşenlerini dağıtmasının gerekip gerekmediğini belirtmek için ' true ' veya ' false ' girin|
|Azure sanal ağ bağlı bileşen şablonu|Resource Manager şablonu|Hub abonelik KIMLIĞI|Hub 'ın dağıtıldığı abonelik KIMLIĞI; Varsayılan değer, şema tanımının bulunduğu aboneliğiniz|
|Azure sanal ağ bağlı bileşen şablonu|Resource Manager şablonu|Bağlı bileşen adı|Bağlı bileşen adı|
|Azure sanal ağ bağlı bileşen şablonu|Resource Manager şablonu|Sanal ağ adresi ön eki|Bağlı bileşen sanal ağı için sanal ağ adresi ön eki|
|Azure sanal ağ bağlı bileşen şablonu|Resource Manager şablonu|Alt ağ adresi öneki|Bağlı bileşen sanal ağı için alt ağ adresi ön eki|
|Azure sanal ağ bağlı bileşen şablonu|Resource Manager şablonu|Alt ağ adresi adları (isteğe bağlı)|Bağlı bileşen sanal ağına dağıtılacak alt ağ adları dizisi; Örneğin, "subnet1", "subnet2"|
|Azure sanal ağ bağlı bileşen şablonu|Resource Manager şablonu|Alt ağ adresi önekleri (isteğe bağlı)|Bağlı bileşen sanal ağı için isteğe bağlı alt ağların IP adresi önekleri dizisi; Örneğin, "10.0.7.0/24", "10.0.8.0/24"|
|Azure sanal ağ bağlı bileşen şablonu|Resource Manager şablonu|Bağlı bileşen dağıt|Atamanın mimarinin bağlı bileşen bileşenlerini dağıtmasının gerekip gerekmediğini belirtmek için ' true ' veya ' false ' girin|
|Azure ağ Izleyicisi şablonu|Resource Manager şablonu|Ağ Izleyicisi konumu|Ağ Izleyicisi kaynağı konumu|
|Azure ağ Izleyicisi şablonu|Resource Manager şablonu|Ağ Izleyicisi kaynak grubu konumu|Ağ Izleyicisi zaten etkinse, bu parametre değeri var olan ağ Izleyicisi kaynak grubunun **konumuyla eşleşmelidir.**|

## <a name="troubleshooting"></a>Sorun giderme

Hatayla karşılaşırsanız `The resource group 'NetworkWatcherRG' failed to deploy due to the
following error: Invalid resource group location '{location}'. The Resource group already exists in
location '{location}'.` , şema parametresi **Ağ İzleyicisi kaynak grubu adının** var olan Ağ İzleyicisi kaynak grubu adını belirttiğinden ve yapıt parametresi **Ağ İzleyicisi kaynak grubu konumunun** var olan Ağ İzleyicisi kaynak grubu konumunu belirttiğinden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Azure Security kıyaslama Foundation şema örneğini dağıtma adımlarını gözden geçirdiğinize göre, mimari hakkında bilgi edinmek için aşağıdaki makaleyi ziyaret edin:

> [!div class="nextstepaction"]
> [Azure Güvenlik kıyaslama altyapısı şeması-genel bakış](./index.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.
