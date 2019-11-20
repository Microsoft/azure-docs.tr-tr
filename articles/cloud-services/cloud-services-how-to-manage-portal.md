---
title: Ortak bulut hizmeti yönetim görevleri | Microsoft Docs
description: Azure portal Cloud Services yönetmeyi öğrenin. Bu örnekler Azure portal kullanır.
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: gwallace
ms.openlocfilehash: 8ec7784fb51d0fa4de2563f76444b0b5e5f34902
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359603"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Azure portal Cloud Services yönetme
Azure portal **Cloud Services** alanında şunları yapabilirsiniz:

* Bir hizmet rolünü veya dağıtımını güncelleştirin.
* Hazırlanmış bir dağıtımı üretime yükseltin.
* Kaynak bağımlılıklarını görebilmeniz ve kaynakları birlikte ölçeklendirmeniz için kaynakları bulut hizmetinize bağlayın.
* Bulut hizmetini veya bir dağıtımı silin.

Bulut hizmetinizi ölçeklendirme hakkında daha fazla bilgi için bkz. [portalda bir bulut hizmeti için otomatik ölçeklendirmeyi yapılandırma](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Bulut hizmeti rolünü veya dağıtımını güncelleştirme
Bulut hizmetiniz için uygulama kodunu güncelleştirmeniz gerekiyorsa, bulut hizmeti dikey penceresinde **Güncelleştir** ' i kullanın. Tek bir rolü veya tüm rolleri güncelleştirebilirsiniz. Güncelleştirmek için yeni bir hizmet paketi veya hizmet yapılandırma dosyası yükleyebilirsiniz.

1. [Azure Portal][Azure portal], güncelleştirmek istediğiniz bulut hizmetini seçin. Bu adım, bulut hizmeti örneği dikey penceresini açar.

2. Dikey pencerede **Güncelleştir**' i seçin.

    ![Güncelleştirme düğmesi](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Dağıtımı yeni bir hizmet paketi dosyası (. cspkg) ve hizmet yapılandırma dosyası (. cscfg) ile güncelleştirin.

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. İsteğe bağlı olarak, depolama hesabını ve dağıtım etiketini güncelleştirin.

5. Herhangi bir rolün yalnızca bir rol örneği varsa, yükseltmenin devam etmesini sağlamak için **bir veya daha fazla rol tek bir örnek içeriyorsa bile dağıt** ' ı seçin.

    Her rolde en az iki rol örneği (sanal makine) varsa, Azure, bir bulut hizmeti güncelleştirmesi sırasında yalnızca yüzde 99,95 hizmet kullanılabilirliğini garanti edebilir. İki rol örneğiyle, bir sanal makine, diğer güncelleştirirken istemci isteklerini işler.

6. Paketin karşıya yüklenmesi tamamlandıktan sonra güncelleştirmeyi uygulamak için **dağıtımı Başlat** onay kutusunu seçin.

7. Hizmeti güncelleştirmeye başlamak için **Tamam ' ı** seçin.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Aşamalı dağıtımı üretime yükseltmek için takas dağıtımları
Bulut hizmetinin yeni bir sürümünü dağıtmaya karar verirken, yeni yayınınızı bulut hizmeti hazırlama ortamınızda hazırlayın ve test edin. İki dağıtımın giderildiği URL 'Leri değiştirmek ve yeni bir yayını üretime yükseltmek için **değiştirme** 'yi kullanın.

Dağıtımları **Cloud Services** sayfasından veya Panodan takas edebilirsiniz.

1. [Azure Portal][Azure portal], güncelleştirmek istediğiniz bulut hizmetini seçin. Bu adım, bulut hizmeti örneği dikey penceresini açar.

2. Dikey pencerede **Değiştir**' i seçin.

    ![Cloud Services takas düğmesi](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Aşağıdaki onay istemi açılır:

    ![Cloud Services takas et](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Dağıtım bilgilerini doğruladıktan sonra, dağıtımları değiştirmek için **Tamam** ' ı seçin.

    Tek şey, dağıtımlar için sanal IP adresleri (VIP) olduğundan, dağıtım takası hızlı bir şekilde gerçekleşir.

    İşlem maliyetlerini kaydetmek için, üretim dağıtımınızın beklendiği gibi çalıştığını doğruladıktan sonra hazırlama dağıtımını silebilirsiniz.

### <a name="common-questions-about-swapping-deployments"></a>Dağıtımları değiştirme hakkında sık sorulan sorular

**Dağıtımları değiştirme önkoşulları nelerdir?**

Başarılı bir dağıtım takası için iki temel önkoşul vardır:

- Üretim yuvalarınız için statik bir IP adresi kullanmak istiyorsanız, hazırlama yuvalarınız için bir tane de ayırmanız gerekir. Aksi takdirde, değiştirme başarısız olur.

- Değiştirme işlemini gerçekleştirebilmeniz için rollerinizin tüm örneklerinin çalışıyor olması gerekir. Örneklerinizin durumunu Azure portal **genel bakış** dikey penceresinde kontrol edebilirsiniz. Alternatif olarak, Windows PowerShell 'de [Get-AzureRole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) komutunu da kullanabilirsiniz.

Konuk işletim sistemi güncelleştirmelerinin ve hizmet düzeltme işlemlerinin de dağıtım yamasının başarısız olmasına neden olabileceğini unutmayın. Daha fazla bilgi için bkz. [Cloud Service dağıtım sorunlarını giderme](cloud-services-troubleshoot-deployment-problems.md).

**Bir değiştirme uygulamam için kapalı kalma süresine neden olur mu? Bunu nasıl işleymem gerekir?**

Önceki bölümde açıklandığı gibi, Azure Yük dengeleyicide yalnızca bir yapılandırma değişikliği olduğundan, dağıtım takası genellikle hızlıdır. Bazı durumlarda, 10 veya daha fazla saniye sürebilir ve geçici bağlantı hatalarıyla sonuçlanır. Müşterilerinizin etkisini sınırlandırmak için, [istemci yeniden deneme mantığını](../best-practices-retry-general.md)uygulamayı düşünün.

## <a name="delete-deployments-and-a-cloud-service"></a>Dağıtımları ve bulut hizmetini silme
Bir bulut hizmetini silebilmeniz için, mevcut her bir dağıtımı silmeniz gerekir.

İşlem maliyetlerini kaydetmek için, üretim dağıtımınızın beklendiği gibi çalıştığını doğruladıktan sonra hazırlama dağıtımını silebilirsiniz. Durdurulmuş olan dağıtılan rol örnekleri için işlem maliyetleri için faturalandırılırsınız.

Bir dağıtımı veya bulut hizmetinizi silmek için aşağıdaki yordamı kullanın.

1. [Azure Portal][Azure portal], silmek istediğiniz bulut hizmetini seçin. Bu adım, bulut hizmeti örneği dikey penceresini açar.

2. Dikey pencerede **Sil**' i seçin.

    ![Cloud Services Sil düğmesi](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Tüm bulut hizmetini silmek için **bulut hizmeti ve dağıtımları** onay kutusunu seçin. Ya da **Üretim dağıtımı** veya **hazırlama dağıtımı** onay kutusunu seçebilirsiniz.

    ![Cloud Services silme](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Alt kısımdaki **Sil** ' i seçin.

5. Bulut hizmetini silmek için **bulut hizmetini Sil**' i seçin. Ardından, onay isteminde **Evet**' i seçin.

> [!NOTE]
> Bir bulut hizmeti silindiğinde ve ayrıntılı izleme yapılandırıldığında, verileri depolama hesabınızdan el ile silmeniz gerekir. Ölçüm tablolarının nerede bulunacağı hakkında daha fazla bilgi için bkz. [bulut hizmeti Izlemeye giriş](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Başarısız dağıtımlar hakkında daha fazla bilgi edinin
**Genel bakış** dikey penceresinin üst kısımdaki bir durum çubuğu vardır. Çubuğu seçtiğinizde, yeni bir dikey pencere açılır ve hata bilgilerini görüntüler. Dağıtım herhangi bir hata içermiyorsa, bilgi dikey penceresi boştur.

![Cloud Services genel bakış](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Sonraki adımlar
* [Bulut hizmetinizin genel yapılandırması](cloud-services-how-to-configure-portal.md).
* [Bulut hizmetini dağıtmayı](cloud-services-how-to-create-deploy-portal.md)öğrenin.
* Özel bir [etki alanı adı](cloud-services-custom-domain-name-portal.md)yapılandırın.
* [SSL sertifikalarını](cloud-services-configure-ssl-certificate-portal.md)yapılandırma.
