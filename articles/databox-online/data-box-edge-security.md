---
title: Azure Data Box Edge güvenliği | Microsoft Docs
description: Şirket içindeki ve buluttaki Azure Data Box Edge cihazınızı, hizmetinizi ve verilerinizi koruyan güvenlik ve gizlilik özelliklerini açıklar.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970894"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure Data Box Edge güvenlik ve veri koruması

Güvenlik, özellikle de teknoloji gizli veya özel verilerle kullanılıyorsa, yeni bir teknolojiyi benimseiyorsanız önemli bir konudur. Azure Data Box Edge, yalnızca yetkili varlıkların verilerinizi görüntüleyebilmenizi, değiştirmesini veya silmesini sağlamanıza yardımcı olur.

Bu makalede, her çözüm bileşeninin ve bunlarda depolanan verilerin korunmasına yardımcı olan Data Box Edge güvenlik özellikleri açıklanmaktadır.

Azure Data Box Edge, birbirleriyle etkileşime geçen dört ana bileşenden oluşur:

- **Azure 'da barındırılan Data Box Edge hizmeti**. Cihaz sırasını oluşturmak için kullandığınız yönetim kaynağı, cihazı yapılandırın ve sonra tamamlanma sırasını izleyin.
- **Data Box Edge cihaz**. Size gönderilen aktarım cihazı, şirket içi verilerinizi Azure 'a aktarabilirsiniz.
- **Cihaza bağlı istemciler/konaklar**. Altyapınızda Data Box Edge cihaza bağlanan ve korunması gereken verileri içeren istemciler.
- **Bulut depolama alanı**. Azure bulut platformunda verilerin depolandığı konum. Bu konum genellikle oluşturduğunuz Data Box Edge kaynağına bağlı depolama hesabıdır.

## <a name="data-box-edge-service-protection"></a>Data Box Edge hizmeti koruması

Data Box Edge hizmeti, Azure 'da barındırılan bir yönetim hizmetidir. Hizmet, cihazı yapılandırmak ve yönetmek için kullanılır.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Cihaz korumasını Data Box Edge

Data Box Edge cihaz, verileri yerel olarak işleyerek ve sonra Azure 'a göndererek verilerinizi dönüştürmenizi sağlayan şirket içi bir cihazdır. Cihazınız:

- Data Box Edge hizmetine erişmek için bir etkinleştirme anahtarına ihtiyaç duyuyor.
- Her zaman bir cihaz parolasıyla korunur.
- Kilitli bir cihazdır. BMC ve BIOS cihaz parola korumalıdır. BIOS, sınırlı Kullanıcı erişimi tarafından korunur.
- Güvenli önyükleme etkin.
- Windows Defender Device Guard 'ı çalıştırır. Device Guard, yalnızca kod bütünlüğü ilkeleriniz içinde tanımladığınız güvenilen uygulamaları çalıştırmanızı sağlar.

### <a name="protect-the-device-via-activation-key"></a>Etkinleştirme anahtarı aracılığıyla cihazı koruma

Yalnızca yetkili bir Data Box Edge cihazının Azure aboneliğinizde oluşturduğunuz Data Box Edge hizmetine katılmasına izin verilir. Bir cihazı yetkilendirmek için, Data Box Edge hizmeti ile cihazı etkinleştirmek üzere bir etkinleştirme anahtarı kullanmanız gerekir.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Daha fazla bilgi için bkz. [etkinleştirme anahtarı edinme](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Cihazı parola ile koruma

Parolalar yalnızca yetkili kullanıcıların verilerinize erişebilmesini güvence altına alabilir. Data Box Edge cihazların kilitli durumda olması.

Şunları yapabilirsiniz:

- Cihazın yerel Web Kullanıcı arabirimine bir tarayıcı aracılığıyla bağlanın ve ardından cihazda oturum açmak için bir parola sağlayın.
- HTTP üzerinden cihaz PowerShell arabirimine uzaktan bağlanın. Uzaktan yönetim varsayılan olarak açıktır. Daha sonra cihazda oturum açmak için cihaz parolasını sağlayabilirsiniz. Daha fazla bilgi için bkz. [Data Box Edge cihazınıza uzaktan bağlanma](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- [Parolayı değiştirmek](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access)için yerel Web Kullanıcı arabirimini kullanın. Parolayı değiştirirseniz, tüm uzaktan erişim kullanıcılarını, oturum açmada sorun yaşamadıklarından haberdar olduğunuzdan emin olun.

## <a name="protect-your-data"></a>Verilerinizi koruyun

Bu bölümde, geçişi ve depolanan verileri koruyan Data Box Edge güvenlik özellikleri açıklanmaktadır.

### <a name="protect-data-at-rest"></a>Bekleyen verileri koruma

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256 bit şifreleme, yerel verileri korumak için kullanılır.


### <a name="protect-data-in-flight"></a>Uçuş sırasında verileri koruma

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Depolama hesapları aracılığıyla verileri koruma

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Depolama hesabınızı yetkisiz kullanıcılara karşı korumaya yardımcı olmak için [depolama hesabı anahtarlarınızı](data-box-edge-manage-shares.md#sync-storage-keys) düzenli olarak döndürün ve eşitleyin.

## <a name="manage-personal-information"></a>Kişisel bilgileri yönetme

Data Box Edge hizmeti, aşağıdaki senaryolarda kişisel bilgileri toplar:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Bir paylaşıma erişebilen veya silebilen kullanıcıların listesini görüntülemek için [Data Box Edge paylaşımları yönetme](data-box-edge-manage-shares.md)bölümündeki adımları izleyin.

Daha fazla bilgi için [Güven Merkezi](https://www.microsoft.com/trustcenter)'nde Microsoft gizlilik ilkesini gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

[Data Box Edge cihazınızı dağıtma](data-box-edge-deploy-prep.md)
