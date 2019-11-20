---
title: Azure ExpressRoute 'ı Oracle bulut altyapısıyla bağlama | Microsoft Docs
description: Platformlar arası Oracle uygulama çözümlerini etkinleştirmek için Azure ExpressRoute 'ı Oracle Cloud Infrastructure (OCı) FastConnect ile bağlama
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2019
ms.author: rogirdh
ms.openlocfilehash: 63543c0ac34536b736bd4b8cdbd47fdd98e9f9be
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802203"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Azure ile Oracle bulut altyapısı arasında doğrudan bir iç bağlantı kurma  

[Tümleşik bir çok kiracılı deneyim](oracle-oci-overview.md) (Önizleme) oluşturmak için Microsoft ve Oracle, [ExpressRoute](../../../expressroute/expressroute-introduction.md) ve [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)aracılığıyla Azure ile Oracle bulut altyapısı (OCI) arasında doğrudan bağlantı sağlar. ExpressRoute ve FastConnect bağlantısı aracılığıyla müşteriler, iki bulut arasında düşük gecikme süresi, yüksek aktarım hızı, özel doğrudan bağlantı ile karşılaşabilir.

> [!IMPORTANT]
> Microsoft Azure ile OCı arasındaki bağlantı önizleme aşamasındadır. Azure ile OCı arasında düşük gecikmeli bağlantı kurmak için, bu özellik için önce Azure aboneliğinizin etkinleştirilmesi gerekir. Bu kısa [anket formunu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu)tamamlayarak önizlemeye kaydolmalısınız. Aboneliğiniz kaydedildiğinde siz de bir e-posta alırsınız. Onay e-postası alınana kadar özelliği kullanamazsınız. Ayrıca, bu önizleme için etkinleştirilecek Microsoft temsilcinize de başvurabilirsiniz. Önizleme özelliğine erişim, Microsoft 'un kendi takdirine bağlı olarak kullanılabilirliğine ve kısıtlanmasını sağlar. Anketin tamamlanması erişimi garanti etmez. Bu önizleme, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için kullanılmamalıdır. Belirli özellikler desteklenmiyor olabilir, kısıtlı yeteneklere sahip olabilir veya tüm Azure konumlarında mevcut olmayabilir. Ayrıntılar için Microsoft Azure önizlemeleri için [ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) bölümüne bakın. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

Aşağıdaki görüntüde, iç bağlantı için üst düzey bir genel bakış gösterilmektedir:

