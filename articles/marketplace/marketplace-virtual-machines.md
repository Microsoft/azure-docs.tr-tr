---
title: Bir sanal makine teklifi planlayın-Microsoft ticari Market
description: Bu makalede, Azure Marketi 'ne bir sanal makine teklifi yayımlama gereksinimleri açıklanmaktadır.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 6e391ca79c9dcf9ab533a7e67961c654b5f8630b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103377"
---
# <a name="plan-a-virtual-machine-offer"></a>Sanal makine teklifi planlama

Bu makalede, ticari Market 'e bir sanal makine (VM) teklifi yayımlamaya yönelik farklı seçenekler ve gereksinimler açıklanmaktadır. VM teklifleri, Azure Marketi aracılığıyla dağıtılan ve faturalandırılan transactable tekliflerdir.

Başlamadan önce, [Iş Ortağı Merkezi 'nde bir ticari Market hesabı oluşturun](create-account.md) ve hesabınızın ticari Market programına kaydedildiğinden emin olun.

### <a name="technical-fundamentals"></a>Teknik temel bilgiler

Teklifleri tasarlama, oluşturma ve test etme işlemi zaman alır ve hem Azure platformunda hem de teklifinizi oluşturmak için kullanılan teknolojilerde uzmanlık gerektirir. Mühendislik ekibinizde [Azure sanal makineleri](https://azure.microsoft.com/services/virtual-machines/), [Azure depolama](https://azure.microsoft.com/services/?filter=storage#storage)ve [Azure ağı](https://azure.microsoft.com/services/?filter=networking#networking)ile Azure [uygulamalarının tasarımı ve mimarisiyle](https://azure.microsoft.com/solutions/architecture/)ilgili bir bilgiye sahip olmanız gerekir. Şu ek teknik kaynaklara bakın: 

- Öğreticiler
  - [Linux VM'leri](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Windows VM'leri](../virtual-machines/windows/tutorial-manage-vm.md)

- Örnekler
  - [Linux VM 'Ler için Azure CLı örnekleri](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Linux sanal makineleri için Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
  - [Windows VM 'Leri için Azure CLı örnekleri](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Windows VM 'Leri için Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

## <a name="technical-requirements"></a>Teknik gereksinimler

VM teklifleri aşağıdaki teknik gereksinimlere sahiptir:

- Bir işletim sistemi sanal sabit diski (VHD) hazırlamanız gerekir. Veri diski VHD 'leri isteğe bağlıdır. Bu, aşağıda daha ayrıntılı olarak açıklanmıştır.
- Müşteri teklifinizi dilediğiniz zaman iptal edebilir.
- Teklifiniz için en az bir plan oluşturmanız gerekir. Planınız, seçtiğiniz [lisanslama seçeneğine](#licensing-options) göre fiyatlandırılır.
   > [!IMPORTANT]
   > Bir plandaki her sanal makine görüntüsünün aynı sayıda veri diski olmalıdır.

VM iki bileşen içerir:

- **İşletim VHD 'si** : teklifinizle birlikte dağıtılan işletim sistemini ve çözümü içerir. VHD 'yi hazırlama işlemi, bir Linux, Windows-veya özel tabanlı VM olmasına bağlı olarak farklılık gösterir.
- **Veri diski VHD** 'leri (isteğe bağlı) – bir VM için ayrılmış, kalıcı depolama. Kalıcı bilgileri depolamak için işletim sistemi VHD 'sini (örneğin, C: sürücüsü) kullanmayın. 
    - En fazla 16 veri diski ekleyebilirsiniz.
    - Disk boş olsa bile, veri diski başına bir VHD kullanın.

    > [!NOTE]
    > Kullandığınız işletim sisteminden bağımsız olarak yalnızca çözüm için gereken en az sayıda veri diski ekleyin. Müşteriler, dağıtım sırasında bir görüntünün parçası olan diskleri kaldıramaz, ancak dağıtım sırasında veya sonrasında her zaman disk ekleyebilirler.

Teknik varlıklarınızı hazırlama hakkında ayrıntılı yönergeler için bkz. [onaylanan temel kullanarak sanal makine oluşturma](azure-vm-create-using-approved-base.md) veya [kendi görüntünüzü kullanarak bir sanal makine oluşturma](azure-vm-create-using-own-image.md).

## <a name="preview-audience"></a>İzleyiciyi Önizle

Önizleme hedef kitlesi, canlı yayımlamadan önce uçtan uca işlevselliği test etmek üzere Azure Marketi 'nde canlı yayımlamadan önce sanal makine teklifinizin erişimine erişebilir. **Izleyiciyi Önizle** sayfasında, sınırlı bir önizleme hedef kitlesi tanımlayabilirsiniz. 

> [!NOTE]
> Önizleme hedef kitlesi özel bir plandan farklıdır. Özel bir plan yalnızca seçtiğiniz belirli bir hedef kitle için kullanılabilir hale getirir. Bu, belirli müşterilerle özel bir plana anlaşma yapmanızı sağlar. Daha fazla bilgi için sonraki bölüme bakın: planlar.

Davetleri Microsoft hesabı (MSA) veya Azure Active Directory (Azure AD) e-posta adreslerine gönderebilirsiniz. 10 ' a kadar e-posta adresi ekleyin veya bir. csv dosyası ile 20 ' ye kadar içeri aktarın. Teklifiniz zaten canlı ise, teklifiniz için herhangi bir değişikliği veya güncelleştirmeyi test etmek üzere bir önizleme hedef kitlesi tanımlayabilirsiniz.

## <a name="plans-and-pricing"></a>Planlar ve fiyatlandırma

VM teklifleri için en az bir plan gereklidir. Bir plan, çözüm kapsamını ve sınırlarını ve ilişkili fiyatlandırmayı tanımlar. Müşterilerinizin farklı teknik ve lisanslama seçeneklerine ek olarak ücretsiz denemelerdeki ve ücretsiz denemeler sunmak için teklifiniz için birden çok plan oluşturabilirsiniz. Fiyatlandırma modelleri, ücretsiz denemeler ve özel planlar dahil olmak üzere planlar hakkında genel rehberlik teklifleri için bkz. [planlar ve fiyatlandırma](plans-pricing.md) . 

VM 'Ler, Kullandıkça Öde veya kendi lisansını getir (KLG) lisanslama modellerini kullanarak tam olarak ticari olarak etkinleştirilmiştir. Microsoft, ticari işlemi barındırır ve müşterinizin adına fatura alır. Müşteri ile Microsoft arasında, kurumsal sözleşmeler dahil tercih edilen ödeme ilişkilerini kullanmanın avantajını elde edersiniz. Daha fazla bilgi için bkz. [ticari Market Transact özellikleri](./marketplace-commercial-transaction-capabilities-and-considerations.md).

> [!NOTE]
> Bir Kurumsal Anlaşma ilişkili Azure ön ödemesi (daha önce parasal taahhüt olarak adlandırılır), VM 'nizin Azure kullanımında kullanılabilir, ancak yazılım lisans ücretlerine karşı kullanılamaz.

### <a name="licensing-options"></a>Lisanslama seçenekleri

Yeni bir VM teklifi yayımlamaya hazırlanırken, hangi lisans seçeneğinin seçileceğine karar vermeniz gerekir. Bu, daha sonra teklifinizi Iş Ortağı Merkezi 'nde oluştururken sağlamanız gereken ek bilgileri saptayacaktır.

Bunlar VM teklifleri için kullanılabilir lisanslama seçenekleridir:

| Lisans seçeneği | İşlem işlemi |
| --- | --- |
| Ücretsiz deneme sürümü | Müşterilerinize bir adet, üç veya altı aylık ücretsiz deneme sürümü sunun. |
| Sınama sürücüsü | Bu seçenek, müşterilerinizin VM 'Leri hiçbir ek ücret ödemeden değerlendirmelerini sağlar. Deneme deneyimiyle iletişim kurmak için mevcut bir Azure müşterisi olması gerekmez. Ayrıntılar için bkz. [test sürücüsü nedir?](./what-is-test-drive.md) |
| KLG | Kendi lisansını getir seçeneği, müşterilerinizin mevcut yazılım lisanslarını Azure 'a getirmelerini sağlar.\* |
| Kullanım tabanlı | Kullandıkça Öde olarak da bilinen bu seçenek, müşterilerinizin saat başına ödeme yapmayı sağlar. |
| Etkileşimli tanıtım  | Etkileşimli bir tanıtım kullanarak müşterilerinize çözümünüze yönelik kılavuzlu bir deneyim sunun. Avantajı, karmaşık çözümünüz için karmaşık bir kurulum sağlamak zorunda kalmadan bir deneme deneyimi sunabilmeniz sağlamaktır. |
|

\* Yayımcı olarak, yazılım lisans işleminin sipariş, tamamlama, ölçüm, faturalandırma, faturalama, ödeme ve koleksiyon dahil olmak üzere tüm yönlerini destekliyoruz.

Aşağıdaki örnekte, Azure Marketi 'nde kullanım tabanlı fiyatlandırmaya sahip bir VM teklifi gösterilmektedir.

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="Örnek VM teklifi ekranı.":::

### <a name="private-plans"></a>Özel planlar

Görüntüyü ve fiyatlandırmayı özel bir plan olarak yayımlayarak, sanal makinenizin bulma ve dağıtımını belirli bir müşteri kümesiyle kısıtlayabilirsiniz. Özel planlar, en yakın müşterileriniz için özel teklifler oluşturma ve özelleştirilmiş yazılım ve hüküm sunma imkanını açar. Özelleştirilmiş terimler, özel fiyatlandırma ve koşullara sahip alan ve sınırlı yayın yazılımına erken erişim dahil olmak üzere çeşitli senaryolar vurgulamanızı sağlar. Özel planlar, belirli fiyatları veya ürünleri sınırlı bir müşteri kümesine vermenizi sağlar.

Daha fazla bilgi için bkz. [Microsoft ticari marketi 'nde](private-offers.md) [ticari Market teklifleri ve özel teklifler için planlar ve fiyatlandırma](plans-pricing.md) .

## <a name="test-drive"></a>Sınama sürücüsü

VM 'niz için bir test sürücüsünü etkinleştirmeyi seçebilirsiniz. Test sürücüleri, müşterilere sabit sayıda saat boyunca önceden yapılandırılmış bir ortama erişim sağlar. Herhangi bir yayımlama seçeneği için test sürücüleri etkinleştirebilirsiniz, ancak bu özellik ek gereksinimlere sahiptir. Sınama sürücüleri hakkında daha fazla bilgi için bkz. [test sürücüsü nedir?](what-is-test-drive.md). Farklı türlerde test sürücüleri yapılandırma hakkında daha fazla bilgi için bkz. [Test Drive Technical Configuration](test-drive-technical-configuration.md).

> [!TIP]
> Bir test sürücüsü, [ücretsiz deneme](plans-pricing.md#free-trials)sürümünden farklıdır. Bir sınama sürücüsü, ücretsiz deneme veya her ikisini birden sağlayabilirsiniz. Bunlar her ikisi de müşterilerinizi sabit bir süre için sağlar. Ancak, bir sınama sürücüsü Ayrıca, ürününüzün temel özelliklerine ve avantajlarına yönelik uygulamalı, kendinden kılavuzlu bir tura sahip olan gerçek hayatta bir uygulama senaryosunda gösterilmekte olan avantajlı bir geliştirme içerir.

## <a name="customer-leads"></a>Müşteri liderleri

Müşteri bilgilerini toplamak için teklifinizi müşteri ilişkileri yönetimi (CRM) sisteminize bağlamanız gerekir. Müşterinin, bilgilerini paylaşması için izin istenir. Teklif adı, KIMLIĞI ve çevrimiçi mağazalarla birlikte bu müşteri ayrıntıları, yapılandırdığınız CRM sistemine gönderilir. Ticari Market, Azure tablosu kullanma veya Power otomatikleştirmeyi kullanarak bir HTTPS uç noktası yapılandırma seçeneğiyle birlikte çeşitli CRM sistemlerini destekler.

Teklif oluşturma sırasında veya sonrasında bir CRM bağlantısını dilediğiniz zaman ekleyebilir veya değiştirebilirsiniz. Ayrıntılı kılavuz için bkz. [ticari Market teklifinizin müşteri adayları](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Yasal sözleşmeler

Müşteriler için satın alma sürecini basitleştirmek ve yazılım satıcılarının yasal karmaşıklığını azaltmak için, Microsoft, ticari Market 'teki tekliflerinizi kullanabileceğiniz standart bir sözleşme sunar. Yazılımınızı standart sözleşme kapsamında sunışınızda, müşterilerin yalnızca bir kez okuyup kabul etmesi ve özel hüküm ve koşullar oluşturmanız gerekmez.

Standart sözleşmeyi kullanmayı seçerseniz, standart sözleşmeye genel değişiklik koşullarını ve en fazla 10 özel değişikliği ekleme seçeneğiniz vardır. Standart sözleşme yerine kendi hüküm ve koşullarınızı da kullanabilirsiniz. Bu ayrıntıları **Özellikler** sayfasında yöneteceksiniz. Ayrıntılı bilgi için bkz. [Microsoft Commercial Market Için standart sözleşme](standard-contract.md).

> [!NOTE]
> Ticari Market için standart sözleşmeyi kullanarak bir teklifi yayımladıktan sonra, kendi özel hüküm ve koşullarınızı kullanamazsınız. Bu bir "veya" senaryosudur. Çözümünüzü standart sözleşme kapsamında ya da kendi hüküm ve koşullarınız için sunun. Standart sözleşmenin şartlarını değiştirmek istiyorsanız, standart sözleşme değişiklikleri aracılığıyla bunu yapabilirsiniz.

## <a name="cloud-solution-providers"></a>Bulut Çözüm Sağlayıcıları

Iş Ortağı Merkezi 'nde teklifinizi oluştururken, **CSP aracılığıyla yeniden satışın** olduğunu görürsünüz. Bu seçenek, Microsoft Bulut çözüm sağlayıcıları (CSP) programının parçası olan iş ortaklarının sanal makineyi bir paketlenmiş teklifin parçası olarak yeniden satmasını sağlar. Kendi lisansını getir (KLG) planları otomatik olarak programa kabul edilir. KLG olmayan planlarınızı kabul etmek de tercih edebilirsiniz. Daha fazla bilgi için bkz. [bulut çözümü sağlayıcısı programı](cloud-solution-providers.md) . 

> [!NOTE]
> Bulut çözümü sağlayıcısı (CSP) iş ortağı kanalı kabul etme artık kullanılabilir. Teklifinizi Microsoft CSP iş ortağı kanalları aracılığıyla pazarlama hakkında daha fazla bilgi için bkz. [**Cloud Solution Providers**](./cloud-solution-providers.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Marketi 'nde sanal makine teklifi oluşturma](azure-vm-create.md)
- [Onaylanan bir temel kullanarak bir sanal makine oluşturun](azure-vm-create-using-approved-base.md) veya [kendi görüntünüzü kullanarak bir sanal makine oluşturun](azure-vm-create-using-own-image.md).
- [En iyi teklif listeleme deneyimleri](gtm-offer-listing-best-practices.md)
