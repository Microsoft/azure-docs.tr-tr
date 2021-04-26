---
title: Bulut hizmeti (klasik) dağıtım sorunlarını giderme | Microsoft Docs
description: Azure 'a bir bulut hizmeti dağıttığınızda karşılaşabileceğiniz bazı yaygın sorunlar vardır. Bu makale, bunların bazılarına yönelik çözümler sağlar.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 7b3d7a9a674aab3976da9399f71ff4d8df08eb62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741086"
---
# <a name="troubleshoot-azure-cloud-services-classic-deployment-problems"></a>Azure Cloud Services (klasik) dağıtım sorunlarını giderme

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Azure 'a bir bulut hizmeti uygulama paketi dağıttığınızda, Azure portal **Özellikler** bölmesinden dağıtım hakkında bilgi alabilirsiniz. Bu bölmedeki ayrıntıları, bulut hizmetindeki sorunları gidermenize yardımcı olması için kullanabilir ve yeni bir destek isteği açarken bu bilgileri Azure desteği 'ne verebilirsiniz.

**Özellikler** bölmesini aşağıdaki gibi bulabilirsiniz:

* Azure portal bulut hizmetinizin dağıtımına tıklayın, **Tüm ayarlar**' a ve ardından **Özellikler**' e tıklayın.

> [!NOTE]
> Bölmenin sağ üst köşesindeki simgeye tıklayarak **Özellikler** bölmesinin içeriğini Pano 'ya kopyalayabilirsiniz.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Sorun: Web siteme erişemiyorum, ancak dağıtımım başlatıldı ve tüm rol örnekleri hazırlanıyor
Portalda gösterilen Web sitesi URL 'SI bağlantısı, bağlantı noktasını içermez. Web siteleri için varsayılan bağlantı noktası 80 ' dir. Uygulamanız farklı bir bağlantı noktasında çalışacak şekilde yapılandırıldıysa, Web sitesine erişirken doğru bağlantı noktası numarasını URL 'ye eklemeniz gerekir.

1. Azure portal bulut hizmetinizin dağıtımına tıklayın.
2. Azure portal **Özellikler** bölmesinde, rol örneklerinin ( **giriş uç noktaları** altında) bağlantı noktalarını kontrol edin.
3. Bağlantı noktası 80 değilse, uygulamaya erişirken doğru bağlantı noktası değerini URL 'ye ekleyin. Varsayılan olmayan bir bağlantı noktası belirtmek için, URL 'yi yazın ve ardından iki nokta üst üste (:), ardından bağlantı noktası numarası, boşluk olmadan).

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Sorun: rol örneklerim hiçbir şey yapmadan geri dönüştürüldü
Azure 'un sorunlu düğümleri algıladığında ve bu nedenle rol örneklerini yeni düğümlere taşıdıkça hizmet onarma işlemi otomatik olarak gerçekleşir. Bu gerçekleştiğinde, rol örneklerinizi otomatik olarak geri dönüşüme bakabilirsiniz. Hizmet iyileştirmenin oluşup olmadığını öğrenmek için:

1. Azure portal bulut hizmetinizin dağıtımına tıklayın.
2. Azure portal **Özellikler** bölmesinde, bilgileri gözden geçirin ve rol geri dönüşümünü gözlemlediğiniz sırada hizmet iyileştirmenin yapılıp yapılmayacağını saptayın.

Roller, ana bilgisayar-işletim sistemi ve konuk işletim sistemi güncelleştirmeleri sırasında ayda her ay bir kez de geri dönüştürülecek.  
Daha fazla bilgi için, [işletim sistemi yükseltmeleri nedeniyle blog gönderisi rolü örneği yeniden başlatmaları](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades) konusuna bakın

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Sorun: VIP takas yapılamıyor ve bir hata alıyorum
Devam eden bir dağıtım güncelleştirmesi varsa bir VIP Swap öğesine izin verilmez. Dağıtım güncelleştirmeleri şu durumlarda otomatik olarak gerçekleşebilir:

* Yeni bir konuk işletim sistemi kullanılabilir ve otomatik güncelleştirmeler için yapılandırdınız.
* Hizmet düzeltme durumu oluşur.

Bir otomatik güncelleştirmenin bir VIP takası yapmanızı engelleyip engellemediğini öğrenmek için:

1. Azure portal bulut hizmetinizin dağıtımına tıklayın.
2. Azure portal **Özellikler** bölmesinde **durum** değerine bakın. Bu **durumda, en** **son işlemi** , VIP takasını engelleyebilecek bir en son gerçekleşip öngörmeyebilirsiniz.
3. Üretim dağıtımı için 1 ve 2. adımları tekrarlayın.
4. Bir otomatik güncelleştirme sürecde ise, VIP takas işlemini gerçekleştirmeye çalışmadan önce işlemin bitmesini bekleyin.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Sorun: bir rol örneği başlatıldı, başlatılıyor, meşgul ve durduruldu arasında döngü yapıyor
Bu durum, uygulama kodunuz, paketiniz veya yapılandırma dosyanızla ilgili bir sorundan kaynaklanıyor olabilir. Bu durumda, her birkaç dakikada bir değişikliği görebilmeniz ve Azure portal **geri dönüşüm**, **meşgul** veya **başlatma** gibi bir şey olabileceğini görmeniz gerekir. Bu, uygulamada rol örneğinin çalışmasını koruyan bir sorun olduğunu gösterir.

Bu sorunla ilgili sorun giderme hakkında daha fazla bilgi için bkz. [Azure PaaS Işlem Tanılama verileri](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data) ve [rollerin geri dönüştürülmesine neden olan yaygın sorunlar](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Sorun: Uygulamam çalışmayı durdurdu
1. Azure portal rol örneğine tıklayın.
2. Azure portal **Özellikler** bölmesinde, sorununuzu çözmek için aşağıdaki koşulları göz önünde bulundurun:
   * Rol örneği yakın zamanda durdurulmuşsa ( **iptal sayısı** değerini kontrol edebilirsiniz), dağıtım güncelleştiriyoruz. Rol örneğinin kendi kendine çalışmayı sürdürüyor olup olmadığını görmek için bekleyin.
   * Rol örneği **meşgulse**, [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) olayının işlenmiş olup olmadığını görmek için uygulama kodunuzu kontrol edin. Bu olayı işleyen bir kod eklemeniz veya çözmeniz gerekebilir.
   * [Azure PaaS Işlem tanılama verilerinde](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)blog postasında tanılama verilerini ve sorun giderme senaryolarını gözden geçin.

> [!WARNING]
> Bulut hizmetinizi geri yüklerseniz, dağıtımın özelliklerini sıfırladıktan sonra özgün sorunun bilgilerini etkin bir şekilde silolursunuz.
>
>

## <a name="next-steps"></a>Sonraki adımlar
Cloud Services için daha fazla [sorun giderme makalesini](./cloud-services-allocation-failures.md) görüntüleyin.

Azure PaaS bilgisayar tanılama verilerini kullanarak bulut hizmeti rolü sorunlarını giderme hakkında bilgi edinmek için bkz. [Kevin Williamson 'ın blog serisi](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).