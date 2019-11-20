---
title: Cloud Service dağıtım sorunlarını giderme | Microsoft Docs
description: Azure 'a bir bulut hizmeti dağıttığınızda karşılaşabileceğiniz bazı yaygın sorunlar vardır. Bu makale, bunların bazılarına yönelik çözümler sağlar.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: ccb08f853ae0f941dd5f9c0eca8c77f0f650905a
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122742"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>Cloud Service dağıtım sorunlarını giderme
Azure 'a bir bulut hizmeti uygulama paketi dağıttığınızda, Azure portal **Özellikler** bölmesinden dağıtım hakkında bilgi alabilirsiniz. Bu bölmedeki ayrıntıları, bulut hizmetindeki sorunları gidermenize yardımcı olması için kullanabilir ve yeni bir destek isteği açarken bu bilgileri Azure desteği 'ne verebilirsiniz.

**Özellikler** bölmesini aşağıdaki gibi bulabilirsiniz:

* Azure portal bulut hizmetinizin dağıtımına tıklayın, **Tüm ayarlar**' a ve ardından **Özellikler**' e tıklayın.

> [!NOTE]
> Bölmenin sağ üst köşesindeki simgeye tıklayarak **Özellikler** bölmesinin içeriğini Pano 'ya kopyalayabilirsiniz.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Gidermek Web siteme erişemiyorum, ancak dağıtımım başlatıldı ve tüm rol örnekleri hazırlanıyor
Portalda gösterilen Web sitesi URL 'SI bağlantısı, bağlantı noktasını içermez. Web siteleri için varsayılan bağlantı noktası 80 ' dir. Uygulamanız farklı bir bağlantı noktasında çalışacak şekilde yapılandırıldıysa, Web sitesine erişirken doğru bağlantı noktası numarasını URL 'ye eklemeniz gerekir.

1. Azure portal bulut hizmetinizin dağıtımına tıklayın.
2. Azure portal **Özellikler** bölmesinde, rol örneklerinin ( **giriş uç noktaları**altında) bağlantı noktalarını kontrol edin.
3. Bağlantı noktası 80 değilse, uygulamaya erişirken doğru bağlantı noktası değerini URL 'ye ekleyin. Varsayılan olmayan bir bağlantı noktası belirtmek için, URL 'yi yazın ve ardından iki nokta üst üste (:), ardından bağlantı noktası numarası, boşluk olmadan).

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Gidermek Rol örneklerim hiçbir şey yapmadan geri dönüştürüldü
Azure 'un sorunlu düğümleri algıladığında ve bu nedenle rol örneklerini yeni düğümlere taşıdıkça hizmet onarma işlemi otomatik olarak gerçekleşir. Bu gerçekleştiğinde, rol örneklerinizi otomatik olarak geri dönüşüme bakabilirsiniz. Hizmet iyileştirmenin oluşup olmadığını öğrenmek için:

1. Azure portal bulut hizmetinizin dağıtımına tıklayın.
2. Azure portal **Özellikler** bölmesinde, bilgileri gözden geçirin ve rol geri dönüşümünü gözlemlediğiniz sırada hizmet iyileştirmenin yapılıp yapılmayacağını saptayın.

Roller, ana bilgisayar-işletim sistemi ve konuk işletim sistemi güncelleştirmeleri sırasında ayda her ay bir kez de geri dönüştürülecek.  
Daha fazla bilgi için, [işletim sistemi yükseltmeleri nedeniyle blog gönderisi rolü örneği yeniden başlatmaları](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) konusuna bakın

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Gidermek VIP takas yapılamıyor ve bir hata alıyorum
Devam eden bir dağıtım güncelleştirmesi varsa bir VIP Swap öğesine izin verilmez. Dağıtım güncelleştirmeleri şu durumlarda otomatik olarak gerçekleşebilir:

* Yeni bir konuk işletim sistemi kullanılabilir ve otomatik güncelleştirmeler için yapılandırdınız.
* Hizmet düzeltme durumu oluşur.

Bir otomatik güncelleştirmenin bir VIP takası yapmanızı engelleyip engellemediğini öğrenmek için:

1. Azure portal bulut hizmetinizin dağıtımına tıklayın.
2. Azure portal **Özellikler** bölmesinde **durum**değerine bakın. Bu **durumda, en** **son işlemi** , VIP takasını engelleyebilecek bir en son gerçekleşip öngörmeyebilirsiniz.
3. Üretim dağıtımı için 1 ve 2. adımları tekrarlayın.
4. Bir otomatik güncelleştirme sürecde ise, VIP takas işlemini gerçekleştirmeye çalışmadan önce işlemin bitmesini bekleyin.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Gidermek Rol örneği başlatıldı, başlatılıyor, meşgul ve durduruldu arasında döngü yapıyor
Bu durum, uygulama kodunuz, paketiniz veya yapılandırma dosyanızla ilgili bir sorundan kaynaklanıyor olabilir. Bu durumda, her birkaç dakikada bir değişikliği görebilmeniz ve Azure portal **geri dönüşüm**, **meşgul**veya **başlatma**gibi bir şey olabileceğini görmeniz gerekir. Bu, uygulamada rol örneğinin çalışmasını koruyan bir sorun olduğunu gösterir.

Bu sorunla ilgili sorun giderme hakkında daha fazla bilgi için bkz. [Azure PaaS Işlem Tanılama verileri](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) ve [rollerin geri dönüştürülmesine neden olan yaygın sorunlar](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Gidermek Uygulamam çalışmayı durdurdu
1. Azure portal rol örneğine tıklayın.
2. Azure portal **Özellikler** bölmesinde, sorununuzu çözmek için aşağıdaki koşulları göz önünde bulundurun:
   * Rol örneği yakın zamanda durdurulmuşsa ( **iptal sayısı**değerini kontrol edebilirsiniz), dağıtım güncelleştiriyoruz. Rol örneğinin kendi kendine çalışmayı sürdürüyor olup olmadığını görmek için bekleyin.
   * Rol örneği **meşgulse**, [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) olayının işlenmiş olup olmadığını görmek için uygulama kodunuzu kontrol edin. Bu olayı işleyen bir kod eklemeniz veya çözmeniz gerekebilir.
   * [Azure PaaS Işlem tanılama verilerinde](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)blog postasında tanılama verilerini ve sorun giderme senaryolarını gözden geçin.

> [!WARNING]
> Bulut hizmetinizi geri yüklerseniz, dağıtımın özelliklerini sıfırladıktan sonra özgün sorunun bilgilerini etkin bir şekilde silolursunuz.
>
>

## <a name="next-steps"></a>Sonraki adımlar
Cloud Services için daha fazla [sorun giderme makalesini](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) görüntüleyin.

Azure PaaS bilgisayar tanılama verilerini kullanarak bulut hizmeti rolü sorunlarını giderme hakkında bilgi edinmek için bkz. [Kevin Williamson 'ın blog serisi](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
