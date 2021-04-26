---
title: Azure Güvenlik Duvarı Premium önizlemesini dağıtma ve yapılandırma
description: Azure Güvenlik Duvarı Premium 'u dağıtmayı ve yapılandırmayı öğrenin.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: fa106fac683619706f4be330ad1c4bff7b56f2dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721795"
---
# <a name="deploy-and-configure-azure-firewall-premium-preview"></a>Azure Güvenlik Duvarı Premium önizlemesini dağıtma ve yapılandırma

> [!IMPORTANT]
> Azure Güvenlik Duvarı Premium Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Azure Güvenlik Duvarı Premium önizleme, yüksek düzeyde hassas ve düzenlenmiş ortamlar için gereken özellikleri içeren bir sonraki nesil güvenlik duvarıdır. Aşağıdaki özellikleri içerir:

- **TLS incelemesi** -giden trafiğin şifresini çözer, verileri işler, ardından verileri şifreler ve hedefe gönderir.
- **IDPs** -ağ izinsiz giriş algılama ve önleme SISTEMI (IDPs), kötü amaçlı etkinlik için ağ etkinliklerini izlemenize, bu etkinlikle ilgili bilgileri günlüğe almanıza, raporlamayla ve isteğe bağlı olarak engellemeyi denemenize olanak tanır.
- **URL Filtresi** -Azure GÜVENLIK duvarının FQDN filtreleme özelliğini genişleterek tüm URL 'yi düşünün. Örneğin, `www.contoso.com/a/c` yerine `www.contoso.com` .
- **Web kategorileri** -Yöneticiler, kumar web siteleri, sosyal medya web siteleri ve diğerleri gibi web sitesi kategorilerine Kullanıcı erişimine izin verebilir veya erişimi reddedebilir.

Daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Premium özellikleri](premium-features.md).

Üç alt ağ ile merkezi VNet (10.0.0.0/16) olan bir test ortamını dağıtmak için bir şablon kullanacaksınız:
- çalışan alt ağı (10.0.10.0/24)
- bir Azure savunma alt ağı (10.0.20.0/24)
- bir güvenlik duvarı alt ağı (10.0.100.0/24)

Bu test ortamında kolaylık sağlaması için tek bir merkezi sanal ağ kullanılır. Üretim amacıyla, eşlenmiş VNET 'ler içeren bir [hub ve bağlı bileşen topolojisi](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) daha yaygındır.

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="Merkezi VNet topolojisi":::

Çalışan sanal makine, güvenlik duvarı üzerinden HTTP/S istekleri gönderen bir istemcdir.

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="deploy-the-infrastructure"></a>Altyapıyı dağıtma

Şablon, Azure Güvenlik Duvarı Premium için ıDPS, TLS Incelemesi, URL filtrelemesi ve Web kategorileri ile etkin bir test ortamı dağıtır:

- temel yeteneklerinin (ıDPS, TLS Incelemesi, URL filtrelemesi ve Web kategorilerinin) kolayca doğrulanmasını sağlamak için önceden tanımlanmış ayarlar içeren yeni bir Azure Güvenlik Duvarı Premium ve güvenlik duvarı Ilkesi
- Key Vault ve yönetilen bir kimlik dahil olmak üzere tüm bağımlılıkları dağıtır. Bir üretim ortamında bu kaynaklar zaten oluşturulmuş ve aynı şablonda gerekli olmayabilir.
- otomatik olarak imzalanan kök CA oluşturur ve oluşturulan Key Vault dağıtır
-  Türetilmiş bir ara CA oluşturur ve bir Windows test sanal makinesinde (WorkerVM) dağıtır
- bir savunma ana bilgisayarı (BastionHost) Ayrıca dağıtılır ve Windows test makinesine (WorkerVM) bağlanmak için kullanılabilir



