---
title: Veri köpek sorunlarını giderme-Azure iş ortağı çözümleri
description: Bu makalede, Azure 'da veri Köpede sorun giderme hakkında bilgi sağlanır.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 0e3c82f711de4cd9710c9aafe798a986e3403ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563717"
---
# <a name="troubleshooting-datadog-on-azure"></a>Azure'da Datadog Sorunlarını Giderme

Bu belge, Dataköpek kullanan çözümlerinizde sorun giderme hakkında bilgiler içerir.

## <a name="purchase-errors"></a>Satın alma hataları

* Geçerli bir kredi kartı Azure aboneliğine bağlı olmadığından veya bir ödeme yöntemi abonelikle ilişkili olmadığından satın alma başarısız olur.

  Farklı bir Azure aboneliği kullanın. Ya da, abonelik için kredi kartı veya ödeme yöntemi ekleyebilir veya güncelleştirebilirsiniz. Daha fazla bilgi için bkz. [kredi ve ödeme yöntemini güncelleştirme](../../cost-management-billing/manage/change-credit-card.md).

* EA aboneliği Market satın alımlara izin vermez.

  Farklı bir abonelik kullanın. Ya da EA aboneliğinizin Market satın alma için etkinleştirilip etkinleştirilmediğini denetleyin. Daha fazla bilgi için bkz. [Market satın alımlarını etkinleştirme](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases). Bu seçenekler sorunu çözmezse, [veri köpek desteğiyle](https://www.datadoghq.com/support)iletişime geçin.

## <a name="unable-to-create-datadog-resource"></a>Dataköpek kaynağı oluşturulamıyor

Azure veri köpek tümleştirmesini ayarlamak için Azure aboneliğinde **sahip** erişiminizin olması gerekir. Kurulum 'u başlatmadan önce uygun erişime sahip olduğunuzdan emin olun.

## <a name="single-sign-on-errors"></a>Çoklu oturum açma hataları

**Çoklu oturum açma ayarları kaydedilemiyor** -bu hata, DATAKÖPEK SAML tanımlayıcısı kullanan başka bir kurumsal uygulamanın bulunduğu yerde oluşur. Hangi uygulamanın kullandığını bulmak için temel SAML yapılandırması bölümünde **Düzenle** ' yi seçin.

Bu sorunu çözmek için, diğer uygulamayı devre dışı bırakın ya da diğer uygulamayı kurumsal uygulama olarak kullanarak SAML SSO 'yu Dataköpek olarak ayarlayın. Diğer uygulamayı kullanmaya karar verirseniz, uygulamanın [gerekli ayarlara](create.md#configure-single-sign-on)sahip olduğundan emin olun.

**Uygulama çoklu oturum açma ayarı sayfasında gösterilmiyor** , ilk olarak uygulama kimliği ' ni arayın. Sonuç gösterilmezse, uygulamanın SAML ayarlarını kontrol edin. Izgara yalnızca doğru SAML ayarlarına sahip uygulamaları gösterir. 

Tanımlayıcı URL 'SI olmalıdır `https://us3.datadoghq.com/account/saml/metadata.xml` .

Yanıt URL 'SI olmalıdır `https://us3.datadoghq.com/account/saml/assertion` .

Aşağıdaki görüntüde doğru değerler gösterilmektedir.
  
:::image type="content" source="media/troubleshoot/troubleshooting.png" alt-text="AAD 'de Dataköpek uygulaması için SAML ayarlarını kontrol edin." border="true":::

**Kiracıya davet edilen Konuk kullanıcılar çoklu oturum açma 'ya erişemez** -bazı kullanıcıların Azure Portal iki e-posta adresi vardır. Genellikle, bir e-posta Kullanıcı asıl adı (UPN), diğer e-posta ise alternatif bir e-postadır.

Konuk Kullanıcı davet edildiğinde, ana kiracı UPN 'sini kullanın. UPN 'yi kullanarak, çoklu oturum açma işlemi sırasında e-posta adresini eşitlenmiş halde tutabilirsiniz. UPN 'yi, Kullanıcı Azure portal sağ üst köşesindeki e-posta adresini arayarak bulabilirsiniz.
  
## <a name="logs-not-being-emitted"></a>Günlükler yayınlanmıyor

Yalnızca Azure Izleyici kaynak günlüğü kategorilerinde listelenen kaynaklar, günlükleri veri köpeye yayar. Kaynağın günlükleri Dataköpek 'e yayıp yaymadığını doğrulamak için, söz konusu kaynak için Azure tanılama ayarına gidin. Bir Dataköpek tanılama ayarı olduğunu doğrulayın.

:::image type="content" source="media/troubleshoot/diagnostic-setting.png" alt-text="Azure kaynağında dataköpek tanılama ayarı" border="true":::

## <a name="metrics-not-being-emitted"></a>Ölçümler yayınlanmıyor

Dataköpek kaynağına uygun Azure aboneliğinde bir **Izleme okuyucu** rolü atanır. Bu rol, veri köpek kaynağının ölçümleri toplamasını ve bu ölçümleri veri Köbir kişiye göndermesini sağlar.

Kaynağın doğru rol atamasına sahip olduğunu doğrulamak için Azure portal açın ve aboneliği seçin. Sol bölmede **Access Control (IAM)** öğesini seçin. Dataköpek kaynak adını arayın. Aşağıda gösterildiği gibi, Dataköpek kaynağının **Izleme okuyucu** rolü atamasını içerdiğinden emin olun.

:::image type="content" source="media/troubleshoot/datadog-role-assignment.png" alt-text="Azure aboneliğindeki dataköpek rolü ataması" border="true":::

## <a name="datadog-agent-installation-fails"></a>Dataköpek Aracısı yüklemesi başarısız oluyor

Azure Dataköpek tümleştirmesi, bir sanal makineye veya App Service 'e Dataköpek Aracısı 'nı yüklemenize olanak sağlar. Dataköpek Aracısı 'nı yapılandırmak için API anahtarları ekranında **varsayılan anahtar** olarak seçilen API anahtarı kullanılır. Varsayılan anahtar seçilmezse, Dataköpek Aracısı yüklemesi başarısız olur.

Dataköpek Aracısı yanlış bir anahtarla yapılandırıldıysa, API anahtarları ekranına gidin ve **varsayılan anahtarı** değiştirin. Sanal makineyi yeni API anahtarlarıyla yapılandırmak için Dataköpek Aracısı 'nı kaldırmanız ve yeniden yüklemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Veri köpek [örneğinizi yönetme](manage.md) hakkında bilgi edinin.
