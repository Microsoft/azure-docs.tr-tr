---
title: Azure Event Grid ile Anahtar Kasası bildirimlerini alın ve yanıtlayın
description: Key Vault Azure Event Grid ile tümleştirmeyi öğrenin.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: b30e260b2eeb0d8af0c347996cdb51685dedd046
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133325"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Azure Event Grid ile Anahtar Kasası bildirimlerini alma ve yanıtlama (Önizleme)

Azure Event Grid ile Azure Key Vault Tümleştirme (Şu anda önizlemede), bir anahtar kasasında depolanan bir gizli dizi durumu değiştiğinde Kullanıcı bildirimine izin vermez. Bu özelliğe genel bir bakış için bkz. [Event Grid Ile izleme Key Vault](event-grid-overview.md).

Bu kılavuzda, Event Grid aracılığıyla Key Vault bildirimlerinin nasıl alınacağı ve Azure Otomasyonu aracılığıyla durum değişikliklerine nasıl yanıt verileceği açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
- Azure aboneliğinizdeki bir Anahtar Kasası. [Azure CLI kullanarak Azure Key Vault bir gizli anahtarı ayarlama ve alma](quick-create-cli.md)bölümündeki adımları izleyerek hızlı bir şekilde yeni bir Anahtar Kasası oluşturabilirsiniz.

## <a name="concepts"></a>Kavramlar

Event Grid, bulut için bir olay hizmetidir. Bu kılavuzdaki adımları izleyerek Key Vault olaylara abone olur ve etkinlikleri Otomasyon 'a yönlendirin. Anahtar kasasındaki parolalardan biri sona ermek üzereyken, Event Grid durum değişikliği bildirilir ve uç noktada bir HTTP GÖNDERISI yapar. Bir Web kancası daha sonra bir PowerShell betiğinin Otomasyon yürütmesini tetikler.

![HTTP akış çizelgesi](media/image1.png)

## <a name="create-an-automation-account"></a>Otomasyon hesabı oluşturma

