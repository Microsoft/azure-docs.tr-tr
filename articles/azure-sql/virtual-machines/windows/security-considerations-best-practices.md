---
title: Güvenlik konuları | Microsoft Docs
description: Bu konu, bir Azure sanal makinesinde çalışan SQL Server güvenliğini sağlamaya yönelik genel rehberlik sağlar.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7ff77a407dfa87e408170573249876bbefee0abe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558593"
---
# <a name="security-considerations-for-sql-server-on-azure-virtual-machines"></a>Azure Sanal Makineler üzerinde SQL Server için güvenlikle ilgili dikkat edilmesi gerekenler
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu konu, bir Azure sanal makinesindeki (VM) SQL Server örneklerine güvenli erişim sağlamaya yardımcı olan genel güvenlik yönergelerini içerir.

Azure, bir sanal makinede çalışan SQL Server ile uyumlu bir çözüm oluşturmanıza olanak sağlayan çeşitli sektör düzenlemeleri ve standartları ile uyumludur. Azure ile yönetmelik uyumluluğu hakkında daha fazla bilgi için bkz. [Azure Güven Merkezi](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-virtual-machine"></a>SQL sanal makinesine erişimi denetleme

SQL Server sanal makinenizi oluştururken, makineye kimlerin erişebileceğini ve SQL Server ne kadar dikkatli bir şekilde kontrol etmeyi göz önünde bulundurun. Genel olarak, aşağıdakileri yapmalısınız:

- SQL Server erişimini yalnızca gerekli uygulamalar ve istemcilerle sınırlayın.
- Kullanıcı hesaplarını ve parolaları yönetmek için en iyi uygulamaları izleyin.

Aşağıdaki bölümler, bu noktalarda düşünce hakkında öneriler sağlar.

## <a name="secure-connections"></a>Güvenli bağlantılar

Galeri görüntüsüne sahip SQL Server bir sanal makine oluşturduğunuzda, **SQL Server bağlantı** seçeneği size **Yerel (VM içinde)**, **özel (sanal ağ Içinde)** veya **Genel (Internet)** seçimi sağlar.

![SQL Server bağlantısı](./media/security-considerations-best-practices/sql-vm-connectivity-option.png)

En iyi güvenlik için senaryonuz için en kısıtlayıcı seçeneği belirleyin. Örneğin, aynı VM 'de SQL Server erişen bir uygulama çalıştırıyorsanız, **Yerel** , en güvenli seçenektir. SQL Server erişmesi gereken bir Azure uygulaması çalıştırıyorsanız, **özel** , yalnızca belirtilen [Azure sanal ağı](../../../virtual-network/virtual-networks-overview.md)içinde SQL Server iletişimin güvenliğini sağlar. SQL Server VM için **genel** (internet) erişime ihtiyacınız varsa, saldırı yüzeyi alanını azaltmak için bu konudaki diğer en iyi yöntemleri izlediğinizden emin olun.

Portalda seçilen seçenekler, sanal makinenize ağ trafiğine izin vermek veya bu trafiği reddetmek için VM 'nin [ağ güvenlik grubu](../../../active-directory/identity-protection/concept-identity-protection-security-overview.md) 'nda (NSG) gelen güvenlik kurallarını kullanır. SQL Server bağlantı noktasına giden trafiğe izin vermek için yeni gelen NSG kurallarını değiştirebilir veya oluşturabilirsiniz (varsayılan 1433). Ayrıca, bu bağlantı noktası üzerinden iletişim kurmaya izin verilen belirli IP adreslerini de belirtebilirsiniz.

![Ağ güvenlik grubu kuralları](./media/security-considerations-best-practices/sql-vm-network-security-group-rules.png)

Ağ trafiğini kısıtlamak için NSG kurallarına ek olarak, sanal makinede Windows güvenlik duvarını da kullanabilirsiniz.

Klasik dağıtım modeliyle uç noktalar kullanıyorsanız, bu dosyaları kullanmıyorsanız sanal makinedeki tüm uç noktaları kaldırın. ACL 'Leri uç noktalarla kullanma hakkında yönergeler için bkz. [bir uç noktada ACL 'Yi yönetme](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Bu, Azure Resource Manager kullanan VM 'Ler için gerekli değildir.

Son olarak, Azure sanal makinenizde SQL Server veritabanı altyapısının örneği için şifrelenmiş bağlantıları etkinleştirmeyi düşünün. SQL Server örneğini imzalı bir sertifika ile yapılandırın. Daha fazla bilgi için bkz. veritabanı altyapısı ve [bağlantı dizesi söz dizimine](/dotnet/framework/data/adonet/connection-string-syntax) [şifreli bağlantıları etkinleştirme](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) .

## <a name="encryption"></a>Şifreleme

Yönetilen diskler Server-Side şifreleme ve Azure disk şifrelemesi sunmaktadır. [Sunucu tarafı şifreleme](../../../virtual-machines/disk-encryption.md) , bekleyen şifreleme sağlar ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamak için verilerinizi korur. [Azure disk şifrelemesi](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) , Bitlocker veya DM-Crypt teknolojisini kullanır ve hem işletim sistemi hem de veri disklerini şifrelemek için Azure Key Vault ile tümleşir. 

## <a name="use-a-non-default-port"></a>Varsayılan olmayan bir bağlantı noktası kullan

Varsayılan olarak, SQL Server 1433 gibi iyi bilinen bir bağlantı noktasını dinler. Daha yüksek güvenlik için, 1401 gibi varsayılan olmayan bir bağlantı noktasını dinlemek üzere SQL Server yapılandırın. Azure portal SQL Server Galeri görüntüsü sağlarsanız, bu bağlantı noktasını **SQL Server ayarları** dikey penceresinde belirtebilirsiniz.

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Bunu sağlamaktan sonra yapılandırmak için iki seçeneğiniz vardır:

- Kaynak Yöneticisi VM 'Ler için, [SQL sanal makineler](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)kaynağından **güvenlik** ' i seçebilirsiniz. Bu, bağlantı noktasını değiştirme seçeneği sağlar.

  ![Portalda TCP bağlantı noktası değişikliği](./media/security-considerations-best-practices/sql-vm-change-tcp-port.png)

- Klasik VM 'Ler veya Portal ile sağlanmayan SQL Server VM 'Ler için, sanal makineye uzaktan bağlanarak bağlantı noktasını el ile yapılandırabilirsiniz. Yapılandırma adımları için bkz. [belirli BIR TCP bağlantı noktasını dinlemek Için sunucu yapılandırma](/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Bu el ile tekniği kullanırsanız, bu TCP bağlantı noktasında gelen trafiğe izin vermek için bir Windows güvenlik duvarı kuralı da eklemeniz gerekir.

> [!IMPORTANT]
> SQL Server bağlantı noktası genel internet bağlantılarına açıksa, varsayılan olmayan bir bağlantı noktası belirtilmesi iyi bir fikirdir.

SQL Server varsayılan olmayan bir bağlantı noktasında dinlerken, bağlantı noktasını, bağlandığınızda belirtmeniz gerekir. Örneğin, sunucu IP adresinin 13.55.255.255 olduğu ve SQL Server bağlantı noktası 1401 üzerinde dinlediği bir senaryoyu düşünün. SQL Server bağlanmak için `13.55.255.255,1401` bağlantı dizesinde belirtmeniz gerekir.

## <a name="manage-accounts"></a>Hesapları yönetme

Saldırganların hesap adlarını veya parolaları kolayca tahmin etmesini istemezsiniz. Yardım almak için aşağıdaki ipuçlarını kullanın:

- **Yönetici** olarak adlandırılmayan benzersiz bir yerel yönetici hesabı oluşturun.

- Tüm hesaplarınız için karmaşık güçlü parolalar kullanın. Güçlü parola oluşturma hakkında daha fazla bilgi için bkz. [güçlü parola oluşturma](https://support.microsoft.com/account-billing/how-to-create-a-strong-password-for-your-microsoft-account-f67e4ddd-0dbe-cd75-cebe-0cfda3cf7386) makalesi.

- Varsayılan olarak, Azure SQL Server sanal makine kurulumu sırasında Windows kimlik doğrulamasını seçer. Bu nedenle, **sa** oturumu devre dışıdır ve kurulum tarafından bir parola atanır. **Sa** oturumunun kullanılması veya etkinleştirilmesi önerilir. SQL oturum açmanın olması gerekiyorsa, aşağıdaki stratejilerden birini kullanın:

  - **Sysadmin** üyeliğine sahip benzersiz bir ada sahıp bir SQL hesabı oluşturun. Bunu, sağlama sırasında **SQL kimlik doğrulamasını** etkinleştirerek portaldan yapabilirsiniz.

    > [!TIP] 
    > Sağlama sırasında SQL kimlik doğrulamasını etkinleştirmezseniz, kimlik doğrulama modunu **SQL Server ve Windows kimlik doğrulama modu** olarak el ile değiştirmeniz gerekir. Daha fazla bilgi için bkz. [sunucu kimlik doğrulama modunu değiştirme](/sql/database-engine/configure-windows/change-server-authentication-mode).

  - **Sa** oturumunu kullanmanız gerekiyorsa, sağlama ve yeni bir güçlü parola atama sonrasında oturum açmayı etkinleştirin.

## <a name="additional-best-practices"></a>Diğer en iyi uygulamalar

Bu konu başlığı altında açıklanan uygulamalara ek olarak, hem geleneksel şirket içi güvenlik uygulamalarından en iyi güvenlik uygulamalarını hem de sanal makine güvenlik en iyi yöntemlerini incelemenizi ve uygulamanızı öneririz. 

Şirket içi güvenlik uygulamaları hakkında daha fazla bilgi için bkz. [SQL Server yüklemesinde güvenlik konuları](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) ve [Güvenlik Merkezi](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database). 

Sanal makine güvenliği hakkında daha fazla bilgi için bkz. [sanal makineler güvenliğine genel bakış](../../../security/fundamentals/virtual-machines-overview.md).


## <a name="next-steps"></a>Sonraki adımlar

Performansla ilgili en iyi yöntemleri de ilgileniyorsanız, bkz. [Azure sanal makinelerinde SQL Server Için En Iyi performans uygulamaları](performance-guidelines-best-practices.md).

Azure VM 'lerinde SQL Server çalıştırmaya ilişkin diğer konular için bkz. [Azure sanal makinelerine genel bakış SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md). SQL Server sanal makineleri hakkında sorularınız olursa [Sık Sorulan Sorular](frequently-asked-questions-faq.md) bölümüne bakın.