---
title: Azure eşleme hizmetini kaydetme-Azure portal
description: Azure eşleme hizmetini Azure portal kullanarak nasıl kaydedeceğinizi öğrenin
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 62210105ba4ba84cff4b246c5b7cf8cc794fb5e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91534956"
---
# <a name="register-peering-service-by-using-the-azure-portal"></a>Eşleme hizmetini Azure portal kullanarak kaydedin

Azure eşleme hizmeti, Microsoft 365, Dynamics 365, hizmet olarak yazılım (SaaS) Hizmetleri, Azure veya genel İnternet üzerinden erişilebilen herhangi bir Microsoft hizmeti gibi Microsoft bulut hizmetleriyle müşteri bağlantısını geliştiren bir ağ hizmetidir.

Bu makalede, Azure portal kullanarak bir eşleme hizmeti bağlantısının nasıl kaydedileceği hakkında bilgi edineceksiniz.

Azure aboneliğiniz yoksa, şimdi bir [Hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

> 

## <a name="prerequisites"></a>Önkoşullar

Aşağıdakilere sahip olmanız gerekir:

### <a name="azure-account"></a>Azure hesabı

Geçerli ve etkin bir Microsoft Azure hesabınızın olması gerekir. Eşleme hizmeti bağlantısını kurmak için bu hesap gereklidir. Eşleme hizmeti, Azure aboneliklerindeki bir kaynaktır. 

### <a name="connectivity-provider"></a>Bağlantı sağlayıcısı

Ağınızı Microsoft Network 'e bağlamak için eşleme hizmeti edinmek üzere bir Internet hizmeti sağlayıcısı veya Internet Exchange ortağıyla birlikte çalışabilirsiniz.

Bağlantı sağlayıcılarının Microsoft ile iş [ortaklığına](location-partners.md) sahip olduğundan emin olun.



## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Bir tarayıcıdan Azure portal gidin ve Azure hesabınızla oturum açın.

## <a name="register-a-peering-service-connection"></a>Eşleme Hizmeti bağlantısını kaydetme

1. Bir eşleme hizmeti bağlantısı kaydetmek için **kaynak**  >  **eşleme hizmeti** oluştur ' u seçin.

    ![Eşleme Hizmeti'ni kaydetme](./media/peering-service-portal/peering-servicecreate.png)
1. **Eşleme hizmeti bağlantısı oluşturma** sayfasındaki **temel bilgiler** sekmesinde aşağıdaki ayrıntıları girin.

 
1. Abonelikle ilişkili aboneliği ve kaynak grubunu seçin.

   ![Eşleme temel sekmesini Kaydet](./media/peering-service-portal/peering-servicebasics.png)

1. Eşleme hizmeti örneğinin kaydedilmesi gereken bir **ad** girin.
 
1. Şimdi, sayfanın altındaki **Sonraki: yapılandırma** düğmesini seçin. **Yapılandırma** sayfası görüntülenir.

## <a name="configure-the-peering-service-connection"></a>Eşleme hizmeti bağlantısını yapılandırma

1. **Yapılandırma** sayfasında, eşleme hizmeti **konumu** açılan listesinden aynı ' ı seçerek eşleme hizmetinin etkinleştirilmesi gereken konumu seçin.

1. Eşleme hizmeti **sağlayıcısı** açılır listesinden bir sağlayıcı adı seçerek eşleme hizmetinin alınması gereken hizmet sağlayıcısını seçin.
 
1. Ön **ekler** bölümünün alt kısmındaki **Yeni ön ek oluştur** ' u seçin ve metin kutuları görüntülenir. Şimdi önek kaynağının adını ve hizmet sağlayıcısıyla ilişkili önekleri girin.

1. **Önek anahtarı** ' nı seçin ve SAĞLAYıCıNıZ (ISS veya ixp) tarafından size verilen önek anahtarını ekleyin. Bu anahtar, MS 'nin IP ön ekine ayrılan ön eki ve sağlayıcıyı doğrulamasını sağlar.
   > ![Ekran görüntüsü, önek anahtarını girebileceğiniz bir eşleme hizmeti bağlantısı oluşturma sayfasının yapılandırma sekmesini gösterir.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Sayfanın sol alt kısmındaki **gözden geçir + oluştur** düğmesini seçin. **Gözden geçir + oluştur** sayfası görünür ve Azure yapılandırmanızı doğrular.
    

1. Gösterildiği gibi **doğrulama başarılı** Iletisini gördüğünüzde **Oluştur**' u seçin.

   > ![Ekran görüntüsü, eşleme hizmeti bağlantısı oluşturma sayfasının Inceleme ve oluşturma sekmesini gösterir.](./media/peering-service-portal/peering-service-prefix.png)


1. Bir eşleme hizmeti bağlantısını kaydettikten sonra, dahil edilen öneklere ek doğrulama gerçekleştirilir. Doğrulama durumunu kaynak adının **önekler** bölümünde gözden geçirebilirsiniz. Doğrulama başarısız olursa, aşağıdaki hata iletilerinden biri görüntülenir:

   - Geçersiz eşleme hizmeti ön eki, ön ek geçerli biçim olmalıdır, yalnızca IPv4 öneki desteklenir.
   - Eşleme hizmet sağlayıcısından önek alınmadı.
   - Ön ek duyurusunun geçerli bir BGP topluluğu yok, eşleme hizmeti sağlayıcısına başvurun.
   - Yedekleme yolu bulunamadı, eşleme hizmeti sağlayıcısına başvurun.
   - Yol olarak daha uzun süre alınan önek, eşleme hizmeti sağlayıcısına başvurun.
   - Özel ile alınan ön ek, yoldaki eşleme hizmeti sağlayıcısına başvurun.

### <a name="add-or-remove-a-prefix"></a>Ön ek ekleme veya kaldırma

Ön ekler eklemek **için ön ekler sayfasında** ön ekler **Ekle** ' yi seçin.

Listelenen önek ' in yanındaki üç nokta (...) simgesini seçin ve **Sil** seçeneğini belirleyin.

### <a name="delete-a-peering-service-connection"></a>Bir eşleme hizmeti bağlantısını silme

**Tüm kaynaklar** sayfasında, eşleme hizmetindeki onay kutusunu seçin ve sayfanın en üstündeki **Sil** seçeneğini belirleyin.

> [!NOTE]
> Var olan bir ön eki değiştiremezsiniz.
>

## <a name="next-steps"></a>Sonraki adımlar

- Eşleme hizmeti bağlantısı hakkında bilgi edinmek için bkz. [eşleme hizmeti bağlantısı](connection.md).
- Eşleme hizmeti bağlantı telemetrisi hakkında bilgi edinmek için bkz. [eşleme hizmeti bağlantı telemetrisi](connection-telemetry.md).
- Telemetriyi ölçmek için bkz. [ölçüm bağlantı telemetrisi](measure-connection-telemetry.md).
- Azure PowerShell kullanarak bağlantıyı kaydetmek için bkz. [eşleme hizmeti bağlantısını kaydetme-Azure PowerShell](powershell.md).
- Azure CLı kullanarak bağlantıyı kaydetmek için bkz. [eşleme hizmeti bağlantısı kaydetme-Azure CLI](cli.md).
