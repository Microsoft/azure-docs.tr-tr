---
title: Azure Güvenlik Duvarı Premium önizlemesi için kuruluş CA sertifikaları dağıtma ve yapılandırma
description: Azure Güvenlik Duvarı Premium önizlemesi için kuruluş CA sertifikalarını dağıtmayı ve yapılandırmayı öğrenin.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 03/18/2021
ms.author: victorh
ms.openlocfilehash: 38d83186c06eac0fc3a49834172c2a4b8542caff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590475"
---
# <a name="deploy-and-configure-enterprise-ca-certificates-for-azure-firewall-preview"></a>Azure Güvenlik Duvarı önizlemesi için kuruluş CA sertifikaları dağıtma ve yapılandırma

> [!IMPORTANT]
> Azure Güvenlik Duvarı Premium Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Azure Güvenlik Duvarı Premium önizleme, sertifika kimlik doğrulama zinciri gerektiren bir TLS İnceleme özelliği içerir. Üretim dağıtımları için, Azure Güvenlik Duvarı Premium ile kullandığınız sertifikaları oluşturmak üzere bir Kurumsal PKI kullanmanız gerekir. Azure Güvenlik Duvarı Premium önizlemesine yönelik bir ara CA sertifikası oluşturmak ve yönetmek için bu makaleyi kullanın.

Azure Güvenlik Duvarı Premium önizleme tarafından kullanılan sertifikalar hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Premium önizleme sertifikaları](premium-certificates.md).

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Azure Güvenlik Duvarı Premium önizleme ile kullanmak üzere bir sertifika oluşturmak üzere kuruluş CA 'sını kullanmak için aşağıdaki kaynaklara sahip olmanız gerekir: 

- bir Active Directory ormanı 
- Web kaydı etkin Active Directory bir sertifika hizmetleri kök CA 'sı 
- Premium katmanı güvenlik duvarı Ilkesiyle bir Azure Güvenlik Duvarı Premium 
- bir Azure Key Vault 
- Key Vault erişim Ilkesinde tanımlanan **Sertifikalar ve gizli** dizileri için okuma Izinlerine sahip yönetilen bir kimlik 

## <a name="request-and-export-a-certificate"></a>Sertifika isteme ve dışarı aktarma

1. Kök CA 'daki Web kayıt sitesine erişin, genellikle `https://<servername>/certsrv` **sertifika iste**' yi seçin.
1. **Gelişmiş Sertifika isteği**' ni seçin.
1. **Oluştur ve bu CA için bir Istek Gönder '** i seçin.
1. Alt sertifika yetkilisi şablonunu kullanarak formu doldurun.
1. İsteği gönder ve sertifikayı yükler.
1. Bu isteğin Internet Explorer 'ı kullanarak bir Windows Server 'dan yapıldığı varsayılarak, **Internet seçenekleri**' ni açın.
1. **İçerik** sekmesine gidin ve **Sertifikalar**' ı seçin.
1. Yeni verilen sertifikayı seçin ve ardından **dışarı aktar**' ı seçin.
1. Sihirbazı başlatmak için **İleri ' yi** seçin. **Evet, özel anahtarı dışarı aktar**' ı seçin ve ardından **İleri**' yi seçin.
1. . pfx dosya biçimi varsayılan olarak seçilidir. **Mümkünse sertifika yolundaki tüm sertifikaları Ekle** seçeneğinin işaretini kaldırın. Tüm sertifika zincirini dışarı aktarırsanız, Azure Güvenlik duvarına içeri aktarma işlemi başarısız olur.
1. Anahtarı korumak için bir parola atayıp onaylayın ve sonra **İleri**' yi seçin.
1. Bir dosya adı ve dışa aktarma konumu seçip **İleri**' yi seçin.
1. **Son** ' u seçin ve dışarıya aktarılmış sertifikayı güvenli bir konuma taşıyın.

## <a name="add-the-certificate-to-a-firewall-policy"></a>Sertifikayı bir güvenlik duvarı Ilkesine ekleme

1. Azure portal, Key Vault sertifikalar sayfasına gidin ve **Oluştur/al**' ı seçin.
1. Oluşturma yöntemi olarak **Içeri aktar** ' ı seçin, sertifikayı adlandırın, dışarı aktarılan. pfx dosyasını seçin, parolayı girin ve ardından **Oluştur**' u seçin.
1. Güvenlik Duvarı ilkenizin **TLS incelemesi (Önizleme)** sayfasına gidin ve yönetilen kimliği, Key Vault ve sertifikanızı seçin. 
1. **Kaydet**’i seçin.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/tls-inspection.png" alt-text="TLS incelemesi":::

## <a name="validate-tls-inspection"></a>TLS incelemesini doğrulama

1. Hedef URL veya tercih ettiğiniz FQDN için TLS incelemesini kullanarak bir uygulama kuralı oluşturun.  Örneğin: `*bing.com`.
1. Kuralın kaynak aralığı içindeki etki alanına katılmış bir makineden, hedefinizden gidin ve tarayıcınızda adres çubuğunun yanındaki kilit simgesini seçin. Sertifika, ortak bir CA yerine kuruluş sertifika yetkiliniz tarafından verildiğini göstermelidir.
1. Sertifika yolu da dahil olmak üzere daha fazla ayrıntı görüntülemek için sertifikayı görüntüleyin.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/certificate-details.png" alt-text="sertifika ayrıntıları":::
1. Log Analytics, TLS denetimine tabi olan tüm istekleri döndürmek için aşağıdaki KQL sorgusunu çalıştırın:
   ```
   AzureDiagnostics 
   | where ResourceType == "AZUREFIREWALLS" 
   | where Category == "AzureFirewallApplicationRule" 
   | where msg_s contains "Url:" 
   | sort by TimeGenerated desc
   ```
   Sonuç, incelenen trafiğin tam URL 'sini gösterir: :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/kql-query.png" alt-text="KQL sorgusu":::

## <a name="next-steps"></a>Sonraki adımlar

[Azure portal Azure Güvenlik Duvarı Premium önizlemesi](premium-portal.md)
