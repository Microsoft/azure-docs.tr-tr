---
title: CloudSimple tarafından Azure VMware çözümü-özel bulut için vSAN şifrelemesini yapılandırma
description: CloudSimple özel bulutunuzun Azure sanal ağınızda çalışan bir anahtar yönetim sunucusu ile çalışabilmesi için vSAN yazılım şifreleme özelliğinin nasıl yapılandırılacağını açıklar.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972324"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>CloudSimple özel bulutu için vSAN şifrelemesini yapılandırma

VSAN yazılım şifreleme özelliğini, CloudSimple özel bulutunuzun Azure sanal ağınızda çalışan bir anahtar yönetim sunucusu ile çalışabilmesi için yapılandırabilirsiniz.

VMware, vSAN şifrelemesi kullanılırken bir dış KMıP 1,1 uyumlu üçüncü taraf anahtar yönetim sunucusu (KMS) aracının kullanılmasını gerektirir. VMware tarafından sertifikalı ve Azure için kullanılabilir olan tüm desteklenen KMS 'den yararlanabilirsiniz.

Bu kılavuzda, Azure sanal ağında çalışan HyTrust KeyControl KMS 'nin nasıl kullanılacağı açıklanmaktadır. Aynı yaklaşım, vSAN için diğer sertifikalı üçüncü taraf KMS çözümleri için kullanılabilir.

Bu KMS çözümü şunları yapmanızı gerektirir:

* Azure sanal ağınızda bir VMware sertifikalı üçüncü taraf KMS aracını yükleyip yönetin.
* KMS aracı için kendi lisanslarınızı belirtin.
* Azure sanal ağınızda çalışan üçüncü taraf KMS aracını kullanarak özel bulutunuzda vSAN şifrelemesini yapılandırın ve yönetin.

## <a name="kms-deployment-scenario"></a>KMS dağıtım senaryosu

KMS sunucusu kümesi, Azure sanal ağınızda çalışır ve yapılandırılmış Azure ExpressRoute bağlantısı üzerinden özel bulut vCenter 'dan IP 'ye erişilebilir.