![Platformlar arası ağ bağlantısı](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Önkoşullar

* Azure ile OCı arasında bağlantı kurmak için etkin bir Azure aboneliğiniz ve etkin bir OCı kiralamanın olması gerekir.

* Yalnızca bir Azure ExpressRoute eşleme konumunun, OCı FastConnect ile aynı eşleme konumuna eşit olduğu durumlarda bağlantı kurulabilir. Bkz. [Önizleme sınırlamaları](oracle-oci-overview.md#preview-limitations).

* Bu önizleme özelliği için Azure aboneliğinizin etkinleştirilmesi gerekir.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>ExpressRoute ve FastConnect arasında doğrudan bağlantı yapılandırma

1. Kaynak grubu altında Azure aboneliğinizde standart bir ExpressRoute devresi oluşturun. 
    * ExpressRoute oluştururken, hizmet sağlayıcı olarak **Oracle Cloud FastConnect** ' i seçin. ExpressRoute bağlantı hattı oluşturmak için, bkz. adım adım [Kılavuzu](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Azure ExpressRoute bağlantı hattı, ayrıntılı bant genişliği seçenekleri sunar, ancak FastConnect 1, 2, 5 veya 10 Gbps 'yi destekler. Bu nedenle, ExpressRoute altındaki bu eşleşen bant genişliği seçeneklerinden birini seçmeniz önerilir.

    ![ExpressRoute devresi oluşturma](media/configure-azure-oci-networking/exr-create-new.png)
1. ExpressRoute **hizmet anahtarınızı**aklınızda edin. FastConnect devrenizi yapılandırırken anahtarı sağlamanız gerekir.

    ![ExpressRoute hizmet anahtarı](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > ExpressRoute bağlantı hattı sağlandığında ( **sağlayıcı durumu** **sağlanmadığından**bile) ExpressRoute ücretleri için faturalandırılırsınız.

1. Azure sanal ağınız veya OCı sanal bulut ağı IP adresi alanı ile örtüşmeyen her biri/30 olan iki özel IP adresi alanını carın. İlk IP adresi alanına birincil adres alanı ve ikinci IP adresi alanı ikincil adres alanı olarak başvuracağız. FastConnect devrenizi yapılandırırken ihtiyacınız olan adresleri aklınızda edin.
1. Dinamik yönlendirme ağ geçidi (DRG) oluşturun. FastConnect devrenizi oluştururken buna ihtiyacınız olacak. Daha fazla bilgi için bkz. [dinamik yönlendirme ağ geçidi](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) belgeleri.
1. Oracle kiracınız kapsamında bir FastConnect devresi oluşturun. Daha fazla bilgi için [Oracle belgelerine](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)bakın.
  
    * FastConnect yapılandırması altında **Microsoft Azure: sağlayıcı olarak ExpressRoute** ' u seçin.
    * Önceki adımda sağladığınız dinamik yönlendirme ağ geçidini seçin.
    * Sağlanacak bant genişliğini seçin. En iyi performans için, bant genişliğinin ExpressRoute bağlantı hattı oluşturulurken seçilen bant genişliğiyle eşleşmesi gerekir.
    * **Sağlayıcı hizmet anahtarı**' nda ExpressRoute hizmet anahtarını yapıştırın.
    * **BIRINCIL BGP IP adresi** ve **İkincil BGP IP** adresi için ıkıncı/30 özel IP adresi alanı için önceki bir adımda yer alan Ilk/30 özel IP adresi alanını kullanın.
        * Oracle BGP IP adresi (birincil ve ikincil) için iki aralığın ilk kullanışlı adresini ve ikinci adresi Müşteri BGP IP adresine (FastConnect perspektifinden) atayın. İlk kullanılan IP adresi/30 adres alanındaki ikinci IP adresidir (ilk IP adresi Microsoft tarafından ayrılmıştır).
    * **Oluştur**’a tıklayın.
1. Yönlendirme tablosunu kullanarak, dinamik yönlendirme ağ geçidi aracılığıyla Oracle kiracınız kapsamındaki sanal bulut ağına FastConnect bağlantısını tamamen yapın.
1. Azure 'a gidin ve ExpressRoute devrenizin **sağlayıcının durumunun** **sağlandı** olarak değiştirildiğinden ve **Azure Private** türünde bir eşlemenin sağlandığından emin olun. Bu, aşağıdaki adımlarla ilgili bir önkoşul değildir.

    ![ExpressRoute sağlayıcısı durumu](media/configure-azure-oci-networking/exr-provider-status.png)
1. **Azure özel** eşlemesi ' ne tıklayın. Bu, FastConnect devrenizi ayarlarken girdiğiniz bilgilere göre eşleme ayrıntılarının otomatik olarak yapılandırıldığını görürsünüz.

    ![Özel eşleme ayarları](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Sanal ağı ExpressRoute 'a bağla

1. Henüz yapmadıysanız bir sanal ağ ve sanal ağ geçidi oluşturun. Ayrıntılar için bkz. [adım adım Kılavuzu](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. [Terrayform betiğini](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) çalıştırarak veya [ExpressRoute FastPath 'ı yapılandırmak](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)için PowerShell komutunu yürüterek sanal ağ geçidi ile ExpressRoute bağlantı hattı arasındaki bağlantıyı ayarlayın.

Ağ yapılandırmasını tamamladıktan sonra, Azure portal ExpressRoute özel eşleme dikey penceresinde **ARP kayıtlarını al** ve **yol al tablosu** ' na tıklayarak yapılandırmanızın geçerliliğini doğrulayabilirsiniz.

## <a name="automation"></a>Otomasyon

Microsoft, ağ bağlantısı otomatik dağıtımını etkinleştirmek için Teraform betikleri oluşturdu. Terrayform betikleri, Azure aboneliğinde yeterli izinleri gerektirdiğinden yürütmeden önce Azure ile kimlik doğrulaması yapmak için gereklidir. Kimlik doğrulaması, bir [Azure Active Directory hizmet sorumlusu](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) veya Azure CLI kullanılarak gerçekleştirilebilir. Daha fazla bilgi için bkz. [Terrayform belgeleri](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Bu [GitHub deposunda](https://aka.ms/azureociinterconnecttf),-Connect ile bağlantı kurmak için Teraform betikleri ve ilgili belgeler bulunabilir.

## <a name="monitoring"></a>İzleme

Her iki bulutda aracı yükleme, uçtan uca ağın performansını izlemek için Azure [ağ performansı İzleyicisi (NPM)](../../../expressroute/how-to-npm.md) özelliğinden yararlanabilirsiniz. NPM, ağ sorunlarını kolayca belirlemenize yardımcı olur ve bunları ortadan kaldırmaya yardımcı olur.

## <a name="delete-the-interconnect-link"></a>InterConnect bağlantısını silme

InterConnect 'i silmek için aşağıdaki adımların belirtilen belirli bir sırada izlenmelidir. Bunun yapılmaması, "başarısız durum" ExpressRoute devresine neden olur.

1. ExpressRoute bağlantısını silin. Bağlantınız için sayfadaki **Sil** simgesine tıklayarak bağlantıyı silin. Daha fazla bilgi için bkz. [ExpressRoute belgeleri](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Oracle 'ın bulut konsolundan Oracle FastConnect 'i silin.
1. Oracle FastConnect devresi silindikten sonra Azure ExpressRoute devresini silebilirsiniz.

Bu noktada, silme ve sağlamayı kaldırma işlemi tamamlanmıştır.

## <a name="next-steps"></a>Sonraki adımlar

* OCı ve Azure arasında çapraz bulut bağlantısı hakkında daha fazla bilgi için [Oracle belgelerine](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)bakın.
* Azure üzerinde hedeflenen Oracle uygulamaları için altyapı dağıtmak üzere [Terrayform betikleri](https://aka.ms/azureociinterconnecttf) kullanın ve ağ bağlantısını yapılandırın. 
