---
title: ISO 27001 paylaşılan hizmetleri şema örnek-dağıt adımları
description: Şema yapıt parametresi ayrıntıları dahil olmak üzere ISO 27001 paylaşılan hizmetleri şeması örneği için adımları dağıtın.
ms.date: 03/14/2019
ms.topic: sample
ms.openlocfilehash: 54f53e13e6d4561cb46f62318203c41c504ee60b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037342"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>ISO 27001 paylaşılan hizmetleri şema örneğini dağıtma

Azure şemaları ISO 27001 paylaşılan hizmetleri şema örneğini dağıtmak için aşağıdaki adımlar gerçekleştirilmelidir:

> [!div class="checklist"]
> - Örnekten yeni bir şema oluştur
> - Örnek kopyanızı **yayımlandı** olarak işaretleyin
> - Şema kopyanızı mevcut bir aboneliğe atama

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

## <a name="create-blueprint-from-sample"></a>Örnekten şema oluştur

İlk olarak, bir başlangıç olarak örneği kullanarak ortamınızda yeni bir şema oluşturarak şema örneğini uygulayın.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Soldaki **Başlarken** sayfasında, şema _Oluştur_altında **Oluştur** düğmesini seçin.

1. **Iso 27001: paylaşılan hizmetler** şeması ' nı _diğer örnekler_ altında bulun ve **Bu örneği kullan**' ı seçin.

1. Şema örneği _hakkında temel bilgileri_ girin:

   - **Şema adı**: ISO 27001 paylaşılan hizmetleri şema Sample kopyasına bir ad verin.
   - **Tanım konumu**: üç noktayı kullanın ve örneğin kopyasını kaydetmek için yönetim grubunu seçin.

1. Sayfanın üst kısmındaki _yapıtlar_ sekmesini veya sonraki: sayfanın en altındaki **yapıtları** seçin.

1. Şema örneğini oluşturan yapıtların listesini gözden geçirin. Yapıtların çoğunda, daha sonra tanımlayacağımız parametreler vardır. Şema örneğini gözden geçirmeyi bitirdiğinizde **Taslağı kaydet** ' i seçin.

## <a name="publish-the-sample-copy"></a>Örnek kopyayı Yayımla

Şema örneğinin kopyası artık ortamınızda oluşturulmuştur. **Taslak** modunda oluşturulur ve atanmadan ve dağıtılmadan önce **yayımlanmaları** gerekir. Şema örneğinin kopyası ortamınıza ve gereksinimlerinize göre özelleştirilebilir, ancak bu değişiklik bu değişikliği ISO 27001 standbundan uzağa taşıyabilir.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Şemayı kullanarak şema örneğinin kopyasını bulun ve ardından seçin.

1. Sayfanın üst kısmındaki şemayı **Yayımla** ' yı seçin. Sağ taraftaki yeni sayfada, şema örneğinin kopyası için bir **Sürüm** belirtin. Daha sonra bir değişiklik yaparsanız, bu özellik için faydalıdır. "ISO 27001 şema örneğinden yayımlanan ilk sürüm" gibi **değişiklik notları** sağlayın. Ardından sayfanın alt kısmında **Yayımla** ' yı seçin.

## <a name="assign-the-sample-copy"></a>Örnek kopyayı atama

Şema örneğinin kopyası başarıyla **yayımlandıktan**sonra, kaydedildiği yönetim grubu içindeki bir aboneliğe atanabilir. Bu adım, her bir şema örneğinin kopyasının her dağıtımını yapmak için parametrelerin sağlandığı yerdir.

1. Sol bölmedeki **tüm hizmetler** ' i seçin. **Şemaları**arayın ve seçin.

1. Sol taraftaki **Blueprint tanımları** sayfasını seçin. Şemayı kullanarak şema örneğinin kopyasını bulun ve ardından seçin.

1. Şema tanım sayfasının en üstünde şema **ata** ' yı seçin.

