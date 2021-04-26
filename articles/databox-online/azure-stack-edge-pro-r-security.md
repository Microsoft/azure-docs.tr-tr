---
title: Azure Stack Edge Pro R güvenliği | Microsoft Docs
description: Azure Stack Edge Pro R 'nizi ve Azure Stack Edge Mini R cihazlarını, hizmetini ve şirket içi ve buluttaki verileri koruyan güvenlik ve gizlilik özelliklerini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/09/2021
ms.author: alkohli
ms.openlocfilehash: f4f7e5f69e6b496395b74dbdcd58b3ada0a7f349
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285220"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Azure Stack Edge Pro R ve Azure Stack Edge Mini R için güvenlik ve veri koruması

[!INCLUDE [applies-to-pro-r-and-mini-r--skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

Güvenlik, özellikle de teknoloji gizli veya özel verilerle kullanılıyorsa, yeni bir teknolojiyi benimseiyorsanız önemli bir konudur. Azure Stack Edge Pro R ve Azure Stack Edge Mini R, yalnızca yetkili varlıkların verilerinizi görüntüleye, değiştirmesini veya silmesini sağlamanıza yardımcı olur.

Bu makalede, her çözüm bileşeninin ve bunlarda depolanan verilerin korunmasına yardımcı olan Azure Stack Edge Pro R ve Azure Stack Edge Mini R güvenlik özellikleri açıklanmaktadır.

Çözüm, birbirleriyle etkileşime geçen dört ana bileşenden oluşur:

- **Azure Stack Edge hizmeti, Azure genel veya Azure Kamu Bulutu 'nda barındırılır**. Cihaz sırasını oluşturmak için kullandığınız yönetim kaynağı, cihazı yapılandırın ve sonra tamamlanma sırasını izleyin.
- **Azure Stack Edge Rugged cihazı**. Size gönderilen Rugged, fiziksel cihaz, şirket içi verilerinizi Azure genel veya Azure Kamu bulutuna aktarabilirsiniz. Cihaz Azure Stack Edge Pro R veya Azure Stack Edge Mini R olabilir.
- **Cihaza bağlı istemciler/konaklar**. Altyapınızdaki cihaza bağlanan ve korunması gereken verileri içeren istemciler.
- **Bulut depolama alanı**. Azure bulut platformunda verilerin depolandığı konum. Bu konum genellikle oluşturduğunuz Azure Stack Edge kaynağına bağlı depolama hesabıdır.

## <a name="service-protection"></a>Hizmet koruması

Azure Stack Edge hizmeti, Azure 'da barındırılan bir yönetim hizmetidir. Hizmet, cihazı yapılandırmak ve yönetmek için kullanılır.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>Cihaz koruması

Rugged cihazı, yerel olarak işleyerek ve sonra Azure 'a göndererek verilerinizi dönüştürmenizi sağlayan şirket içi bir cihazdır. Cihazınız:

- Azure Stack Edge hizmetine erişmek için bir etkinleştirme anahtarına ihtiyaç duyuyor.
- Her zaman bir cihaz parolasıyla korunur.
- Kilitli bir cihazdır. Cihaz temel kart yönetim denetleyicisi (BMC) ve BIOS, parola korumalı. BMC sınırlı Kullanıcı erişimi tarafından korunur.
- , Cihazın yalnızca Microsoft tarafından sağlanan güvenilir yazılımları kullanarak önyükleme yapılmasını sağlayan güvenli önyükleme etkinleştirilmiştir.
- Windows Defender uygulama denetimi (WDAC) çalıştırır. WDAC, yalnızca kod bütünlüğü ilkeleriniz içinde tanımladığınız güvenilen uygulamaları çalıştırmanızı sağlar.
- Donanım tabanlı ve güvenlikle ilgili işlevler gerçekleştiren bir Güvenilir Platform Modülü (TPM) vardır. Özellikle TPM, cihazda kalıcı olması gereken gizli dizileri ve verileri yönetir ve korur.
- Cihazda yalnızca gerekli bağlantı noktaları açılır ve diğer tüm bağlantı noktaları engellenir. Daha fazla bilgi için bkz. [cihaz Için bağlantı noktası gereksinimleri](azure-stack-edge-pro-r-system-requirements.md) listesi.
- Cihaz donanımına ve yazılıma yönelik tüm erişimin de günlüğe kaydedilir. 
    - Cihaz yazılımı için, varsayılan güvenlik duvarı günlükleri cihazdan gelen ve giden trafik için toplanır. Bu Günlükler destek paketinde paketlenmiştir.
    - Cihaz donanımı için cihaz kasalarının açma ve kapatma gibi tüm cihaz Kasası olayları cihaza kaydedilir.

    Donanım ve yazılım yetkisiz giriş olaylarını ve günlüklerin nasıl alınacağını içeren belirli Günlükler hakkında daha fazla bilgi için [Gelişmiş güvenlik günlüklerini toplayın](azure-stack-edge-gpu-troubleshoot.md)bölümüne gidin.


### <a name="protect-the-device-via-activation-key"></a>Etkinleştirme anahtarı aracılığıyla cihazı koruma

Azure aboneliğinizde oluşturduğunuz Azure Stack Edge hizmetine yalnızca yetkili bir Azure Stack Edge Pro R veya Azure Stack Edge Mini R cihazının katılmasına izin verilir. Bir cihazı yetkilendirmek için, Azure Stack Edge hizmeti ile cihazı etkinleştirmek üzere bir etkinleştirme anahtarı kullanmanız gerekir.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

Daha fazla bilgi için bkz. [etkinleştirme anahtarı edinme](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Cihazı parola ile koruma

Parolalar yalnızca yetkili kullanıcıların verilerinize erişebilmesini güvence altına alabilir. Azure Stack Edge Pro R cihazlarının ön yüklemesi kilitli durumda.

Seçenekleriniz şunlardır:

- Cihazın yerel Web Kullanıcı arabirimine bir tarayıcı aracılığıyla bağlanın ve ardından cihazda oturum açmak için bir parola sağlayın.
- HTTP üzerinden cihaz PowerShell arabirimine uzaktan bağlanın. Uzaktan yönetim varsayılan olarak açıktır. Uzaktan Yönetim Ayrıca, kullanıcıların yapabileceklerinizi sınırlandırmak için yeterli yönetim (JEA) kullanmak üzere yapılandırılmıştır. Daha sonra cihazda oturum açmak için cihaz parolasını sağlayabilirsiniz. Daha fazla bilgi için bkz. [cihazınıza uzaktan bağlanma](azure-stack-edge-gpu-connect-powershell-interface.md).
- Cihazdaki Yerel uç kullanıcının, ilk yapılandırma ve sorun giderme için cihaza sınırlı erişimi vardır. Cihazda çalışan işlem iş yüklerine, veri aktarımına ve depolama alanına, buluttaki kaynak için Azure genel veya kamu portalından erişebilirsiniz.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- [Parolayı değiştirmek](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password)için yerel Web Kullanıcı arabirimini kullanın. Parolayı değiştirirseniz, tüm uzaktan erişim kullanıcılarını, oturum açmada sorun yaşamadıklarından haberdar olduğunuzdan emin olun.

### <a name="establish-trust-with-the-device-via-certificates"></a>Sertifikalar aracılığıyla cihazla güven oluşturma

Azure Stack Edge Rugged cihazı, kendi sertifikalarınızı getirip tüm genel uç noktalar için kullanılmak üzere bunları yüklemenize olanak sağlar. Daha fazla bilgi için [sertifikanızı karşıya yükleme](azure-stack-edge-gpu-manage-certificates.md#upload-certificates)bölümüne gidin. Cihazınıza yüklenebilen tüm sertifikaların bir listesi için [cihazınızdaki sertifikaları Yönet](azure-stack-edge-gpu-manage-certificates.md)' e gidin.

- Cihazınızda işlem yapılandırdığınızda, bir IoT cihazı ve bir IoT Edge cihaz oluşturulur. Bu cihazlara otomatik olarak simetrik erişim anahtarları atanır. En iyi güvenlik uygulaması olarak, bu anahtarlar IoT Hub hizmeti aracılığıyla düzenli olarak döndürülür.

## <a name="protect-your-data"></a>Verilerinizi koruma

Bu bölümde, geçişi ve depolanan verileri koruyan güvenlik özellikleri açıklanmaktadır.

### <a name="protect-data-at-rest"></a>Bekleyen verileri koruma

Cihazdaki bekleyen tüm veriler çift şifrelenir, verilere erişim denetlenir ve cihaz devre dışı bırakıldıktan sonra veriler veri disklerini güvenle silinir.

#### <a name="double-encryption-of-data"></a>Verilerin çift şifrelenmesi

Disklerinizdeki veriler iki şifreleme katmanı ile korunur:

- İlk şifreleme katmanı, veri birimlerindeki BitLocker XTS-AES 256 bit şifrelemedir.
- İkinci katman yerleşik şifrelemeye sahip sabit disklerdir.
- İşletim sistemi birimi, tek şifreleme katmanı olarak BitLocker 'a sahiptir.

> [!NOTE]
> İşletim sistemi diskinde tek katmanlı BitLocker XTS-AES-256 yazılım şifrelemesi vardır.

Cihazı etkinleştirmeden önce cihazınızda, bekleyen şifreleme 'yi yapılandırmanız gerekir. Bu gerekli bir ayardır ve bu, başarılı bir şekilde yapılandırılana kadar cihazı etkinleştiremezsiniz. 

Fabrika sırasında, cihazlar yansıma alındıktan sonra birim düzeyi BitLocker şifrelemesi etkinleştirilir. Cihazı aldıktan sonra, Rest şifrelemesini yapılandırmanız gerekir. Depolama havuzu ve birimleri yeniden oluşturulur ve REST 'ten şifrelemeyi etkinleştirmek için BitLocker anahtarları sağlayabilir ve bu sayede bekleyen verileriniz için başka bir şifreleme katmanı oluşturabilirsiniz. 

Rest 'ten şifreleme anahtarı, sağladığınız 32 karakterlik uzun Base-64 kodlu bir anahtardır ve gerçek şifreleme anahtarını korumak için bu anahtar kullanılır. Microsoft, verilerinizi koruyan bu bekleyen bu şifrelemeye erişemez. Anahtar, cihaz etkinleştirildikten sonra **bulut ayrıntıları** sayfasındaki bir anahtar dosyasına kaydedilir.

Cihaz etkinleştirildiğinde, cihaz önyüklenemediğinde Cihazdaki verileri kurtarmaya yardımcı olan kurtarma anahtarlarını içeren anahtar dosyasını kaydetmeniz istenir. Belirli kurtarma senaryoları, kaydettiğiniz anahtar dosyasını ister. Anahtar dosyası aşağıdaki Kurtarma anahtarlarına sahiptir:

- İlk şifreleme katmanının kilidini eden bir anahtar.
- Veri disklerindeki donanım şifrelemenin kilidini eden bir anahtar.
- İşletim sistemi birimlerindeki cihaz yapılandırmasını kurtarmaya yardımcı olan bir anahtar.
- Azure hizmeti üzerinden akan verileri koruyan bir anahtar.

> [!IMPORTANT]
> Anahtar dosyasını cihazın dışında güvenli bir konuma kaydedin. Cihazın önyüklemesi yapılmazsa ve anahtarınız yoksa, veri kaybına neden olabilir.



#### <a name="restricted-access-to-data"></a>Verilere kısıtlı erişim

Paylaşımlar ve depolama hesaplarında depolanan verilere erişim kısıtlıdır.

- Paylaşma verilerine erişen SMB istemcilerinin, paylaşımda ilişkili kullanıcı kimlik bilgileri olması gerekir. Bu kimlik bilgileri, paylaşma oluşturulduğunda tanımlanmıştır.
- Bir paylaşıma erişen NFS istemcilerinin, bir paylaşımın oluşturulduğu sırada IP adresinin açıkça eklenmiş olması gerekir.
- Cihazda oluşturulan uç depolama hesapları yereldir ve veri disklerindeki şifreleme tarafından korunur. Bu sınır depolama hesaplarının eşlendiği Azure depolama hesapları, sınır depolama hesabıyla ilişkili abonelikle ve 2 512 bitlik depolama erişim anahtarlarına göre korunmaktadır (Bu anahtarlar Azure depolama hesaplarınızla ilişkilenlerden farklıdır). Daha fazla bilgi için bkz. [depolama hesaplarında verileri koruma](#protect-data-in-storage-accounts).
- BitLocker XTS-AES 256 bit şifreleme, yerel verileri korumak için kullanılır.

#### <a name="secure-data-erasure"></a>Güvenli veri eriyure

Cihaz sabit bir sıfırlamaya geçtiğinde, cihazda güvenli silme gerçekleştirilir. Güvenli silme, NıST SP 800-88r1 temizleme kullanarak diskler üzerinde veri eriyonu gerçekleştirir.

### <a name="protect-data-in-flight"></a>Uçuş sırasında verileri koruma

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>Depolama hesaplarında verileri koruma

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]
- Depolama hesabınızı yetkisiz kullanıcılara karşı korumaya yardımcı olmak için [depolama hesabı anahtarlarınızı](azure-stack-edge-gpu-manage-storage-accounts.md) düzenli olarak döndürün ve eşitleyin.

## <a name="manage-personal-information"></a>Kişisel bilgileri yönetme

Azure Stack Edge hizmeti, aşağıdaki senaryolarda kişisel bilgileri toplar:

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

Bir paylaşıma erişebilen veya silebilen kullanıcıların listesini görüntülemek için [Azure Stack Edge 'de paylaşımları yönetme](azure-stack-edge-gpu-manage-shares.md)bölümündeki adımları izleyin.

Daha fazla bilgi için [Güven Merkezi](https://www.microsoft.com/trustcenter)'nde Microsoft gizlilik ilkesini gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Stack Edge Pro R cihazınızı dağıtma](azure-stack-edge-gpu-deploy-prep.md)
