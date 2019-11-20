---
title: Bulut hizmeti oluşturma ve dağıtma | Microsoft Docs
description: Azure portal kullanarak bir bulut hizmeti oluşturmayı ve dağıtmayı öğrenin.
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: gwallace
ms.openlocfilehash: 3d5b3f291eb42edc1f7999f33cf6c0879c33bcf4
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359119"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Bulut hizmeti oluşturma ve dağıtma
Azure portal bulut hizmeti oluşturmanız ve dağıtmanız için iki yol sunar: *Hızlı oluştur* ve *özel oluştur*.

Bu makalede yeni bir bulut hizmeti oluşturmak için hızlı oluşturma yönteminin nasıl kullanılacağı ve sonra Azure 'da bir bulut hizmeti paketini karşıya yüklemek ve dağıtmak için **karşıya yükleme** işleminin nasıl yapılacağı açıklanır. Bu yöntemi kullandığınızda Azure portal, hareket halindeyken tüm gereksinimlerin tamamlanmasına yönelik uygun olan bağlantıları sağlar. Bulut hizmetinizi oluştururken dağıtmaya hazırsanız, her ikisini de özel oluştur 'u kullanarak yapabilirsiniz.

> [!NOTE]
> Bulut hizmetinizi Azure DevOps ' dan yayımlamayı planlıyorsanız hızlı oluştur ' u kullanın ve Azure hızlı başlangıç veya Pano 'dan Azure DevOps yayımlaması ' nı ayarlayın. Daha fazla bilgi için bkz. Azure [DevOps kullanarak Azure 'A sürekli teslim][TFSTutorialForCloudService]veya **hızlı başlangıç** sayfası için yardım.
>
>

## <a name="concepts"></a>Kavramlar
Azure 'da bir uygulamayı bulut hizmeti olarak dağıtmak için üç bileşen gereklidir:

* **Hizmet tanımı**  
  Bulut hizmeti tanım dosyası (. csdef), rol sayısı dahil olmak üzere hizmet modelini tanımlar.
* **Hizmet yapılandırması**  
  Bulut hizmeti yapılandırma dosyası (. cscfg), bulut hizmeti ve rol örneklerinin sayısı dahil olmak üzere ayrı ayrı roller için yapılandırma ayarları sağlar.
* **Hizmet paketi**  
  Hizmet paketi (. cspkg) uygulama kodu ve yapılandırma ve hizmet tanım dosyasını içerir.

Bunlar hakkında daha fazla bilgi ve [burada](cloud-services-model-and-package.md)bir paket oluşturma hakkında daha fazla bilgi edinebilirsiniz.

## <a name="prepare-your-app"></a>Uygulamanızı hazırlama
Bir bulut hizmetini dağıtabilmeniz için önce uygulama kodunuzda ve bir bulut hizmeti yapılandırma dosyasından (. cscfg) bulut hizmeti paketini (. cspkg) oluşturmanız gerekir. Azure SDK, bu gerekli dağıtım dosyalarını hazırlamaya yönelik araçlar sağlar. [Azure İndirmeleri](https://azure.microsoft.com/downloads/) sayfasından, uygulama kodunuzu geliştirmeyi tercih ettiğiniz dilde SDK 'yı yükleyebilirsiniz.

Bir hizmet paketini dışarı aktarmadan önce üç bulut hizmeti özelliği için özel yapılandırma gerekir:

* Veri şifreleme için Güvenli Yuva Katmanı (SSL) kullanan bir bulut hizmeti dağıtmak istiyorsanız, uygulamanızı SSL için [yapılandırın](cloud-services-configure-ssl-certificate-portal.md#modify) .
* Rol örneklerine uzak masaüstü bağlantılarını yapılandırmak istiyorsanız, Uzak Masaüstü için [rolleri yapılandırın](cloud-services-role-enable-remote-desktop-new-portal.md) .
* Bulut hizmetiniz için ayrıntılı izleme yapılandırmak istiyorsanız, bulut hizmeti için Azure Tanılama etkinleştirin. *En az izleme* (varsayılan izleme düzeyi), rol örnekleri (sanal makineler) için konak işletim sistemlerinden toplanan performans sayaçlarını kullanır. *Ayrıntılı izleme* , uygulama işleme sırasında oluşan sorunların daha yakından analizine olanak tanımak için rol örnekleri içindeki performans verilerine dayalı ek ölçümler toplar. Azure Tanılama nasıl etkinleştireceğinizi öğrenmek için bkz. [Azure 'da tanılamayı etkinleştirme](cloud-services-dotnet-diagnostics.md).

Web rollerinin veya çalışan rollerinin dağıtımlarıyla bir bulut hizmeti oluşturmak için [hizmet paketini oluşturmanız](cloud-services-model-and-package.md#servicepackagecspkg)gerekir.

## <a name="before-you-begin"></a>Başlamadan önce
* Azure SDK 'sını yüklemediyseniz Azure [indirme sayfasını](https://azure.microsoft.com/downloads/)açmak için Azure **SDK 'sını yükle** ' ye tıklayın ve ardından kodunuzu geliştirmeyi tercih ettiğiniz dile ait SDK 'yı indirin. (Bunu daha sonra yapmak için bir fırsattır.)
* Herhangi bir rol örneği bir sertifika gerektiriyorsa, sertifikaları oluşturun. Bulut Hizmetleri, özel anahtara sahip bir. pfx dosyası gerektirir. Bulut hizmetini oluştururken ve dağıtırken sertifikaları Azure 'a yükleyebilirsiniz.

## <a name="create-and-deploy"></a>Oluşturma ve dağıtma
1. [Azure Portal](https://portal.azure.com/)’da oturum açın.
2. **İşlem > kaynak oluştur ' a**tıklayın, ardından aşağı kaydırarak **bulut hizmeti**' ne tıklayın.

    ![Bulut hizmetinizi yayımlayın](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. Yeni **bulut hizmeti** bölmesinde **DNS adı**için bir değer girin.
4. Yeni bir **kaynak grubu** oluşturun veya var olan bir grubu seçin.
5. Bir **Konum** seçin.
6. **Paket**' e tıklayın. Bu, **bir paket yükle** bölmesini açar. Gerekli alanları girin. Rollerinizin herhangi biri tek bir örnek içeriyorsa, **bir veya daha fazla rol tek bir örnek içeriyorsa bile dağıtıma** dikkat edin.
7. **Dağıtımı Başlat** ' ın seçili olduğundan emin olun.
8. **Tamam** ' **a tıklayarak bir paket yükle** bölmesini kapatacaktır.
9. Eklemek için herhangi bir sertifikanız yoksa **Oluştur**' a tıklayın.

    ![Bulut hizmetinizi yayımlayın](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Sertifikayı karşıya yükleyin
Dağıtım paketiniz [sertifikaları kullanacak şekilde yapılandırıldıysa](cloud-services-configure-ssl-certificate-portal.md#modify), sertifikayı Şimdi karşıya yükleyebilirsiniz.

1. **Sertifikalar**' ı seçin ve sertifika **Ekle** bölmesinde SSL sertifikası. pfx dosyasını seçin ve ardından sertifika için **parola** belirtin.
2. **Sertifika**Ekle ' ye tıklayın ve ardından Sertifika **Ekle** bölmesinde **Tamam** ' a tıklayın.
3. **Bulut hizmeti** bölmesinde **Oluştur** ' a tıklayın. Dağıtım, **hazırlık** durumuna ulaştığında, sonraki adımlara geçebilirsiniz.

    ![Bulut hizmetinizi yayımlayın](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Dağıtımınızın başarıyla tamamlandığını doğrulayın
1. Bulut hizmeti örneğine tıklayın.

    Durum, hizmetin **çalıştığını**göstermelidir.
2. **Essentials**altında, bulut hizmetinizi bir Web tarayıcısında açmak için **site URL 'sine** tıklayın.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Sonraki adımlar
* [Bulut hizmetinizin genel yapılandırması](cloud-services-how-to-configure-portal.md).
* Özel bir [etki alanı adı](cloud-services-custom-domain-name-portal.md)yapılandırın.
* [Bulut hizmetinizi yönetin](cloud-services-how-to-manage-portal.md).
* [SSL sertifikalarını](cloud-services-configure-ssl-certificate-portal.md)yapılandırma.