[Azure Portal](https://portal.azure.com)aracılığıyla bir Otomasyon hesabı oluşturun:

1.  Portal.azure.com adresine gidin ve aboneliğinizde oturum açın.

1.  Arama kutusuna **Otomasyon hesapları**girin.

1.  Arama çubuğundaki aşağı açılan listenin **Hizmetler** bölümünde **Otomasyon hesapları**' nı seçin.

1.  **Add (Ekle)** seçeneğini belirleyin.

    ![Otomasyon hesapları bölmesi](media/image2.png)

1.  **Otomasyon hesabı ekle** bölmesine gerekli bilgileri girip **Oluştur**' u seçin.

## <a name="create-a-runbook"></a>Runbook oluşturma

Otomasyon hesabınız hazırlandıktan sonra bir runbook oluşturun.

![Runbook Kullanıcı arabirimi oluşturma](media/image3.png)

1.  Yeni oluşturduğunuz Otomasyon hesabını seçin.

1.  **Işlem Otomasyonu**altında **runbook 'ları** seçin.

1.  **Runbook oluştur**' u seçin.

1.  Runbook 'unuzu adlandırın ve Runbook türü olarak **PowerShell** ' i seçin.

1.  Oluşturduğunuz runbook 'u seçin ve ardından **Düzenle** düğmesini seçin.

1.  Aşağıdaki kodu girin (test amaçları için) ve **Yayınla** düğmesini seçin. Bu eylem, alınan POST isteğinin sonucunu döndürür.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![Runbook 'u Yayımla Kullanıcı arabirimi](media/image4.png)

## <a name="create-a-webhook"></a>Web kancası oluşturma

Yeni oluşturduğunuz runbook 'u tetiklemek için bir Web kancası oluşturun.

1.  Yeni yayımladığınız runbook 'un **kaynaklar** bölümünden **Web kancaları** ' nı seçin.

1.  **Web kancası Ekle**' yi seçin.

    ![Web kancası Ekle düğmesi](media/image5.png)

1.  **Yeni Web kancası oluştur**' u seçin.

1. Web kancasını adlandırın, bir sona erme tarihi ayarlayın ve URL 'YI kopyalayın.

    > [!IMPORTANT] 
    > URL 'YI oluşturduktan sonra görüntüleyemezsiniz. Bir kopyayı bu kılavuzun geri kalanı için erişebileceğiniz güvenli bir konumda kaydettiğinizden emin olun.

1. **Parametreler ' i seçin ve ayarları çalıştırın** ve **Tamam**' ı seçin. Herhangi bir parametre girmeyin. Bu işlem **Oluştur** düğmesini etkinleştirir.

1. **Tamam** ' ı ve ardından **Oluştur**' u seçin.

    ![Yeni Web kancası Kullanıcı arabirimi oluşturma](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Event Grid aboneliği oluşturma

[Azure Portal](https://portal.azure.com)üzerinden Event Grid bir abonelik oluşturun.

1.  [Azure portalı](https://portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true) açın.

1.  Anahtar kasanıza gidin ve **Olaylar** sekmesini seçin. Bunu göremiyorsanız [portalın önizleme sürümünü](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true)kullandığınızdan emin olun.

    ![Azure portal olaylar sekmesi](media/image7.png)

1.  **Olay aboneliği** düğmesini seçin.

1.  Abonelik için açıklayıcı bir ad oluşturun.

1.  **Event Grid şeması**' nı seçin.

1.  **Konu kaynağı** , durum değişikliklerini izlemek istediğiniz Anahtar Kasası olmalıdır.

1.  **Olay türlerine filtre uygulamak**için tüm seçenekleri seçili bırakın (**9 seçili**).

1.  **Uç Noktası Türü** için **Web kancası**'nı seçin.

1.  **Uç nokta seç ' i**seçin. Yeni bağlam bölmesinde Web kancası URL 'sini [bir Web kancası oluştur](#create-a-webhook) adımından **abone uç noktası** alanına yapıştırın.

1.  Bağlam bölmesinde **Seçimi Onayla** ' yı seçin.

1.  **Oluştur**'u seçin.

    ![Olay aboneliği oluştur](media/image8.png)

## <a name="test-and-verify"></a>Test ve doğrulama

Event Grid aboneliğinizin düzgün yapılandırıldığını doğrulayın. Bu test, [Event Grid abonelik oluşturma](#create-an-event-grid-subscription)' daki "gizli yeni sürüm oluşturuldu" bildirimine abone olduğunuzu ve bir anahtar kasasında gizli dizi yeni bir sürümünü oluşturmak için gerekli izinlere sahip olduğunuzu varsayar.

![Event Grid aboneliğinin test yapılandırması](media/image9.png)

![Gizli anahtar oluştur bölmesi](media/image10.png)

1.  Azure portal, anahtar kasanıza gidin.

1.  Yeni bir gizli dizi oluşturun. Sınama amacıyla, süre sonu tarihini bir sonraki güne ayarlayın.

1.  Anahtar kasanızın **Olaylar** sekmesinde, oluşturduğunuz Event Grid aboneliğini seçin.

1.  **Ölçümler**altında bir olayın yakalanıp yakalanmadığını denetleyin. İki olay bekleniyor: SecretNewVersion ve Secretasyncresult süre sonu. Bu olaylar, anahtar kasasındaki gizli dizinin durum değişikliğini Event Grid başarıyla yakalayacağını doğrular.

    ![Ölçümler bölmesi: yakalanan olayları denetle](media/image11.png)

1.  Otomasyon hesabınıza gidin.

1.  **Runbook 'lar** sekmesini seçin ve ardından oluşturduğunuz runbook 'u seçin.

1.  **Web kancaları** sekmesini seçin ve yeni parolayı oluştururken "son tetiklenen" zaman damgasının 60 saniye içinde olduğunu onaylayın. Bu sonuç Event Grid, Web kancasına, Anahtar Kasanızda durum değişikliğinin olay ayrıntılarıyla ve Web kancasının tetiklendiğine ilişkin bir GÖNDERI yaptığını onaylar.

    ![Web kancaları sekmesi, son tetiklenen zaman damgası](media/image12.png)

1. Runbook 'una dönün ve **genel bakış** sekmesini seçin.

1. **Son işler** listesine bakın. Bir işin oluşturulduğunu ve durumunun tamamlandığını görmeniz gerekir. Bu, Web kancası runbook 'un betiğini yürütmeye başlamasını bildiren runbook 'u tetiklediğini onaylar.

    ![Web kancası son Işleri listesi](media/image13.png)

1. Son işi seçin ve Event Grid 'dan Web kancasına gönderilen POST isteğine bakın. JSON 'ı inceleyin ve anahtar kasası ve olay türü parametrelerinin doğru olduğundan emin olun. JSON nesnesindeki "olay türü" parametresi, anahtar kasasında oluşan olayla eşleşiyorsa (Bu örnekte, Microsoft. Keykasası. Secretyaklaştığında süre sonu), test başarılı oldu.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="you-cant-create-an-event-subscription"></a>Olay aboneliği oluşturamazsınız

Azure abonelik kaynak sağlayıcılarınızda Event Grid ve Anahtar Kasası sağlayıcısını yeniden kaydettirin. Bkz. [Azure kaynak sağlayıcıları ve türleri](../azure-resource-manager/resource-manager-supported-services.md).

## <a name="next-steps"></a>Sonraki adımlar

Tebrikler! Bu adımların tümünü doğru şekilde izlediyseniz, artık Anahtar Kasanızda depolanan gizli dizi değişiklikleri için programlı olarak yanıt vermeye hazırsınız demektir.

Anahtar kasalarınızda gizli dizi durum değişikliklerini aramak için yoklama tabanlı bir sistem kullanıyorsanız, artık bu bildirim özelliğini kullanmaya başlayabilirsiniz. Ayrıca, kullanım süreleri dolduğunda sırlarınızı programlı bir şekilde yenilemek için Runbook 'inizdeki test betiğini kodla de değiştirebilirsiniz.

Daha fazla bilgi edinin:


- Genel Bakış: [Azure Event Grid Key Vault izleme (Önizleme)](event-grid-overview.md)
- Nasıl yapılır: [Anahtar Kasası gizli anahtarı değiştiğinde e-posta alma](event-grid-logicapps.md)
- [Azure Key Vault için Azure Event Grid olay şeması (Önizleme)](../event-grid/event-schema-key-vault.md)
- [Azure Key Vault genel bakış](key-vault-overview.md)
- [Azure Event Grid genel bakış](../event-grid/overview.md)
- [Azure Otomasyonu’na genel bakış](../automation/index.yml)
