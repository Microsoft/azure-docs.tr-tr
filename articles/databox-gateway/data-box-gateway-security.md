---
title: Azure Data Box Gateway güvenliği | Microsoft Docs
description: Şirket içinde ve Bulutta Azure Data Box Gateway sanal cihazınızı, hizmetinizi ve verilerinizi koruyan güvenlik ve gizlilik özelliklerini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 13d3809611714992f24a66a96c22074e69fba9bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786665"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Data Box Gateway Güvenlik ve veri koruması

Güvenlik, özellikle de teknoloji gizli veya özel verilerle kullanılıyorsa, yeni bir teknolojiyi benimseiyorsanız önemli bir konudur. Azure Data Box Gateway, yalnızca yetkili varlıkların verilerinizi görüntüleyebilmenizi, değiştirmesini veya silmesini sağlamanıza yardımcı olur.

Bu makalede, her çözüm bileşeninin ve bunlarda depolanan verilerin korunmasına yardımcı olan Azure Data Box Gateway Güvenlik özellikleri açıklanmaktadır.

Data Box Gateway çözümü, birbirleriyle etkileşime geçen dört ana bileşenden oluşur:

- **Azure 'da barındırılan Data Box Gateway hizmeti**. Cihaz sırasını oluşturmak için kullandığınız yönetim kaynağı, cihazı yapılandırın ve sonra tamamlanma sırasını izleyin.
- **Data Box Gateway cihaz**. Sağladığınız sistemin Hiper yöneticide sağladığınız sanal cihaz. Bu sanal cihaz, şirket içi verilerinizi Azure 'a aktarmak için kullanılır.
- **Cihaza bağlı istemciler/konaklar**. Altyapınızda Data Box Gateway cihaza bağlanan ve korunması gereken verileri içeren istemciler.
- **Bulut depolama alanı**. Azure bulut platformunda verilerin depolandığı konum. Bu konum genellikle oluşturduğunuz Data Box Gateway kaynağına bağlı depolama hesabıdır.

## <a name="data-box-gateway-service-protection"></a>Data Box Gateway hizmeti koruması

Data Box Gateway hizmeti, Azure 'da barındırılan bir yönetim hizmetidir. Hizmet, cihazı yapılandırmak ve yönetmek için kullanılır.

[!INCLUDE [data-box-gateway-service-protection](../../includes/data-box-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Cihaz korumasını Data Box Gateway

Data Box Gateway cihaz, sağladığınız şirket içi sistemin Hiper yöneticide sağlanan sanal bir cihazdır. Cihaz, verileri Azure 'a göndermenizi sağlar. Cihazınız:

- Azure Stack Edge Pro/Data Box Gateway hizmetine erişmek için bir etkinleştirme anahtarına ihtiyaç duyuyor.
- Her zaman bir cihaz parolasıyla korunur.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Etkinleştirme anahtarı aracılığıyla cihazı koruma

Yalnızca yetkili bir Data Box Gateway cihazının Azure aboneliğinizde oluşturduğunuz Data Box Gateway hizmetine katılmasına izin verilir. Bir cihazı yetkilendirmek için, Data Box Gateway hizmeti ile cihazı etkinleştirmek üzere bir etkinleştirme anahtarı kullanmanız gerekir.

[!INCLUDE [data-box-gateway-activation-key](../../includes/data-box-gateway-activation-key.md)]

Daha fazla bilgi için bkz. [etkinleştirme anahtarı edinme](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Cihazı parola ile koruma

Parolalar yalnızca yetkili kullanıcıların verilerinize erişebilmesini güvence altına alabilir. Data Box Gateway cihazların kilitli durumda olması.

Seçenekleriniz şunlardır:

- Cihazın yerel Web Kullanıcı arabirimine bir tarayıcı aracılığıyla bağlanın ve ardından cihazda oturum açmak için bir parola sağlayın.
- HTTP üzerinden cihazın PowerShell arabirimine uzaktan bağlanın. Uzaktan yönetim varsayılan olarak açıktır. Daha sonra cihazda oturum açmak için cihaz parolasını sağlayabilirsiniz. Daha fazla bilgi için bkz. [Data Box Gateway cihazınıza uzaktan bağlanma](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-gateway-password-best-practices](../../includes/data-box-gateway-password-best-practices.md)]
- [Parolayı değiştirmek](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)için yerel Web Kullanıcı arabirimini kullanın. Parolayı değiştirirseniz, tüm uzaktan erişim kullanıcılarına, oturum açma sorunları yaşamadıklarından emin olun.

## <a name="protect-your-data"></a>Verilerinizi koruma

Bu bölümde, geçişi ve depolanan verileri koruyan Data Box Gateway Güvenlik özellikleri açıklanmaktadır.

### <a name="protect-data-at-rest"></a>Bekleyen verileri koruma

[!INCLUDE [data-box-gateway-data-rest](../../includes/data-box-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Uçuş sırasında verileri koruma

[!INCLUDE [data-box-gateway-data-flight](../../includes/data-box-gateway-data-flight.md)]

### <a name="protect-data-using-storage-accounts"></a>Depolama hesaplarını kullanarak verileri koruma

[!INCLUDE [data-box-gateway-data-storage-accounts](../../includes/data-box-gateway-protect-data-storage-accounts.md)]

- Depolama hesabınızı yetkisiz kullanıcılara karşı korumaya yardımcı olmak için [depolama hesabı anahtarlarınızı](data-box-gateway-manage-shares.md#sync-storage-keys) düzenli olarak döndürün ve eşitleyin.

### <a name="protect-the-device-data-using-bitlocker"></a>BitLocker kullanarak cihaz verilerini koruma

Data Box Gateway sanal makinenizde sanal disklerin güvenliğini sağlamak için BitLocker 'ı etkinleştirmenizi öneririz. Varsayılan olarak, BitLocker etkin değildir. Daha fazla bilgi için bkz.

- [Hyper-V Yöneticisi 'nde şifreleme desteği ayarları](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
- [Sanal makinede BitLocker desteği](https://kb.vmware.com/s/article/2036142)

## <a name="manage-personal-information"></a>Kişisel bilgileri yönetme

Data Box Gateway hizmeti, aşağıdaki senaryolarda kişisel bilgileri toplar:

[!INCLUDE [data-box-gateway-manage-personal-data](../../includes/data-box-gateway-manage-personal-data.md)]

Bir paylaşıma erişebilen veya silebilen kullanıcıların listesini görüntülemek için [Data Box Gateway paylaşımları yönetme](data-box-gateway-manage-shares.md)bölümündeki adımları izleyin.

Daha fazla bilgi için [Güven Merkezi](https://www.microsoft.com/trustcenter)'nde Microsoft gizlilik ilkesini gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

[Data Box Gateway cihazınızı dağıtma](data-box-gateway-deploy-prep.md)