1. Şema atamasının parametre değerlerini sağlayın:

   - Temel Bilgiler

     - **Abonelikler**: şema örneğinin kopyasını kaydettiğiniz yönetim grubundaki bir veya daha fazla abonelik seçin. Birden fazla abonelik seçerseniz, girilen parametreleri kullanarak her biri için bir atama oluşturulur.
     - **Atama adı**: ad, BLUEPRINT adına göre önceden doldurulur.
       Gerektiğinde değiştirin veya olduğu gibi bırakın.
     - **Konum**: yönetilen kimliğin oluşturulacağı bölgeyi seçin. Azure Blueprint bu yönetilen kimliği kullanarak tüm yapıtları atanmış şemaya dağıtır. Daha fazla bilgi için bkz. [Azure kaynakları için yönetilen kimlikler](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - Şema **tanımı sürümü**: şema örneğinin kopyasının **yayınlanmış** bir sürümünü seçin.

   - Kilit ataması

     Ortamınız için BLUEPRINT Lock ayarını seçin. Daha fazla bilgi için bkz. [şema kaynağı kilitleme](../../concepts/resource-locking.md).

   - Yönetilen Kimlik

     Varsayılan sistem tarafından _atanmış_ yönetilen kimlik seçeneğini bırakın.

   - Şema parametreleri

     Bu bölümde tanımlanan parametreler, tutarlılığı sağlamak için şema tanımındaki birçok yapıtlar tarafından kullanılır.

     - **Kuruluş adı**: kuruluşunuz için kısa bir ad girin. Bu özellik birincil olarak adlandırma kaynakları için kullanılır.
     - **Paylaşılan hizmetler alt ağ adresi ön eki**: dağıtılan kaynakları birbirine DAĞıTMAK için CIDR Gösterimi değerini sağlayın.
     - **Paylaşılan hizmetler konumu**: yapıtların hangi konuma dağıtıldığını belirler. Tüm hizmetler tüm konumlarda kullanılabilir değildir. Bu tür hizmetleri dağıtan yapıtlar, bu yapıtın dağıtılacağı konum için bir parametre seçeneği sağlar.
     - **Izin verilen konum (ilke: ıso 27001 Için Blueprint girişimi)** : kaynak grupları ve kaynaklar için izin verilen konumları gösteren değer.
     - **VM aracıları için Log Analytics çalışma alanı (ilke: ıso 27001 Için Blueprint girişimi)** : bir çalışma ALANıNıN kaynak kimliğini belirtir. Bu parametre, kaynak KIMLIĞINI oluşturmak için bir `concat` işlevi kullanır.

   - Yapıt parametreleri

     Bu bölümde tanımlanan parametreler, tanımlanan yapıt için geçerlidir. Bu parametreler, Blueprint atama sırasında tanımlandıklarından [dinamik parametrelerdir](../../concepts/parameters.md#dynamic-parameters) . Tam liste veya yapıt parametreleri ve açıklamaları için bkz. [yapıt parametreleri tablosu](#artifact-parameters-table).

1. Tüm parametreler girildikten sonra sayfanın alt kısmındaki **ata** ' yı seçin. Şema ataması oluşturulur ve yapıt dağıtımı başlar. Dağıtım kabaca bir saat sürer. Dağıtımın durumunu denetlemek için, BLUEPRINT atamasını açın.

> [!WARNING]
> Azure şemaları hizmeti ve yerleşik şema örnekleri **ücretsiz olarak ücretsizdir**. Azure kaynakları [ürüne göre fiyatlandırılır](https://azure.microsoft.com/pricing/). Bu şema örneği tarafından dağıtılan çalışan kaynakların maliyetini tahmin etmek için [fiyatlandırma hesaplayıcısını](https://azure.microsoft.com/pricing/calculator/) kullanın.

## <a name="artifact-parameters-table"></a>Yapıt parametreleri tablosu

Aşağıdaki tabloda, şema yapıt parametrelerinin bir listesi verilmiştir:

|Yapıt adı|Yapıt türü|Parametre adı|Açıklama|
|-|-|-|-|
|\[Preview\]: Linux VM Ölçek Kümeleri için Log Analytics aracısı dağıtma (VMSS)|İlke ataması|İsteğe bağlı: kapsama eklemek için desteklenen Linux işletim sistemini destekleyen VM görüntülerinin listesi|Seçim Varsayılan değer _["none"]_ değeridir.|
|\[Önizleme\]: Linux sanal makineleri için Log Analytics aracısını dağıtmayı|İlke ataması|İsteğe bağlı: kapsama eklemek için desteklenen Linux işletim sistemini destekleyen VM görüntülerinin listesi|Seçim Varsayılan değer _["none"]_ değeridir.|
|\[Preview\]: Windows VM Ölçek Kümeleri için Log Analytics aracısı dağıtma (VMSS)|İlke ataması|İsteğe bağlı: kapsama eklemek için desteklenen Windows işletim sistemini destekleyen VM görüntülerinin listesi|Seçim Varsayılan değer _["none"]_ değeridir.|
|\[Önizleme\]: Windows Vm'leri için Log Analytics aracısını dağıtmayı|İlke ataması|İsteğe bağlı: kapsama eklemek için desteklenen Windows işletim sistemini destekleyen VM görüntülerinin listesi|Seçim Varsayılan değer _["none"]_ değeridir.|
|İzin verilen kaynak türleri|İlke ataması|İzin verilen kaynak türleri|Dağıtılmasına izin verilen kaynak türleri listesi. Bu liste, paylaşılan hizmetlerde dağıtılan tüm kaynak türlerinden oluşur.|
|İzin verilen depolama hesabı SKU'ları|İlke ataması|İzin verilen depolama SKU 'Ları|İzin verilen tanılama günlüğü depolama hesabı SKU 'Larının listesi. Varsayılan değer _["Standard_LRS"]_ değeridir.|
|İzin verilen sanal makine SKU 'Ları|İlke ataması|Dağıtılmasına izin verilen sanal makine SKU 'Larının listesi. Varsayılan değer _["Standard_DS1_v2", "Standard_DS2_v2"]_ .|
|ISO 27001 için Blueprint girişimi|İlke ataması|Tanılama günlüklerini denetlemek için kaynak türleri|Tanılama günlüğü ayarı etkinleştirilmemişse denetlenecek kaynak türleri listesi. Kabul edilebilir değerler, [Azure izleyici tanılama günlükleri şemalarında](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)bulunabilir.|
|Log Analytics kaynak grubu|Kaynak grubu|Ad|**Kilitli** -kaynak grubunu benzersiz hale getirmek için `-sharedsvsc-log-rg` ile **kuruluş adını** art arda ekler.|
|Log Analytics kaynak grubu|Kaynak grubu|Konum|**Kilitli** -şema parametresini kullanır.|
|Log Analytics şablonu|Resource Manager şablonu|Hizmet katmanı|Log Analytics çalışma alanının katmanını ayarlar. Varsayılan değer _PerNode_değeridir.|
|Log Analytics şablonu|Resource Manager şablonu|Günlük tutma gün sayısı|Gün cinsinden veri saklama. Varsayılan değer _365_' dir.|
|Log Analytics şablonu|Resource Manager şablonu|Konum|Log Analytics çalışma alanını oluşturmak için kullanılan bölge. Varsayılan değer _Batı ABD 2_.|
|Ağ kaynak grubu|Kaynak grubu|Ad|**Kilitli** -kaynak grubunu benzersiz hale getirmek için `-sharedsvcs-net-rg` ile **kuruluş adını** art arda ekler.|
|Ağ kaynak grubu|Kaynak grubu|Konum|**Kilitli** -şema parametresini kullanır.|
|Azure Güvenlik Duvarı şablonu|Resource Manager şablonu|Azure Güvenlik Duvarı özel IP 'si|[Azure Güvenlik duvarının](../../../../firewall/overview.md)özel IP 'sini yapılandırır. Bu değer, paylaşılan hizmetler alt ağında varsayılan yol tablosu olarak da kullanılır. **Azure Güvenlik Duvarı alt ağ adresi ön eki**'NDE tanımlanan CIDR gösteriminin bir parçası olmalıdır. Varsayılan değer _10.0.4.4_' dir.|
|Azure Güvenlik Duvarı şablonu|Resource Manager şablonu|Günlük tutma gün sayısı|Gün cinsinden veri saklama. Varsayılan değer _365_' dir.|
|Ağ güvenlik grubu şablonu|Resource Manager şablonu|Günlük tutma gün sayısı|Gün cinsinden veri saklama. Varsayılan değer _365_' dir.|
|Sanal ağ ve yol tablosu şablonu|Resource Manager şablonu|Sanal ağ adresi ön eki|Sanal ağın CıDR gösterimi. Varsayılan değer _10.0.0.0/16_' dır.|
|Sanal ağ ve yol tablosu şablonu|Resource Manager şablonu|Sanal ağ DDoS korumasını etkinleştir|Sanal ağ için DDoS korumasını yapılandırır. Varsayılan değer _true_'dur.|
|Sanal ağ ve yol tablosu şablonu|Resource Manager şablonu|Paylaşılan hizmetler alt ağ adresi ön eki|Paylaşılan hizmetler alt ağının CıDR gösterimi. Varsayılan değer _10.0.0.0/24_' dir.|
|Sanal ağ ve yol tablosu şablonu|Resource Manager şablonu|DMZ alt ağ adresi öneki|DMZ alt ağı için CıDR gösterimi. Varsayılan değer _10.0.1.0/24_' dir.|
|Sanal ağ ve yol tablosu şablonu|Resource Manager şablonu|Application Gateway alt ağ adresi öneki|Uygulama ağ geçidi alt ağının CıDR gösterimi. Varsayılan değer _10.0.2.0/24_' dir.|
|Sanal ağ ve yol tablosu şablonu|Resource Manager şablonu|Sanal ağ geçidi alt ağ adresi ön eki|Sanal ağ geçidi alt ağının CıDR gösterimi. Varsayılan değer _10.0.3.0/24_' dir.|
|Sanal ağ ve yol tablosu şablonu|Resource Manager şablonu|Azure Güvenlik Duvarı alt ağ adresi ön eki|[Azure Güvenlik Duvarı](../../../../firewall/overview.md) alt ağı için CIDR Gösterimi. **Azure Güvenlik Duvarı özel IP** parametresini içermelidir.|
|Key Vault kaynak grubu|Kaynak grubu|Ad|**Kilitli** -kaynak grubunu benzersiz hale getirmek için `-sharedsvcs-kv-rg` ile **kuruluş adını** art arda ekler.|
|Key Vault kaynak grubu|Kaynak grubu|Konum|**Kilitli** -şema parametresini kullanır.|
|Key Vault şablonu|Resource Manager şablonu|JumpBox Yönetici Kullanıcı adı|Sıçrama kutusu için Kullanıcı adı. Sıçrama **kutusu şablonundaki**aynı özellik değeriyle eşleşmelidir. Varsayılan değer _JB-admin-User_değeridir.|
|Key Vault şablonu|Resource Manager şablonu|JumpBox yönetici SSH anahtarı veya parolası|Sıçrama kutusundaki hesabın anahtar veya parolası. Sıçrama **kutusu şablonundaki**aynı özellik değeriyle eşleşmelidir. Varsayılan değer olmadığından boş bırakılamaz.|
|Key Vault şablonu|Resource Manager şablonu|Etki alanı yöneticisi Kullanıcı adı|Active Directory VM 'ye erişmek ve diğer VM 'Leri bir etki alanına katmak için kullanılan Kullanıcı adı. **Active Directory Domain Services şablonundaki** **Domain Admin kullanıcı** özelliği değeriyle eşleşmelidir. Varsayılan değer _etki alanı-yönetici-kullanıcı_' dır.|
|Key Vault şablonu|Resource Manager şablonu|Etki alanı yöneticisi parolası|Etki alanı yöneticisi kullanıcısının parolası. Varsayılan değer olmadığından boş bırakılamaz.|
|Key Vault şablonu|Resource Manager şablonu|AAD nesne KIMLIĞI|Key Vault örneğine erişim gerektiren hesabın AAD nesne tanımlayıcısı. Varsayılan değer olmadığından boş bırakılamaz. Azure portal bu değeri bulmak için, _Hizmetler_altında "kullanıcılar" ı arayın ve seçin. Hesap adını filtrelemek için _ad_ kutusunu kullanın ve bu hesabı seçin. _Kullanıcı profili_ sayfasında, _nesne kimliğinin_yanındaki "kopyalamak için tıklayın" simgesini seçin.  |
|Key Vault şablonu|Resource Manager şablonu|Günlük tutma gün sayısı|Gün cinsinden veri saklama. Varsayılan değer _365_' dir.|
|Key Vault şablonu|Resource Manager şablonu|Key Vault SKU 'SU|Oluşturulan Key Vault SKU 'sunu belirtir. Varsayılan değer _Premium_' dur.|
|Atlama kutusu kaynak grubu|Kaynak grubu|Ad|**Kilitli** -kaynak grubunu benzersiz hale getirmek için `-sharedsvcs-jb-rg` ile **kuruluş adını** art arda ekler.|
|Atlama kutusu kaynak grubu|Kaynak grubu|Konum|**Kilitli** -şema parametresini kullanır.|
|Sıçrama kutusu şablonu|Resource Manager şablonu|JumpBox Yönetici Kullanıcı adı|Sıçrama kutusu VM 'lerine erişmek için kullanılan Kullanıcı adı. **Key Vault şablonunda**aynı özellik değeriyle eşleşmelidir. Varsayılan değer _JB-admin-User_değeridir.|
|Sıçrama kutusu şablonu|Resource Manager şablonu|JumpBox yönetici parolası (Key Vault kaynak KIMLIĞI)|Key Vault kaynak KIMLIĞI. "/Subscriptions/{SubscriptionID}/resourceGroups/{orgName}-sharedsvcs-kV-RG/Providers/Microsoft.KeyVault/Vaults/{orgName}-sharedsvcs-kV" kullanın ve `{subscriptionId}` abonelik kimliğinizle ve `{orgName}` **kuruluş adı** şema parametresiyle değiştirin.|
|Sıçrama kutusu şablonu|Resource Manager şablonu|JumpBox yönetici parolası (Key Vault gizli adı)|Sıçrama kutusu yöneticisinin Kullanıcı adı. **Key Vault Template** özelliği **JumpBox Yönetici Kullanıcı adı**' nın değeriyle eşleşmelidir.|
|Sıçrama kutusu şablonu|Resource Manager şablonu|JumpBox Işletim sistemi|Sıçrama kutusu sanal makinesinin işletim sistemini belirler. Varsayılan değer _Windows_' dır.|
|Active Directory Domain Services kaynak grubu|Kaynak grubu|Ad|**Kilitli** -kaynak grubunu benzersiz hale getirmek için `-sharedsvcs-adds-rg` ile **kuruluş adını** art arda ekler.|
|Active Directory Domain Services kaynak grubu|Kaynak grubu|Konum|**Kilitli** -şema parametresini kullanır.|
|Active Directory Domain Services şablonu|Resource Manager şablonu|Etki alanı yöneticisi Kullanıcı adı|Atlama kutusu ekleme için Kullanıcı adı. **Key Vault şablonunda**aynı özellik değeriyle eşleşmelidir. Varsayılan değer _ekler-admin-User_.|
|Active Directory Domain Services şablonu|Resource Manager şablonu|Etki alanı yönetici parolası (Key Vault kaynak KIMLIĞI)|Key Vault kaynak KIMLIĞI. "/Subscriptions/{SubscriptionID}/resourceGroups/{orgName}-sharedsvcs-kV-RG/Providers/Microsoft.KeyVault/Vaults/{orgName}-sharedsvcs-kV" kullanın ve `{subscriptionId}` abonelik kimliğinizle ve `{orgName}` **kuruluş adı** şema parametresiyle değiştirin.|
|Active Directory Domain Services şablonu|Resource Manager şablonu|Etki alanı yönetici parolası (Key Vault gizli adı)|Etki alanı yöneticisinin Kullanıcı adı. **Key Vault şablon** özelliği **etki alanı Yönetici Kullanıcı adı**' nın değeriyle eşleşmelidir.|
|Active Directory Domain Services şablonu|Resource Manager şablonu|Etki alanı adı|Örnek tarafından oluşturulan Active Directory adı. Varsayılan değer _contoso.com_' dir.|
|Active Directory Domain Services şablonu|Resource Manager şablonu|Etki alanı yöneticisi kullanıcısı|Yönetici AD hesabı için Kullanıcı adı ve cihazları AD etki alanına katma. **Key Vault şablonundaki** **ad Yöneticisi Kullanıcı adı** özellik değeriyle eşleşmelidir. Varsayılan değer _etki alanı-yönetici-kullanıcı_' dır.|
|Active Directory Domain Services şablonu|Resource Manager şablonu|Etki alanı yöneticisi parolası|Parolayı depolamak için Key Vault ayrıntılarını ayarlayın. Varsayılan değer olmadığından boş bırakılamaz.|

## <a name="next-steps"></a>Sonraki adımlar

ISO 27001 paylaşılan hizmetleri şema örneğini dağıtma adımlarını gözden geçirdiğinize göre, mimari ve denetim eşlemesi hakkında bilgi edinmek için aşağıdaki makaleleri ziyaret edin:

> [!div class="nextstepaction"]
> [ISO 27001 paylaşılan hizmetler şeması-genel bakış](./index.md)
> [ISO 27001 paylaşılan hizmetler şema-Control Mapping](./control-mapping.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.