[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>Güvenlik duvarını test etme

Artık ıDPS 'yi, TLS incelemesini, Web filtrelemeyi ve Web kategorilerini test edebilirsiniz.

### <a name="add-firewall-diagnostics-settings"></a>Güvenlik Duvarı Tanılama ayarları ekle

Güvenlik Duvarı günlüklerini toplamak için, güvenlik duvarı günlüklerini toplamak üzere Tanılama ayarları eklemeniz gerekir.

1. **DemoFirewall** öğesini seçin ve **izleme** altında **Tanılama ayarları**' nı seçin.
2. **Tanılama ayarı ekle**’yi seçin.
3. **Tanılama ayarı adı** için, *FW-diag* yazın.
4. **Günlük** altında **AzureFirewallApplicationRule** ve **AzureFirewallNetworkRule** öğesini seçin.
5. **Hedef ayrıntıları** altında **Log Analytics çalışma alanına gönder**' i seçin.
6. **Kaydet**’i seçin.

### <a name="idps-tests"></a>IDPS testleri

IDPS 'yi test etmek için, kendi iç Web sunucunuzu uygun bir sunucu sertifikasıyla dağıtmanız gerekir. Azure Güvenlik Duvarı Premium önizleme sertifikası gereksinimleri hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Premium önizleme sertifikaları](premium-certificates.md).

`curl`ÇEŞITLI HTTP üstbilgilerini denetlemek ve kötü amaçlı trafiğin benzetimini yapmak için ' i kullanabilirsiniz.

#### <a name="to-test-idps-for-http-traffic"></a>IDPS 'yi HTTP trafiği için test etmek için:

1. WorkerVM sanal makinesinde bir yönetici komut istemi penceresi açın.
2. Komut istemine aşağıdaki komutu yazın:

   `curl -A "BlackSun" <your web server address>`
3. Web sunucusu yanıtınızı görürsünüz.
4. Aşağıdaki iletiye benzer bir uyarı bulmak için Azure portal güvenlik duvarı ağ kuralı günlüklerine gidin:

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="Uyarı iletisi":::

   > [!NOTE]
   > Verilerin günlüklerde gösterilbaşlaması biraz zaman alabilir. Günlüklerin verileri göstermeye başlaması için en az 20 dakika bekleyin.
5. İmza 2008983 için bir imza kuralı ekleyin:

   1. **DemoFirewallPolicy** ' ı seçin ve **Ayarlar** ' ın altında **IDPs (Önizleme)** öğesini seçin.
   1. **İmza kuralları** sekmesini seçin.
   1. **Imza kimliği** altında, Aç metin kutusuna *2008983* yazın.
   1. **Mod** altında **Reddet**' i seçin.
   1. **Kaydet**’i seçin.
   1. Devam etmeden önce dağıtımın tamamlanmasını bekleyin.



6. WorkerVM 'de `curl` komutu yeniden çalıştırın:

   `curl -A "BlackSun" <your web server address>`

   HTTP isteği artık güvenlik duvarı tarafından engellendiğinden, bağlantı zaman aşımı süresi dolduktan sonra aşağıdaki çıktıyı görürsünüz:

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. Azure portal Izleyici günlüklerine gidin ve engellenen isteğin iletisini bulun.
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>IDPS 'yi HTTPS trafiği için test etmek için

Bu kıvrımlı testleri HTTP yerine HTTPS kullanarak tekrarlayın. Örnek:

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

HTTP sınamalarında sahip olduğunuz sonuçların aynısını görmeniz gerekir.

### <a name="tls-inspection-with-url-filtering"></a>URL Filtresi ile TLS Incelemesi

TLS Incelemesini URL Filtresi ile test etmek için aşağıdaki adımları kullanın.

1. Güvenlik duvarı ilkesi uygulama kurallarını düzenleyin ve kural koleksiyonuna adlı yeni bir kural ekleyin `AllowURL` `AllowWeb` . Hedef URL `www.nytimes.com/section/world` 'yi, kaynak IP adresini **\* *_, hedef türü _* URL 'yi (Önizleme)** yapılandırın, **TLS incelemesini (Önizleme)** ve protokolleri **http, https** seçin.

3. Dağıtım tamamlandığında, WorkerVM ' de bir tarayıcı açın ve adresine gidin `https://www.nytimes.com/section/world` ve HTML yanıtının tarayıcıda beklendiği şekilde görüntülendiğini doğrulayın.
4. Azure portal, uygulama kuralı Izleme günlüklerinde URL 'nin tamamını görüntüleyebilirsiniz:

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="URL 'YI gösteren uyarı iletisi":::

Bazı HTML sayfaları, reddedilen diğer URL 'Lere başvurdıklarından tamamlanmamış görünebilir. Bu sorunu gidermek için aşağıdaki yaklaşım uygulanabilir:

- HTML sayfası diğer etki alanlarına bağlantılar içeriyorsa, bu etki alanlarını bu FQDN 'lere erişime izin ver ile yeni bir uygulama kuralına ekleyebilirsiniz.
- HTML sayfası alt URL bağlantıları içeriyorsa, kuralı değiştirebilir ve URL 'ye bir yıldız işareti ekleyebilirsiniz. Örnek: `targetURLs=www.nytimes.com/section/world*`

   Alternatif olarak, kurala yeni bir URL ekleyebilirsiniz. Örnek: 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>Web kategorileri testi

Spor web sitelerine erişime izin vermek için bir uygulama kuralı oluşturalım.
1. Portalda, kaynak grubunuzu açın ve **DemoFirewallPolicy**' ı seçin.
2. **Uygulama kuralları**' nı seçin ve ardından **bir kural koleksiyonu ekleyin**.
3. **Ad** Için, *generalweb*, **Priority** *103*, **kural koleksiyonu grubu** ' nu seçin, **defaultapplicationrulecollectiongroup** yazın.
4. Ad  türü olarak  *allowspor*, **kaynak** *\** , **protokol** *http, https*, **TLS incelemesi**, **hedef türü** seçin *Web kategorileri (Önizleme)* seçin, **hedef** seçin *spor*' u seçin.
5. **Add (Ekle)** seçeneğini belirleyin.

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="Spor Web kategorisi":::
6. Dağıtım tamamlandığında,  **Workervm** ' ye gidin ve bir Web tarayıcısı açın ve adresine gidin `https://www.nfl.com` .

   NFL Web sayfasını görmeniz gerekir ve uygulama kuralı günlüğünde bir **Web kategorisi: spor** kuralı eşleştirildiği ve isteğe izin verildi.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal Azure Güvenlik Duvarı Premium önizlemesi](premium-portal.md)