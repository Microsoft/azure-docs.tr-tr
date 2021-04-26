---
title: Azure Data Box için kasa yapılandırma
description: Azure Data Box Müşteri Kasası nasıl kullanacağınızı öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 178ad169c1b576458f38b440ca79f4bb9eb012f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92124920"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>Azure Data Box için Müşteri Kasası kullanma (Önizleme)

Azure Data Box, müşteri verilerini Azure 'a veya Azure 'dan aktarmak için kullanılır. Microsoft Desteği, Destek isteği sırasında müşteri verilerine erişmesi gerekebilecek örnekler vardır. Bu veri erişim isteklerini gözden geçirmek ve onaylamak veya reddetmek için Müşteri Kasası arabirim olarak kullanabilirsiniz. 

Bu makalede, Data Box içeri aktarma için Müşteri Kasası isteklerinin nasıl başlatıldığı ve izlendiği ve siparişlerin dışa aktarma işlemlerinin nasıl yapılacağı ele alınmaktadır. Makale hem Azure Data Box cihazları hem de Azure Data Box Heavy cihazları için geçerlidir. 

## <a name="devops-workflow-for-data-access"></a>Veri erişimi için DevOps iş akışı

Microsoft 'ta destek ve Data Box Işlemler ekibi, genellikle müşteri verilerine erişemez. Bunlar, standart araçları ve telemetrisi kullanarak sorunları çözmeye çalışır. <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

Sorunlar çözülemezse ve verileri araştırmak veya onarmak için Microsoft Desteği gerekiyorsa, tam zamanında (JıT) Portal aracılığıyla yükseltilmiş erişim ister. JıP portalı izin düzeyini doğrular, çok faktörlü kimlik doğrulaması sağlar ve Iç Microsoft onaylayanlardan onay içerir. Örneğin, onaylayan DevOps yöneticisi olabilir. 

Yükseltilmiş erişim isteğiniz JıT portalı aracılığıyla onaylandıktan sonra, kasayı etkinleştirdiyseniz, Microsoft, verilere erişim için açık izniniz de gerektirir. Erişim, portalda Müşteri Kasası hizmeti aracılığıyla istenir ve izlenir. 

Kasayı etkinleştirmediyseniz, verilere erişmek için onayınızı gerekli değildir.


## <a name="prerequisites-for-access-request"></a>Erişim isteği önkoşulları

Başlamadan önce şunlardan emin olun:

1. İçindeki yönergelere göre Azure Data Box bir sıra oluşturdunuz:
    1. [Öğretici:](data-box-deploy-ordered.md) içeri aktarma siparişlerinin siparişi Azure Data Box.
    1. [Öğretici:](data-box-deploy-export-ordered.md) dışa aktarma siparişlerinin siparişi Azure Data Box.

2. Data Box için Müşteri Kasası yapılandırdınız. Bu bir katılım hizmetidir. 

    1. Müşteri Kasası Şu anda Data Box hizmeti için önizleme aşamasındadır. Kuruluşunuz için Data Box Müşteri Kasası etkinleştirmek üzere [Azure genel önizlemesi için müşteri kasası](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu)kaydolun.
    2. Müşteri Kasası, en az geliştirici düzeyi olan bir Azure Destek planına sahip tüm müşteriler için otomatik olarak kullanılabilir. <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. Bu sorun için bir hizmet isteği veya destek bileti zaten açık. Destek bileti hakkında bilgi için bkz. [Data Box için hizmet Isteği dosyası](data-box-disk-contact-microsoft-support.md).


## <a name="track-approve-request-via-lockbox"></a>İzleme, isteği kasa aracılığıyla Onayla

Müşteri verilerine erişim isteğini izlemek ve onaylamak için aşağıdaki adımları izleyin:

1. Microsoft, verileri Azure veri merkezinde karşıya yükleme veya indirme sırasında bir sorun olduğunu algılar. Örneğin, Data Box sırası **veri kopyalama** aşaması sırasında durdurulur. 

    Destek Mühendisi, destek oturumu aracılığıyla Data Box bağlanır ve standart araçları ve telemetrisi kullanarak sorunu gidermeye çalışır. Data Box diskler kilitliyse ve paylaşımlar erişilebilir değilse, Destek Mühendisi bir kasa isteği oluşturur. 
 
2. İstek oluşturulduğunda, genellikle bildirim abonelik yöneticisine gider, ancak bir grubu bildirimler için de yapılandırabilirsiniz. 

3. Azure portal kasa isteğini onayınız için görebilirsiniz. 

    ![Azure portal isteği](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    Portalın kasa isteğini onaylamak için **Onayla**' yı seçebilirsiniz.

    ![İsteği Onayla](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    İstek onaylandığında, cihaz disklerinin kilidi açılır ve destek oturumunda paylaşımlara erişilebilir.

4. Destek Mühendisi karşıya yükleme sorununu çözer ve ardından destek oturumunu devre dışı bırakır.

Sorun çözümlendikten sonra, veri kopyalama işi tamamlandığında ilerlemeye devam eder.


## <a name="next-steps"></a>Sonraki adımlar

- [Microsoft Azure için Müşteri Kasası](../security/fundamentals/customer-lockbox-overview.md)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->