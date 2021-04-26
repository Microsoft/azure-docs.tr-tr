---
title: Microsoft Azure için Müşteri Kasası
description: Microsoft 'un müşteri verilerine erişmesi gerektiğinde bulut sağlayıcısı erişimi üzerinde denetim sağlayan Microsoft Azure için Müşteri Kasası Teknik Genel Bakış.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 04/05/2021
ms.openlocfilehash: 80d1e4f39d69f761b801ccec834c0228057e4847
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448534"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Microsoft Azure için Müşteri Kasası

> [!NOTE]
> Bu özelliği kullanmak için, kuruluşunuzda en az **Geliştirici** düzeyi olan bir [Azure Destek planına](https://azure.microsoft.com/support/plans/) sahip olmanız gerekir.

Microsoft personeli ve alt işlemciler tarafından gerçekleştirilen çoğu işlem, destek ve sorun giderme, müşteri verilerine erişim gerektirmez. Bu tür erişimin gerekli olduğu nadir koşullarda, Microsoft Azure için Müşteri Kasası müşterilerin müşteri verileri erişim isteklerini gözden geçirmesi ve onaylaması veya reddetmesi için bir arabirim sağlar. Müşteri tarafından başlatılan bir destek biletine veya Microsoft tarafından tanımlanan bir soruna yanıt olarak, Microsoft mühendisinin müşteri verilerine erişmesi gereken durumlarda kullanılır.

Bu makalede, Müşteri Kasası nasıl etkinleştirileceği ve kasa isteklerinin nasıl başlatıldığı, izlendiği ve daha sonraki incelemeler ve denetimler için nasıl depolandığı ele alınmaktadır.

<a name='supported-services-and-scenarios-in-general-availability'></a><a name='supported-services-and-scenarios-in-preview'></a>
## <a name="supported-services-and-scenarios"></a>Desteklenen hizmetler ve senaryolar

### <a name="general-availability"></a>Genel kullanılabilirlik
Aşağıdaki hizmetler Müşteri Kasası için genel kullanıma sunulmuştur:

- Azure API Management
- Azure App Service
- Azure Bilişsel Hizmetler
- Azure Container Registry
- MySQL için Azure Veritabanı
- Azure Databricks
- Azure Data Box
- Azure Veri Gezgini
- Azure Data Factory
- PostgreSQL için Azure Veritabanı
- Azure İşlevleri
- Azure HDInsight
- Azure Kubernetes Service
- Azure İzleyici
- Azure Depolama
- Azure SQL Veritabanı
- Azure abonelik aktarımları
- Azure Synapse Analytics
- Azure 'daki sanal makineler (uzak masaüstü erişimini, bellek dökümüne erişimi ve yönetilen diskleri kapsayan)

### <a name="public-preview"></a>Genel Önizleme
Aşağıdaki hizmetler şu anda Müşteri Kasası için önizleme aşamasındadır:

- Azure Machine Learning
- Azure Batch

## <a name="enable-customer-lockbox"></a>Müşteri Kasası etkinleştir

Artık Müşteri Kasası dikey penceresindeki [Yönetim modülünden](https://aka.ms/customerlockbox/administration) müşteri kasası etkinleştirebilirsiniz.  

> [!NOTE]
> Müşteri Kasası etkinleştirmek için, Kullanıcı hesabına [genel yönetici rolü atanmalıdır](../../active-directory/roles/manage-roles-portal.md).

## <a name="workflow"></a>İş akışı

Aşağıdaki adımlarda Müşteri Kasası isteği için tipik bir iş akışı ana hatlarıyla verilmiştir.

1. Kuruluştaki birisinin Azure iş yüküyle ilgili bir sorun vardır.

2. Bu kişi sorunu giderdikten, ancak bunu düzeltemedi [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)bir destek bileti açar. Bilet bir Azure müşteri destek mühendisine atanır.

3. Azure Destek Mühendisi, hizmet isteğini inceler ve sorunu çözmeye yönelik sonraki adımları belirler.

4. Destek Mühendisi standart araçları ve hizmet tarafından üretilen verileri kullanarak sorunu gideremez bir sonraki adım, tam zamanında (JıT) erişim hizmeti kullanarak yükseltilmiş izinler isteğidir. Bu istek, Azure DevOps ekibine ilerletiğinden, bu istek orijinal destek mühendisinden veya farklı bir mühendisden olabilir.

5. Azure mühendisi tarafından erişim isteği gönderildikten sonra, tam zamanında hizmeti, isteği şu şekilde hesaba katmasının önüne değerlendirir:
    - Kaynağın kapsamı
    - İstek sahibinin yalıtılmış bir kimlik mi yoksa Multi-Factor Authentication kullanılarak mı kullanıldığı
    - İzin düzeyleri

    JıT kuralına bağlı olarak, bu istek Iç Microsoft onaylayanlardan onay de içerebilir. Örneğin, onaylayan müşteri destek lideri veya DevOps yöneticisi olabilir.

6. İstek müşteri verilerine doğrudan erişim gerektirdiğinde, bir Müşteri Kasası isteği başlatılır. Örneğin, bir müşterinin sanal makinesine uzak masaüstü erişimi.

    İstek artık **müşteri tarafından bildirildi** durumunda olduğundan, erişim izni vermeden önce müşterinin onayını bekliyor.

7. Müşteri kuruluşunda, Azure aboneliği için [sahip rolüne](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sahip olan Kullanıcı Microsoft 'tan bir e-posta alır ve bu, bekleyen erişim isteği hakkında bilgilendirmeye yöneliktir. Müşteri Kasası istekler için, bu kişi belirlenen onaylayıcı.

    Örnek e-posta:

    ![Azure Müşteri Kasası-e-posta bildirimi](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. E-posta bildirimi, Yönetim modülündeki **müşteri kasası** dikey penceresine bir bağlantı sağlar. Bu bağlantıyı kullanarak, belirlenen onaylayan, kuruluşlarının Müşteri Kasası sahip olduğu bekleyen istekleri görüntülemek için Azure portal oturum açar:

    ![Azure Müşteri Kasası-giriş sayfası](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   İstek, dört gün boyunca müşteri kuyruğunda kalır. Bu süreden sonra, erişim isteği otomatik olarak sona erer ve Microsoft mühendislerine erişim sağlanmaz.

9. Bekleyen isteğin ayrıntılarını almak için, belirlenen onaylayan, **bekleyen isteklerden** gelen kasa isteğini seçebilir:

    ![Azure Müşteri Kasası-bekleyen isteği görüntüleme](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. Belirlenen onaylayan, özgün kullanıcı tarafından oluşturulan destek bileti isteğini görüntülemek için **HIZMET ISTEğI kimliğini** de seçebilir. Bu bilgiler, Microsoft Desteği neden bağlı olduğuna ve bildirilen sorunun geçmişine yönelik bağlam sağlar. Örnek:

    ![Azure Müşteri Kasası-destek bileti isteğini görüntüleme](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. İstek gözden geçirildikten sonra, belirlenen onaylayan **Onayla** veya **Reddet**' i seçer:

    ![Azure Müşteri Kasası-onayla veya Reddet ' i seçin](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    Seçimin bir sonucu olarak:
    - **Onayla**: Microsoft mühendisine erişim verilir. Erişim varsayılan sekiz saatlik bir süre için verilir.
    - **Reddet**: Microsoft mühendisine göre yükseltilmiş erişim isteği reddedilir ve başka bir eylem yapılmaz.

Denetim amaçlarıyla, bu iş akışında gerçekleştirilen eylemler [müşteri kasası istek günlüklerine](#auditing-logs)kaydedilir.

## <a name="auditing-logs"></a>Denetim günlükleri

Müşteri Kasası günlükleri etkinlik günlüklerine depolanır. Azure portal, Müşteri Kasası isteklerle ilgili denetim bilgilerini görüntülemek için **etkinlik günlükleri** ' ni seçin. Belirli eylemler için filtreleyebilirsiniz, örneğin:
- **Kasa İsteğini Reddetme**
- **Kasa İsteği Oluşturma**
- **Kasa İsteğini Onaylama**
- **Kasa Isteği süre sonu**

Örneğin:

![Azure Müşteri Kasası-etkinlik günlükleri](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>Azure Güvenlik Karşılaştırması ile Müşteri Kasası tümleştirmesi

Azure Güvenlik kıyaslaması 'nda Müşteri Kasası uygulanabilirliğini içeren yeni bir temel denetim ([3,13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)) sunuyoruz. Müşteriler artık bir hizmetin Müşteri Kasası uygulanabilirliğini gözden geçirmek için kıyaslama özelliğinden yararlanabilir.

## <a name="exclusions"></a>Dışlamalar

Müşteri Kasası istekleri aşağıdaki mühendislik desteği senaryolarında tetiklenmez:

- Standart işletim yordamları dışında kalan acil durum senaryoları. Örneğin, bir ana hizmet kesintisi, beklenmeyen veya öngörülemeyen bir senaryoda Hizmetleri kurtarmak veya geri yüklemek için hemen ilgilenilmesi gerekir. Bu "kesme camı" olayları nadir ve çoğu örnekte çözümlemek üzere müşteri verilerine erişim gerekmez.
- Microsoft mühendis, sorun gidermenin bir parçası olarak Azure platformuna erişir ve yanlışlıkla müşteri verilerine açık olarak sunulur. Örneğin Azure Ağ Ekibi sorun giderme işlemi yapıyor ve bu işlem sonucunda ağ cihazında bir paket yakalanıyor olabilir. Bu tür senaryolar, anlamlı miktarda müşteri verilerine erişim elde edilmesine neden olur. Müşteriler, verilerini transit ve bekleyen şifrelemeyle kullanarak daha fazla koruyabilir.

Müşteri Kasası istekler, veriler için de dış yasal talepler tarafından tetiklenmez. Ayrıntılar için bkz. Microsoft Güven Merkezi 'nde [veriler için kamu istekleri](https://www.microsoft.com/trust-center/) tartışması.

## <a name="next-steps"></a>Sonraki adımlar

Müşteri Kasası, en az **Geliştirici** düzeyi olan bir [Azure Destek planına](https://azure.microsoft.com/support/plans/) sahip tüm müşteriler için kullanılabilir. Müşteri Kasası dikey penceresindeki [Yönetim modülünden](https://aka.ms/customerlockbox/administration) müşteri kasası etkinleştirebilirsiniz.

Bir destek durumunu devam etmek için bu işlem gerekiyorsa, Müşteri Kasası istekleri bir Microsoft mühendisi tarafından başlatılır.