![.. Azure sanal ağ 'da/media/KMS kümesi](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>Çözümü dağıtma

Dağıtım işlemi aşağıdaki adımlara sahiptir:

1. [Önkoşulların karşılandığından emin olun](#verify-prerequisites-are-met)
2. [CloudSimple Portalı: ExpressRoute eşleme bilgilerini alın](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure portalı: Sanal ağınızı özel buluta bağlayın](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure portalı: Sanal ağınıza bir HyTrust KeyControl kümesi dağıtma](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: KMıP sunucusunu yapılandırma](#hytrust-webui-configure-the-kmip-server)
6. [vCenter Kullanıcı arabirimi: Azure sanal ağınızda KMS kümesi kullanmak için vSAN şifrelemesini yapılandırma](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Önkoşulların karşılandığını doğrula

Dağıtımdan önce aşağıdakileri doğrulayın:

* Seçili KMS satıcısı, aracı ve sürümü vSAN uyumluluk listesinde.
* Seçilen satıcı, bir aracın Azure 'da çalışması için bir sürümünü destekler.
* KMS aracının Azure sürümü, KMıP 1,1 uyumludur.
* Bir Azure Resource Manager ve sanal ağ zaten oluşturulmuş.
* CloudSimple özel bulutu zaten oluşturulmuş.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>CloudSimple Portalı: ExpressRoute eşleme bilgilerini alın

Kuruluma devam etmek için, ExpressRoute için yetkilendirme anahtarı ve eşdüzey devre URI 'sine ve Azure aboneliğinize erişim elde etmeniz gerekir. Bu bilgiler, CloudSimple portalındaki sanal ağ bağlantısı sayfasında bulunur. Yönergeler için bkz. [özel buluta sanal ağ bağlantısı kurma](virtual-network-connection.md). Bilgileri elde etmek için bir sorun yaşıyorsanız, bir [destek isteği](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)açın.

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure portal: Sanal ağınızı özel buluta bağlayın

1. [Azure Portal kullanarak ExpressRoute için sanal ağ geçidi yapılandırma](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)bölümündeki yönergeleri izleyerek sanal ağınız için sanal ağ geçidi oluşturun.
2. [Portalı kullanarak sanal ağı ExpressRoute devresine bağlama](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)bölümündeki yönergeleri izleyerek sanal ağınızı Cloudsimple ExpressRoute bağlantı hattına bağlayın.
3. Sanal ağınızı Azure 'daki CloudSimple ExpressRoute devresine bağlamak için CloudSimple 'dan hoş geldiniz e-postalarınız içindeki CloudSimple ExpressRoute bağlantı hattı bilgilerini kullanın.
4. Yetkilendirme anahtarını ve eş bağlantı devresini girin, bağlantıya bir ad verin ve **Tamam**' a tıklayın.

![Sanal ağ oluştururken CS eş devre URI 'SI sağlayın](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure portal: Sanal ağınızdaki Azure Resource Manager bir HyTrust KeyControl kümesi dağıtma

Sanal ağınızdaki Azure Resource Manager bir HyTrust KeyControl kümesi dağıtmak için aşağıdaki görevleri gerçekleştirin. Ayrıntılar için [Hytrust belgelerine](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0) bakın.

1. HyTrust belgelerindeki yönergeleri izleyerek, belirtilen gelen kurallarla bir Azure ağ güvenlik grubu (NSG-hytrust) oluşturun.
2. Azure 'da bir SSH anahtar çifti oluşturun.
3. Azure Marketi 'ndeki görüntüden ilk KeyControl düğümünü dağıtın.  Oluşturulan anahtar çiftinin ortak anahtarını kullanın ve KeyControl düğümü için ağ güvenlik grubu olarak **NSG-hytrust** ' ı seçin.
4. KeyControl özel IP adresini bir statik IP adresine dönüştürür.
5. Genel IP adresini ve daha önce bahsedilen anahtar çiftinin özel anahtarını kullanarak KeyControl sanal makinesine SSH.
6. SSH kabuğu 'nda sorulduğunda, düğümü ilk keycontrol düğümü olarak ayarlamayı seçin `No` .
7. Bu yordamın 3-5. adımlarını yineleyerek ve var olan bir kümeye ekleme sorulduğunda `Yes` öğesini seçerek ek keycontrol düğümleri ekleyin.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: KMıP sunucusunu yapılandırma

Https://*genel-IP*adresine gidin; burada *genel IP* , keycontrol düğüm sanal makinesinin genel IP adresidir. [Hytrust belgelerindeki](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)adımları izleyin.

1. [Bir KMıP sunucusunu yapılandırma](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [VMware şifrelemesi için bir sertifika paketi oluşturma](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter Kullanıcı arabirimi: Azure sanal ağınızda KMS kümesi kullanmak için vSAN şifrelemesini yapılandırma

[VCenter 'da BIR KMS kümesi oluşturmak](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)Için hytrust talimatlarını izleyin.

![VCenter 'da KMS kümesi ayrıntıları ekleme](media/vsan-config01.png)

VCenter 'da, **küme > Yapılandır** ' a gidin ve vSAN için **genel** seçeneğini belirleyin. Şifrelemeyi etkinleştirin ve daha önce vCenter 'a eklenmiş olan KMS kümesini seçin.

![VSAN şifrelemesini etkinleştirme ve KMS kümesini vCenter 'da yapılandırma](media/vsan-config02.png)

## <a name="references"></a>Referanslar

### <a name="azure"></a>Azure

[Azure portal kullanarak ExpressRoute için sanal ağ geçidi yapılandırma](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Portalı kullanarak ExpressRoute bağlantı hattına bir sanal ağı bağlama](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl ve Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[KMPı sunucusunu yapılandırma](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[VMware şifrelemesi için bir sertifika paketi oluşturma](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[VSphere içinde KMS kümesi oluşturma](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
