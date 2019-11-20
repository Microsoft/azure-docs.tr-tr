---
title: Bulut hizmetini yapılandırma (portal) | Microsoft Docs
description: Azure 'da bulut hizmetlerini yapılandırmayı öğrenin. Bulut hizmeti yapılandırmasını güncelleştirmeyi ve rol örneklerine uzaktan erişimi yapılandırmayı öğrenin. Bu örnekler Azure portal kullanır.
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 12/07/2016
ms.author: gwallace
ms.openlocfilehash: 8b60a81e06b95c69a02f88ff3275743b056c191d
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359660"
---
# <a name="how-to-configure-cloud-services"></a>Cloud Services nasıl yapılandırılır

Azure portal bir bulut hizmeti için en yaygın olarak kullanılan ayarları yapılandırabilirsiniz. Ya da yapılandırma dosyalarınızı doğrudan güncelleştirmek isterseniz güncelleştirilecek hizmet yapılandırma dosyasını indirin ve ardından güncelleştirilmiş dosyayı yükleyin ve bulut hizmetini yapılandırma değişiklikleriyle güncelleştirin. Her iki şekilde de yapılandırma güncelleştirmeleri tüm rol örneklerine atılır.

Ayrıca, bulut hizmeti rollerinizin veya uzak masaüstünün örneklerini yönetebilirsiniz.

Azure, her rol için en az iki rol örneliğine sahipseniz, yapılandırma güncelleştirmeleri sırasında yalnızca yüzde 99,95 hizmet kullanılabilirliği sağlayabilir. Bu, bir sanal makinenin diğer güncellenirken istemci isteklerini işlemesini sağlar. Daha fazla bilgi için bkz. [hizmet düzeyi sözleşmeleri](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Bulut hizmetini değiştirme

[Azure Portal](https://portal.azure.com/)açtıktan sonra bulut hizmetinize gidin. Buradan bunun birçok yönünü yönetirsiniz.

![Ayarlar sayfası](./media/cloud-services-how-to-configure-portal/cloud-service.png)

**Ayarlar** veya **Tüm ayarlar** bağlantıları, **özellikleri**değiştirmek, **yapılandırmayı**değiştirmek, **sertifikaları**yönetmek, **Uyarı kuralları**ayarlamak ve kullanıcıları yönetmek için **ayarları** açar. bu bulut hizmetine kimlerin erişimi vardır.

![Azure bulut hizmeti ayarları](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Konuk işletim sistemi sürümünü yönetme

Varsayılan olarak, Azure, Konuk işletim sistemini, Windows Server 2016 gibi hizmet yapılandırmanızda (. cscfg) belirttiğiniz işletim sistemi ailesinde bulunan en son desteklenen görüntüye düzenli olarak güncelleştirir.

Belirli bir işletim sistemi sürümünü hedefliyorsanız, bu sürümü **yapılandırma**bölümünde ayarlayabilirsiniz.

![İşletim sistemi sürümünü ayarla](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Belirli bir işletim sistemi sürümünün seçilmesi otomatik işletim sistemi güncelleştirmelerini devre dışı bırakır ve sorumluluğa yama yapar. Rol örneklerinizin güncelleştirmeleri aldığından emin olmanız gerekir veya uygulamanızı güvenlik açıklarına maruz kalabilirsiniz.

## <a name="monitoring"></a>İzleme

Bulut hizmetinize uyarı ekleyebilirsiniz. **Ayarlar** > **Uyarı**kurallarıUyarı > **Ekle**' ye tıklayın.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Buradan bir uyarı ayarlayabilirsiniz. **Ölçüm** açılan kutusuyla, aşağıdaki veri türleri için bir uyarı ayarlayabilirsiniz.

* Disk okuma
* Disk yazma
* Ağa gelen
* Ağdan giden
* CPU yüzdesi

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Ölçüm kutucuğundan izlemeyi yapılandırma

**Ayarlar** > **Uyarı kurallarını**kullanmak yerine, bulut hizmetinin **izleme** bölümündeki ölçüm kutucuklarından birine tıklayabilirsiniz.

![Bulut hizmeti Izleme](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Buradan, kutucukla kullanılan grafiği özelleştirebilir veya bir uyarı kuralı ekleyebilirsiniz.

## <a name="reboot-reimage-or-remote-desktop"></a>Yeniden başlatma, yeniden görüntü oluşturma veya uzak masaüstü

Uzak Masaüstü [Azure Portal (Uzak Masaüstü Kurulum)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)veya [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)aracılığıyla ayarlayabilirsiniz.

Bir bulut hizmetine yeniden başlatmak, yeniden görüntü eklemek veya uzak bir hizmet vermek için bulut hizmeti örneğini seçin.

![Bulut hizmeti örneği](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Daha sonra, bir Uzak Masaüstü bağlantısı başlatabilir, örneği uzaktan yeniden başlatabilir ya da uzaktan yeniden görüntüye (yeni bir görüntüyle başlayabilirsiniz).

![Bulut hizmeti örnek düğmeleri](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>. Cscfg dosyanızı yeniden yapılandırın

[Hizmet yapılandırma (cscfg)](cloud-services-model-and-package.md#cscfg) dosyası aracılığıyla bulut hizmetinizi yeniden yapılandırmanız gerekebilir. Önce. cscfg dosyanızı indirmeniz, değiştirip dosyayı karşıya yüklemeniz gerekir.

1. Ayarları açmak için **Ayarlar** simgesine veya **Tüm ayarlar** bağlantısına **tıklayın.**

    ![Ayarlar sayfası](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. **Yapılandırma** öğesine tıklayın.

    ![Yapılandırma dikey penceresi](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. **İndir** düğmesine tıklayın.

    ![İndirme](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Hizmet yapılandırma dosyasını güncelleştirdikten sonra yapılandırma güncelleştirmelerini yükleyin ve uygulayın:

    ![Karşıya Yükle](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. . Cscfg dosyasını seçin ve **Tamam**' a tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

* [Bulut hizmetini dağıtmayı](cloud-services-how-to-create-deploy-portal.md)öğrenin.
* Özel bir [etki alanı adı](cloud-services-custom-domain-name-portal.md)yapılandırın.
* [Bulut hizmetinizi yönetin](cloud-services-how-to-manage-portal.md).
* [SSL sertifikalarını](cloud-services-configure-ssl-certificate-portal.md)yapılandırma.
