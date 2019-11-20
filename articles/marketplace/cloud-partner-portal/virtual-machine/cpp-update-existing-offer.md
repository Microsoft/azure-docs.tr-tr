---
title: Azure Marketi 'nde var olan bir VM teklifini güncelleştirme
description: Azure Marketi 'nde mevcut bir VM teklifinin nasıl güncelleştireceğinizi açıklar.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/27/2018
ms.author: ansud
ms.openlocfilehash: 5cbee909b4bd6353ad8fbe9fcbc126dc4a245012
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823980"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Azure Marketi 'nde mevcut bir VM teklifini güncelleştirme

Bu makalede, sanal makine (VM) teklifinizi [bulut iş ortağı portalı](https://cloudpartner.azure.com/) ve ardından teklifi yeniden yayımlayacağınız farklı yönleri açıklanmaktadır. 

Teklifinizi güncelleştirmeniz için kullanabileceğiniz çeşitli çok sayıda, aşağıdakiler de dahil olmak üzere, şunlar vardır:

-  Mevcut SKU 'Lara yeni bir VM görüntüsü sürümü ekleyin
-  Bir SKU 'nun kullanılabildiği bölgeleri değiştirme
-  Yeni SKU 'Lar Ekle
-  Teklif veya bağımsız SKU 'Lar için Market meta verilerini güncelleştirme
-  Kullandıkça Öde tekliflarında fiyatlandırmayı güncelleştirme

Bu değişiklikler konusunda size yardımcı olmak için Portal **karşılaştırma** ve **geçmiş** özelliklerini sunmaktadır.  

>[!Note]
>Bulut çözümü sağlayıcıları (CSP) iş ortağı kanalı kabul etme artık kullanılabilir.  Teklifinizi Microsoft CSP iş ortağı kanalları aracılığıyla pazarlama hakkında daha fazla bilgi için lütfen bkz. [bulut çözümü sağlayıcıları](../../cloud-solution-providers.md) .

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>VM teklifinin veya SKU 'sunda izin verilmeyen değişiklikler

Teklif Azure Marketi 'nde canlı olduktan sonra değiştirilemeyen bir VM teklifinin veya SKU 'sunun bazı öznitelikleri vardır:

-  Teklifin **TEKLIF kimliği** ve **Yayımcı kimliği**
-  Mevcut SKU 'ların **SKU kimliği**
-  Mevcut SKU 'ların veri diski sayısı
-  Faturalandırma/lisans modeli, mevcut SKU 'Larda değişir
-  Yayınlanmış bir SKU 'ya fiyat artar


## <a name="common-update-operations"></a>Ortak güncelleştirme işlemleri

Bir VM teklifinde değiştirebileceğiniz çok çeşitli özellikler olsa da, aşağıdaki işlemler yaygındır.

### <a name="update-the-vm-image-version-for-a-sku"></a>SKU için VM görüntüsü sürümünü güncelleştirme

Bir VM görüntüsünün güvenlik düzeltme ekleri, ek özellikler vb. ile düzenli olarak güncelleştirilmesini yaygındır.  Bu senaryolarda, aşağıdaki adımları kullanarak SKU 'nuzun VM görüntüsünü güncelleştirmek istersiniz:

1.  [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.

2.  **Tüm teklifler**altında, güncelleştirilecek teklifi bulun.

3.  **SKU 'lar** sekmesinde, güncelleştirilecek VM görüntüsüyle ilişkili SKU 'ya tıklayın.

4.  Yeni bir VM görüntüsü eklemek için **disk sürümü**' nün altında **+ yeni disk sürümü** ' ne tıklayın.

5.  Yeni VM görüntüleri **disk sürümünü**sağlayın. Disk sürümünün [anlam sürümü](https://semver.org/) biçimini izlemesi gerekir. Sürümler X. Y. Z biçiminde olmalıdır; burada X, Y ve Z tamsayılardır. Sağladığınız yeni sürümün önceki sürümlerden daha büyük olduğunu doğrulayın; Aksi halde, yeni sürümü yeniden yayımladıktan sonra portalda veya Azure Marketi 'nde görüntülenmez.

6.  **OS VHD URL 'si**için, IŞLETIM sistemi VHD 'si için oluşturulan [paylaşılan ERIŞIM imzası (SAS) URI](./cpp-get-sas-uri.md) 'sini girin. 

    > [!WARNING] 
    > Veri diski sayısı, SKU 'nun farklı sürümleri arasında değişiklik alamaz. Önceki sürümlerde veri diskleri yapılandırılmışsa, bu yeni sürümün aynı sayıda veri diskine de sahip olması gerekir.

7.  Yeni VM sürümünüzü Azure Marketi 'nde yayımlamak üzere iş akışını başlatmak için **Yayımla** ' ya tıklayın.


### <a name="change-region-availability-of-a-sku"></a>SKU 'nun bölge kullanılabilirliğini değiştirme

Zaman içinde teklifinizin/SKU 'nuzu daha fazla bölgede kullanılabilir hale getirmek isteyebilirsiniz.  Alternatif olarak, belirli bir bölgedeki teklif/SKU 'YU desteklemeyi durdurmak isteyebilirsiniz.
Kullanılabilirliği değiştirmek için aşağıdaki adımları kullanın:

1.  [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.

2.  **Tüm teklifler** altında güncelleştirmek istediğiniz teklifi bulun.

3.  **SKU 'lar** sekmesinde, kullanılabilirliğini DEğIşTIRMEk istediğiniz SKU 'ya tıklayın.

4.  **Ülke/bölge kullanılabilirliği** alanının altındaki **ülkeleri Seç** düğmesine tıklayın.

5.  Bölge kullanılabilirliği açılır penceresinde, bu SKU 'nun bölgelerini ekleyin veya kaldırın.

6.  SKU bölgenizin kullanılabilirliğini güncelleştirmek üzere Yayımla iş akışını başlatmak için **Yayımla** ' ya tıklayın.

Bir SKU yeni bir bölgede kullanılabilir duruma getiriliyorsa, **fiyatlandırma verilerini dışarı aktar** işlevini kullanarak söz konusu bölge için fiyatlandırma belirtme olanağına sahip olursunuz. Daha önce kullanılabilir bir bölgeyi geri ekliyorsanız fiyatlandırma değişikliklerine izin verilmediğinden fiyatlandırma güncelleyemeyeceksiniz.


### <a name="add-a-new-sku"></a>Yeni bir SKU Ekle

Mevcut teklifiniz için yeni bir SKU 'YU kullanılabilir hale getirmek için aşağıdaki adımları kullanın: 

1.  [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.

2.  **Tüm teklifler** altında güncelleştirmek istediğiniz teklifi bulun.

3.  **SKU 'lar** sekmesinde, **Yeni SKU Ekle** ' ye tıklayın ve açılır pencerede bir **SKU kimliği** sağlayın.

4.  [Sanal makineyi Azure Marketi 'Nde yayımlama](./cpp-publish-offer.md)makalesinde ayrıntılı olarak VM 'yi yeniden yayımlayın.

5.  Yeni SKU 'nuzu yayımlamak üzere iş akışını başlatmak için **Yayımla** ' ya tıklayın.


### <a name="update-offer-marketplace-metadata"></a>Teklif marketi meta verilerini Güncelleştir

Teklifinizle ilişkili olan market meta verilerini (Şirket adı, logolar vb.) güncelleştirmek için aşağıdaki adımları kullanın: 

1.  [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.

2.  **Tüm teklifler** altında güncelleştirmek istediğiniz teklifi bulun.

3.  **Market** sekmesine gidin ve meta verileri değişiklik yapmak Için [Azure Marketi 'ne bir sanal makine yayımlama](./cpp-publish-offer.md) makalesindeki yönergeleri izleyin.

4.  Değişikliklerinizi yayımlamak üzere iş akışını başlatmak için **Yayımla** ' ya tıklayın.


### <a name="update-pricing-on-published-offers"></a>Yayımlanmış tekliflerle ilgili fiyatlandırmayı güncelleştirme

Kullandıkça Öde teklifiniz yayımlandıktan sonra, SKU fiyatlandırmasını doğrudan artırabilirsiniz.  (Ancak, aynı teklif altında yeni bir SKU oluşturabilir, eski SKU 'yu silebilir ve sonra teklifinizi yeni müşteriler için yeniden yayımlayabilirsiniz.)  Buna karşılık, aşağıdaki adımları kullanarak yayımlanmış bir teklifin fiyatını azaltabilirsiniz:

1.  [Bulut iş ortağı portalı](https://cloudpartner.azure.com/)oturum açın.

2.  **Tüm teklifler**altında, güncelleştirilecek teklifi bulun.

3.  Fiyatlandırmasını azaltmak istediğiniz SKU 'ya tıklayın.

4.  1x1 GUI 'de fiyatlandırmayı ayarladıysanız, fiyatı kullanıcı arabiriminden doğrudan değiştirebilirsiniz. İçeri/dışarı aktarma elektronik tablosu aracılığıyla fiyatlandırma ayarlarsanız, fiyatları yalnızca içeri/dışarı aktarma özelliği aracılığıyla azaltabilirsiniz.

3.  **Kaydet** düğmesine tıklayın.

4.  Değişikliklerinizi yayımlamak üzere iş akışını başlatmak için **Yayımla** ' ya tıklayın.

Yeni azaltılan fiyatlandırma, Web sitesinde etkin olduktan sonra yeni müşterilere görünür olacaktır.  Bu yeni fiyat, müşterilerinizi aşağıdaki yollarla etkiler:

- Yeni müşteriler bu yeni ücret üzerinden ücretlendirilecektir. 
- Mevcut müşteriler için fiyat azalmasıyla, Fiyat azalmasıyla ilgili faturalandırma döngüsünün başlangıcına geriye dönük olarak yansıtılyacaktır.
Bir fiyat azalmasıyla ilgili bir dönem için zaten faturalandırılmışsa, daha düşük bir fiyat elde etmek üzere bir sonraki faturalandırma sürecinde bir para iadesi alırlar.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Basitleştirilmiş para birimi fiyatlandırması

1 2018 Eylül 'den itibaren, portala **Basitleştirilmiş para birimi fiyatlandırması** adlı yeni bir bölüm eklenecektir. Microsoft, müşterilerinizden dünya genelindeki daha öngörülebilir fiyatlandırma ve koleksiyonlar sağlayarak Azure Market işletmesini kolaylaştırıyor. Bu hızlandırma, müşterilerinize faturadığımız para birimi sayısını azaltmayı içerir.

Yeni bölüm bu yeni para birimlerinde fiyatlandırma kazanacak. Tüm müşteriler bu yeni kapatma para birimlerine geçirildikten sonra, orijinal fiyatlandırma bölümü kullanımdan kaldırılır ve yalnızca Basitleştirilmiş para birimi fiyatlandırma bölümü kalır.

1 Kasım 2018 ' e kadar, kapatma para birimi 'nin değişmekte olduğu bölgeler için yeni bir fiyat ayarlamanız gerekir. Kapatma para birimiyle değişmeyen bölgelerin fiyatını artıramezsiniz.

> [!NOTE] 
> Teklifinizi yayımlamak için API 'Ler kullanıyorsanız, bu teklif JSON içinde yeni bir bölüm görebilirsiniz. Bu, teklifin türüne bağlı olarak `virtualMachinePricingV2` veya `monthlyPricingV2`olarak açıklanmalıdır. 

Bu değişiklik hakkında sorularınız varsa [Azure Market desteği](../../support-azure-marketplace.md)'ne başvurun.


## <a name="compare-feature"></a>Karşılaştırma özelliği

Önceden yayımlanmış bir teklifte değişiklik yaptığınızda, yapılan değişiklikleri denetlemek için **karşılaştırma** özelliğinden yararlanabilirsiniz. Bu özelliği kullanmak için:

1.  İstediğiniz herhangi bir noktada, teklifiniz için **Karşılaştır** düğmesine tıklayın.

    ![Özellik düğmesini Karşılaştır](./media/publishvm_037.png)


2.  Pazarlama varlıklarının ve meta verilerin yan yana sürümlerini görüntüleyin.


## <a name="history-of-publishing-actions"></a>Yayımlama eylemlerinin geçmişi

Geçmiş yayımlama etkinliklerini görüntülemek için Bulut İş Ortağı Portalı sol gezinti menü çubuğundan **Geçmiş** öğesine tıklayın. Burada, Azure Market tekliflerinizin kullanım ömrü boyunca alınmış olan zaman damgamış eylemleri görüntüleyebileceksiniz.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

