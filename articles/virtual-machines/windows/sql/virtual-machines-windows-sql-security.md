---
title: Azure 'da SQL Server yönelik güvenlik konuları | Microsoft Docs
description: Bu konu, bir Azure sanal makinesinde çalışan SQL Server güvenliğini sağlamaya yönelik genel rehberlik sağlar.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6b2f9853c2699b69a0c9be13e6925a4b30f358f7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102021"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Azure Sanal Makineler'de SQL Server için Güvenlikle İlgili Dikkat Edilmesi Gerekenler

Bu konu, bir Azure sanal makinesindeki (VM) SQL Server örneklerine güvenli erişim sağlamaya yardımcı olan genel güvenlik yönergelerini içerir.

Azure, bir sanal makinede çalışan SQL Server ile uyumlu bir çözüm oluşturmanıza olanak sağlayan çeşitli sektör düzenlemeleri ve standartları ile uyumludur. Azure ile yönetmelik uyumluluğu hakkında daha fazla bilgi için bkz. [Azure Güven Merkezi](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>SQL VM 'ye erişimi denetleme

SQL Server sanal makinenizi oluştururken, makineye kimlerin erişebileceğini ve SQL Server ne kadar dikkatli bir şekilde kontrol etmeyi göz önünde bulundurun. Genel olarak, aşağıdakileri yapmalısınız:

- SQL Server erişimini yalnızca gerekli uygulamalar ve istemcilerle sınırlayın.
- Kullanıcı hesaplarını ve parolaları yönetmek için en iyi uygulamaları izleyin.

Aşağıdaki bölümler, bu noktalarda düşünce hakkında öneriler sağlar.

## <a name="secure-connections"></a>Güvenli bağlantılar

Galeri görüntüsüne sahip SQL Server bir sanal makine oluşturduğunuzda, **SQL Server bağlantı** seçeneği size **Yerel (VM içinde)** , **özel (sanal ağ Içinde)** veya **Genel (Internet)** seçimi sağlar.

![SQL Server bağlantısı](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

En iyi güvenlik için senaryonuz için en kısıtlayıcı seçeneği belirleyin. Örneğin, aynı VM 'de SQL Server erişen bir uygulama çalıştırıyorsanız, **Yerel** , en güvenli seçenektir. SQL Server erişmesi gereken bir Azure uygulaması çalıştırıyorsanız, **özel** , yalnızca belirtilen [Azure sanal ağı](../../../virtual-network/virtual-networks-overview.md)içinde SQL Server iletişimin güvenliğini sağlar. SQL Server VM için **genel** (internet) erişime ihtiyacınız varsa, saldırı yüzeyi alanını azaltmak için bu konudaki diğer en iyi yöntemleri izlediğinizden emin olun.

Portalda seçilen seçenekler, sanal makinenize ağ trafiğine izin vermek veya bu trafiği reddetmek için VM 'nin [ağ güvenlik grubu](../../../virtual-network/security-overview.md) 'nda (NSG) gelen güvenlik kurallarını kullanır. SQL Server bağlantı noktasına giden trafiğe izin vermek için yeni gelen NSG kurallarını değiştirebilir veya oluşturabilirsiniz (varsayılan 1433). Ayrıca, bu bağlantı noktası üzerinden iletişim kurmaya izin verilen belirli IP adreslerini de belirtebilirsiniz.

![Ağ güvenlik grubu kuralları](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Ağ trafiğini kısıtlamak için NSG kurallarına ek olarak, sanal makinede Windows güvenlik duvarını da kullanabilirsiniz.

Klasik dağıtım modeliyle uç noktalar kullanıyorsanız, bu dosyaları kullanmıyorsanız sanal makinedeki tüm uç noktaları kaldırın. ACL 'Leri uç noktalarla kullanma hakkında yönergeler için bkz. [bir uç noktada ACL 'Yi yönetme](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Bu, Kaynak Yöneticisi kullanan VM 'Ler için gerekli değildir.

Son olarak, Azure sanal makinenizde SQL Server veritabanı altyapısının örneği için şifrelenmiş bağlantıları etkinleştirmeyi düşünün. SQL Server örneğini imzalı bir sertifika ile yapılandırın. Daha fazla bilgi için bkz. veritabanı altyapısı ve [bağlantı dizesi söz dizimine](https://msdn.microsoft.com/library/ms254500.aspx) [şifreli bağlantıları etkinleştirme](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) .

## <a name="use-a-non-default-port"></a>Varsayılan olmayan bir bağlantı noktası kullan

Varsayılan olarak, SQL Server iyi bilinen bir bağlantı noktasını dinler, 1433. Daha yüksek güvenlik için, 1401 gibi varsayılan olmayan bir bağlantı noktasını dinlemek üzere SQL Server yapılandırın. Azure portal SQL Server Galeri görüntüsü sağlarsanız, bu bağlantı noktasını **SQL Server ayarları** dikey penceresinde belirtebilirsiniz.

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Bunu sağlamaktan sonra yapılandırmak için iki seçeneğiniz vardır:

- Kaynak Yöneticisi VM 'Ler için, [SQL sanal makineler](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)kaynağından **güvenlik** ' i seçebilirsiniz. Bu, bağlantı noktasını değiştirme seçeneği sağlar.

  ![Portalda TCP bağlantı noktası değişikliği](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Klasik VM 'Ler veya Portal ile sağlanmayan SQL Server VM 'Ler için, sanal makineye uzaktan bağlanarak bağlantı noktasını el ile yapılandırabilirsiniz. Yapılandırma adımları için bkz. [belirli BIR TCP bağlantı noktasını dinlemek Için sunucu yapılandırma](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Bu el ile tekniği kullanırsanız, bu TCP bağlantı noktasında gelen trafiğe izin vermek için bir Windows güvenlik duvarı kuralı da eklemeniz gerekir.

> [!IMPORTANT]
> SQL Server bağlantı noktası genel internet bağlantılarına açıksa, varsayılan olmayan bir bağlantı noktası belirtilmesi iyi bir fikirdir.

SQL Server varsayılan olmayan bir bağlantı noktasında dinlerken, bağlantı noktasını, bağlandığınızda belirtmeniz gerekir. Örneğin, sunucu IP adresinin 13.55.255.255 olduğu ve SQL Server bağlantı noktası 1401 üzerinde dinlediği bir senaryoyu düşünün. SQL Server bağlanmak için bağlantı dizesinde belirtmeniz `13.55.255.255,1401` gerekir.

## <a name="manage-accounts"></a>Hesapları yönetme

Saldırganların hesap adlarını veya parolaları kolayca tahmin etmesini istemezsiniz. Yardım almak için aşağıdaki ipuçlarını kullanın:

- **Yönetici**olarak adlandırılmayan benzersiz bir yerel yönetici hesabı oluşturun.

- Tüm hesaplarınız için karmaşık güçlü parolalar kullanın. Güçlü parola oluşturma hakkında daha fazla bilgi için bkz. [güçlü parola oluşturma](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) makalesi.

- Varsayılan olarak, Azure SQL Server sanal makine kurulumu sırasında Windows kimlik doğrulamasını seçer. Bu nedenle, **sa** oturumu devre dışıdır ve kurulum tarafından bir parola atanır. **Sa** oturumunun kullanılması veya etkinleştirilmesi önerilir. SQL oturum açmanın olması gerekiyorsa, aşağıdaki stratejilerden birini kullanın:

  - **Sysadmin** üyeliğine sahip benzersiz bir ada sahıp bir SQL hesabı oluşturun. Bunu, sağlama sırasında **SQL kimlik doğrulamasını** etkinleştirerek portaldan yapabilirsiniz.

    > [!TIP] 
    > Sağlama sırasında SQL kimlik doğrulamasını etkinleştirmezseniz, kimlik doğrulama modunu **SQL Server ve Windows kimlik doğrulama modu**olarak el ile değiştirmeniz gerekir. Daha fazla bilgi için bkz. [sunucu kimlik doğrulama modunu değiştirme](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - **Sa** oturumunu kullanmanız gerekiyorsa, sağlama ve yeni bir güçlü parola atama sonrasında oturum açmayı etkinleştirin.

## <a name="follow-on-premises-best-practices"></a>Şirket içi en iyi uygulamaları izleyin

Bu konuda açıklanan uygulamalara ek olarak, uygun yerlerde geleneksel şirket içi güvenlik uygulamalarını incelemenizi ve uygulamanızı öneririz. Daha fazla bilgi için bkz. [SQL Server yükleme Için güvenlik konuları](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>Sonraki Adımlar

Performansla ilgili en iyi yöntemleri de ilgileniyorsanız, bkz. [Azure sanal makinelerinde SQL Server Için En Iyi performans uygulamaları](virtual-machines-windows-sql-performance.md).

Azure VM 'lerinde SQL Server çalıştırmaya ilişkin diğer konular için bkz. [Azure sanal makinelerine genel bakış SQL Server](virtual-machines-windows-sql-server-iaas-overview.md). SQL Server sanal makineleri hakkında sorularınız olursa [Sık Sorulan Sorular](virtual-machines-windows-sql-server-iaas-faq.md) bölümüne bakın.

