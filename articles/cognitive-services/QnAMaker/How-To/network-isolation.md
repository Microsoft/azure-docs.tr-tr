---
title: Ağ yalıtımı
description: Kullanıcılar, Soru-Cevap Oluşturma kaynaklarına genel erişimi kısıtlayabilir.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: c2fad19bd84418d41aca1b2e0770eaa3cde488b0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043368"
---
# <a name="recommended-settings-for-network-isolation"></a>Ağ yalıtımı için önerilen ayarlar

Soru-Cevap Oluşturma kaynaklarına genel erişimi kısıtlamak için aşağıdaki adımları izlemeniz gerekir. Bilişsel hizmetler kaynağını [, sanal ağı yapılandırarak](../../cognitive-services-virtual-networks.md?tabs=portal)ortak erişimden koruyun.

## <a name="restrict-access-to-app-service-qna-runtime"></a>App Service erişimi kısıtla (QnA Runtime)

App Service 'e, erişimi kısıtlamak veya App Service Ortamı Soru-Cevap Oluşturma App Service barındıracak şekilde yapılandırmak için IP 'Leri ekleyebilirsiniz.

#### <a name="add-ips-to-app-service-allow-list"></a>App Service izin verilenler listesine IP ekleme

1. Yalnızca bilişsel hizmetler IP 'lerinden gelen trafiğe izin verin. Bunlar zaten hizmet etiketine dahil edilmiştir `CognitiveServicesManagement` . Bu, App Service 'i çağırmak ve Azure Search hizmeti 'ni uygun şekilde güncelleştirmek için API 'Leri yazma (oluşturma/güncelleştirme KB) için gereklidir. [Hizmet etiketleri hakkında daha fazla bilgi edinin.](../../../virtual-network/service-tags-overview.md)
2. Azure bot hizmeti, Soru-Cevap Oluşturma portalı vb. gibi diğer giriş noktalarına da izin verdiğinizden emin olun. tahmin için "GenerateAnswer" API erişimi.
3. IP adresi aralıklarını bir izin verilenler listesine eklemek için lütfen şu adımları izleyin:

   1. [Tüm hizmet etiketlerinin IP aralıklarını](https://www.microsoft.com/download/details.aspx?id=56519)indirin.
   2. "Biliveservicesmanagement" öğesinin IP 'lerini seçin.
   3. App Service kaynağınızın ağ bölümüne gidin ve IP 'Leri izin verilenler listesine eklemek için "erişim kısıtlaması Yapılandır" seçeneğini tıklayın.

Ayrıca, App Service için aynı olacak otomatikleştirilmiş bir betiğimiz de vardır. GitHub 'da [bir izin verilenler listesi yapılandırmak Için PowerShell betiğini](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) bulabilirsiniz. Abonelik kimliği, kaynak grubu ve gerçek App Service adını betik parametreleri olarak girbilmeniz gerekir. Betiği çalıştırmak, IP 'Leri App Service izin verilenler listesine otomatik olarak ekler.

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>App Service Ortamı Soru-Cevap Oluşturma barındırmak için yapılandırın App Service
    
App Service Ortamı (Ao), Soru-Cevap Oluşturma App Service 'i barındırmak için kullanılabilir. Lütfen aşağıdaki adımları izleyin:

1. Bir App Service Ortamı oluşturun ve "dış" olarak işaretleyin. Yönergeler için lütfen [öğreticiyi](../../../app-service/environment/create-external-ase.md) izleyin.
2.  App Service Ortamı içinde bir App Service oluşturun.
    1. App Service 'in yapılandırmasını denetleyin ve bir uygulama ayarı olarak ' BID Yendpointkey ' ekleyin. ' BID Yendpointkey ' değeri "-BID \<app-name\> yendpointkey" olarak ayarlanmalıdır. Uygulama adı App Service URL 'sinde tanımlanmıştır. Örneğin, uygulama hizmeti URL 'SI "mywebsite.myase.p.azurewebsite.net" ise, uygulama adı "mywebsite" olur. Bu durumda, ' bıı Yendpointkey ' değeri "mywebsite-bıı Yendpointkey" olarak ayarlanmalıdır.
    2. Azure Search hizmeti oluşturun.
    3. Azure Search ve uygulama ayarlarının uygun şekilde yapılandırıldığından emin olun. 
          Lütfen bu [öğreticiyi](../reference-app-service.md?tabs=v1#app-service)izleyin.
3.  App Service Ortamı ilişkili ağ güvenlik grubunu güncelleştirin
    1. Gereksinimlerinize göre önceden oluşturulmuş gelen güvenlik kurallarını güncelleştirin.
    2. ' Hizmet etiketi ' ve kaynak hizmet etiketi ' Biliveservicesmanagement ' olarak kaynağa sahip yeni bir gelen güvenlik kuralı ekleyin.
       
    ![gelen bağlantı noktası özel durumları](../media/inbound-ports.png)

4.  Azure Resource Manager kullanarak Soru-Cevap Oluşturma bilişsel hizmet örneği (Microsoft. Biliveservices/hesaplar) oluşturun; burada Soru-Cevap Oluşturma uç noktası yukarıda oluşturulan App Service uç noktaya ayarlanır (https://mywebsite.myase.p.azurewebsite.net).
    
---

## <a name="restrict-access-to-cognitive-search-resource"></a>Bilişsel Arama kaynağına erişimi kısıtla

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

Bilişsel Arama örnek, Soru-Cevap Oluşturma kaynakları oluşturulduktan sonra özel bir uç nokta ile yalıtılabilir. Özel uç nokta bağlantıları, Arama Hizmeti örneğine erişilebilen bir sanal ağ gerektirir. 

Soru-Cevap Oluşturma App Service bir App Service Ortamı kullanarak kısıtlanmışsa, Bilişsel Arama örneğine özel bir uç nokta bağlantısı oluşturmak için aynı VNet 'i kullanın. Bilişsel Arama uç noktasını Bilişsel Arama özel uç nokta IP adresine eşlemek için sanal ağda yeni bir DNS girişi oluşturun. 

App Service Ortamı QnAMaker App Service için kullanılmıyorsa, önce yeni bir VNet kaynağı oluşturun ve ardından Bilişsel Arama örneğine özel uç nokta bağlantısını oluşturun. Bu durumda, Bilişsel Arama örneğine bağlanmak için Soru-Cevap Oluşturma App Service [VNET ile tümleştirilmesi](../../../app-service/web-sites-integrate-with-vnet.md) gerekir. 

#  <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

Azure Search kaynağında [Özel uç noktalar oluşturun](../reference-private-endpoint.md) .

---