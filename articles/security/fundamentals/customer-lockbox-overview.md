---
title: Microsoft Azure için Müşteri Kasası
description: Microsoft 'un müşteri verilerine erişmesi gerektiğinde bulut sağlayıcısı erişimi üzerinde denetim sağlayan Microsoft Azure için Müşteri Kasası Teknik Genel Bakış.
author: cabailey
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: cabailey
manager: barbkess
ms.date: 11/04/2019
ms.openlocfilehash: 7c0409d48876a0f830366381c2a46821c4aa03a0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466411"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Microsoft Azure için Müşteri Kasası

> [!NOTE]
> Bu özelliği kullanmak için, kuruluşunuzda en az **Geliştirici**düzeyi olan bir [Azure Destek planına](https://azure.microsoft.com/support/plans/) sahip olmanız gerekir.

Microsoft Azure için Müşteri Kasası, müşterilerin müşteri verileri erişim isteklerini gözden geçirmesi ve onaylaması veya reddetmesi için bir arabirim sağlar. Bir Microsoft mühendisinin bir destek isteği sırasında müşteri verilerine erişmesi gereken durumlarda kullanılır.

Bu makalede, Müşteri Kasası isteklerinin nasıl başlatıldığı, izlendiği ve daha sonraki gözden geçirmeleri ve denetimleri için depolandığı ele alınmaktadır.

Müşteri Kasası artık genel olarak kullanılabilir ve sanal makinelere uzak masaüstü erişimi için şu anda etkin durumda.

## <a name="workflow"></a>İş akışı

Aşağıdaki adımlarda Müşteri Kasası isteği için tipik bir iş akışı ana hatlarıyla verilmiştir.

1. Kuruluştaki birisinin Azure iş yüküyle ilgili bir sorun vardır.

2. Bu kişi sorunu giderdikten, ancak bunu düzeltemedi [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)bir destek bileti açar. Bilet bir Azure müşteri destek mühendisine atanır.

3. Azure Destek Mühendisi, hizmet isteğini inceler ve sorunu çözmeye yönelik sonraki adımları belirler.

4. Destek Mühendisi standart araçları ve telemetrisi kullanarak sorunu gideremez bir sonraki adım, tam zamanında (JıT) erişim hizmeti kullanarak yükseltilmiş izinler isteğidir. Bu istek, özgün Destek Mühendisinden olabilir. Veya, sorun Azure DevOps ekibine ilerletiğinden, bu, farklı bir mühendisden olabilir.

5. Azure mühendisi tarafından erişim isteği gönderildikten sonra, tam zamanında hizmeti, isteği şu şekilde hesaba katmasının önüne değerlendirir:
    - Kaynağın kapsamı
    - İstek sahibinin yalıtılmış bir kimlik mi yoksa Multi-Factor Authentication kullanılarak mı kullanıldığı
    - İzin düzeyleri
    
    JıT kuralına bağlı olarak, bu istek Iç Microsoft onaylayanlardan onay de içerebilir. Örneğin, onaylayan müşteri destek lideri veya DevOps yöneticisi olabilir.

6. İstek müşteri verilerine doğrudan erişim gerektirdiğinde, bir Müşteri Kasası isteği başlatılır. Örneğin, bir müşterinin sanal makinesine uzak masaüstü erişimi.
    
    İstek artık **müşteri tarafından bildirildi** durumunda olduğundan, erişim izni vermeden önce müşterinin onayını bekliyor.

7. Müşteri kuruluşunda, Azure aboneliği için [sahip rolüne](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) sahip olan Kullanıcı Microsoft 'tan bir e-posta alır ve bu, bekleyen erişim isteği hakkında bilgilendirmeye yöneliktir. Müşteri Kasası istekler için, bu kişi belirlenen onaylayıcı.
    
    Örnek e-posta:
    
    ![Azure Müşteri Kasası-e-posta bildirimi](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. E-posta bildirimi, Azure portal **müşteri kasası** dikey penceresine bir bağlantı sağlar. Bu bağlantıyı kullanarak, belirlenen onaylayan, kuruluşlarının Müşteri Kasası sahip olduğu bekleyen istekleri görüntülemek için Azure portal oturum açar:
    
    ![Azure Müşteri Kasası-giriş sayfası](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)
    
   İstek, dört gün boyunca müşteri kuyruğunda kalır. Bu süreden sonra, erişim isteği otomatik olarak sona erer ve Microsoft mühendislerine erişim sağlanmaz.

9. Bekleyen isteğin ayrıntılarını almak için, belirlenen onaylayan, **bekleyen isteklerden**gelen kasa isteğini seçebilir:
    
    ![Azure Müşteri Kasası-bekleyen isteği görüntüleme](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. Belirlenen onaylayan, özgün kullanıcı tarafından oluşturulan destek bileti isteğini görüntülemek için **HIZMET ISTEğI kimliğini** de seçebilir. Bu bilgiler, Microsoft Desteği neden bağlı olduğuna ve bildirilen sorunun geçmişine yönelik bağlam sağlar. Örneğin:
    
    ![Azure Müşteri Kasası-destek bileti isteğini görüntüleme](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. İstek gözden geçirildikten sonra, belirlenen onaylayan **Onayla** veya **Reddet**' i seçer:
    
    ![Azure Müşteri Kasası-onayla veya Reddet ' i seçin](./media/customer-lockbox-overview/customer-lockbox-approval.png)
    
    Seçimin bir sonucu olarak:
    - **Onayla**: Microsoft mühendisine erişim verilir. Erişim varsayılan sekiz saatlik bir süre için verilir.
    - **Reddet**: Microsoft mühendisine göre yükseltilmiş erişim isteği reddedilir ve başka bir eylem yapılmaz.

Denetim amaçlarıyla, bu iş akışında gerçekleştirilen eylemler [müşteri kasası istek günlüklerine](#auditing-logs)kaydedilir.

## <a name="auditing-logs"></a>Denetim günlükleri

Müşteri Kasası Günlükler etkinlik günlüklerine depolanır. Azure portal, Müşteri Kasası isteklerle ilgili denetim bilgilerini görüntülemek için **etkinlik günlükleri** ' ni seçin. Belirli eylemler için filtre uygulayabilirsiniz, örneğin:
- **Kasa Isteğini Reddet**
- **Kasa Isteği oluştur**
- **Kasa Isteğini Onayla**
- **Kasa Isteği süre sonu**

Örneğin:

![Azure Müşteri Kasası-etkinlik günlükleri](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios-in-general-availability"></a>Genel kullanıma yönelik desteklenen hizmetler ve senaryolar

Aşağıdaki hizmetler ve senaryolar Şu anda Müşteri Kasası için genel kullanıma yöneliktir.

### <a name="remote-desktop-access-to-virtual-machines"></a>Sanal makinelere uzak masaüstü erişimi

Müşteri Kasası Şu anda sanal makinelere uzak masaüstü erişim istekleri için etkinleştirilmiştir. Aşağıdaki iş yükleri desteklenir:
- Hizmet olarak platform (PaaS)-Azure Cloud Services (Web rolü ve çalışan rolü)
- Hizmet olarak altyapı (IaaS)-Windows ve Linux (yalnızca Azure Resource Manager)
- Sanal makine ölçek kümesi-Windows ve Linux

> [!NOTE]
> IaaS klasik örnekleri Müşteri Kasası tarafından desteklenmez. IaaS klasik örneklerinde çalışan iş yükleriniz varsa, bunları klasik 'dan Kaynak Yöneticisi dağıtım modellerine geçirmeniz önerilir. Yönergeler için bkz. [Klasik 'dan Azure Resource Manager IaaS kaynaklarının platform tarafından desteklenen geçişi](../../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Ayrıntılı denetim günlükleri

Uzak Masaüstü erişimi içeren senaryolar için, Microsoft mühendis tarafından gerçekleştirilen eylemleri gözden geçirmek üzere Windows olay günlüklerini kullanabilirsiniz. Azure Güvenlik Merkezi 'ni kullanarak Olay günlüklerinizi toplayın ve analiz için verileri çalışma alanınıza kopyalayın. Daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'Nde veri toplama](../../security-center/security-center-enable-data-collection.md).

## <a name="supported-services-and-scenarios-in-preview"></a>Önizlemede desteklenen hizmetler ve senaryolar

Aşağıdaki hizmetler şu anda Müşteri Kasası için önizleme aşamasındadır:

- Azure Depolama 

- Azure SQL DB 

- Azure Veri Gezgini 

- Sanal makineler (artık bellek dökümlerinin ve yönetilen disklerin erişimini de kapsayan) 

- Azure abonelik aktarımları

Kuruluşunuzun bu önizleme teklifleri için Müşteri Kasası etkinleştirmek üzere [Azure genel önizlemesi için müşteri kasası](https://aka.ms/customerlockbox/insiderprogram)kaydolun.


## <a name="exclusions"></a>İstisnalar

Müşteri Kasası istekleri aşağıdaki mühendislik desteği senaryolarında tetiklenmez:

- Microsoft mühendisinin standart işletim yordamları dışında kalan bir etkinlik yapması gerekir. Örneğin, beklenmeyen veya öngörülemeyen senaryolarda Hizmetleri kurtarmak veya geri yüklemek için.

- Microsoft mühendis, sorun gidermenin bir parçası olarak Azure platformuna erişir ve yanlışlıkla müşteri verilerine erişebilir. Örneğin, Azure ağ ekibi, bir ağ cihazında paket yakalamaya neden olan sorun gidermeyi gerçekleştirir. Ancak, müşteri verileri aktarım sırasında şifrelediyse, mühendis verileri okuyamaz.

## <a name="next-steps"></a>Sonraki adımlar

Müşteri Kasası, en az **Geliştirici**düzeyi olan bir [Azure Destek planına](https://azure.microsoft.com/support/plans/) sahip tüm müşteriler için otomatik olarak kullanılabilir.

Uygun bir destek planınız varsa, Müşteri Kasası etkinleştirmeniz için herhangi bir işlem yapmanız gerekmez. Müşteri Kasası istekleri, kuruluşunuzdaki bir bilgisayardan dosyalanan bir destek biletini devam etmek için gerekliyse, bu işlem bir Microsoft mühendisi tarafından başlatılır.
