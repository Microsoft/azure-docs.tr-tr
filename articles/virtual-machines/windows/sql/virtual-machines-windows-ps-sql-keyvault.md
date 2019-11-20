---
title: Azure 'da Windows VM 'lerinde Key Vault SQL Server Tümleştirme (Kaynak Yöneticisi) | Microsoft Docs
description: Azure Key Vault ile kullanmak üzere SQL Server şifreleme yapılandırmasını nasıl otomatikleştirebileceğinizi öğrenin. Bu konuda Kaynak Yöneticisi ile oluşturulan SQL Server sanal makinelerle Azure Key Vault tümleştirmenin nasıl kullanılacağı açıklanmaktadır.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: cad70169e88e1fafa129c02f30d5288d39e30a9c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102144"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Azure sanal makinelerinde SQL Server için Azure Key Vault tümleştirmesini yapılandırma (Kaynak Yöneticisi)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Klasik](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Genel Bakış
[Saydam veri şifrelemesi (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [sütun DÜZEYINDE şifreleme (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)ve [yedekleme şifrelemesi](https://msdn.microsoft.com/library/dn449489.aspx)gibi birden çok SQL Server şifreleme özelliği vardır. Bu şifreleme biçimleri, şifreleme için kullandığınız şifreleme anahtarlarını yönetmenizi ve depolamanızı gerektirir. Azure Key Vault (AKV) hizmeti, güvenli ve yüksek oranda kullanılabilir bir konumda bu anahtarların güvenliğini ve yönetimini geliştirmek için tasarlanmıştır. [SQL Server Bağlayıcısı](https://www.microsoft.com/download/details.aspx?id=45344) , SQL Server Azure Key Vault bu anahtarları kullanmasına olanak sağlar.

Şirket içi makinelerle SQL Server çalıştırıyorsanız, Şirket [içi SQL Server makinenizden Azure Key Vault erişmek için izleyebileceğiniz adımlar](https://msdn.microsoft.com/library/dn198405.aspx)vardır. Ancak Azure VM 'lerinde SQL Server için *Azure Key Vault tümleştirme* özelliğini kullanarak zamandan tasarruf edebilirsiniz.

Bu özellik etkinleştirildiğinde, SQL Server Bağlayıcısı otomatik olarak yüklenir, EKM sağlayıcısını Azure Key Vault erişim için yapılandırır ve kasanıza erişmenize izin veren kimlik bilgisini oluşturur. Daha önce bahsedilen şirket içi belgelerdeki adımlara bakarsanız, bu özelliğin 2 ve 3. adımları otomatikleştirdiği hakkında bilgi alabilirsiniz. Hala el ile yapmanız gereken tek şey, anahtar kasasını ve anahtarları oluşturmaktır. Buradan, SQL sanal makinenizin tüm kurulumu otomatikleştirilir. Bu özellik bu kurulumu tamamladıktan sonra, veritabanlarını veya yedeklemelerinizi normal şekilde şifrelemeye başlamak için T-SQL deyimlerini çalıştırabilirsiniz.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > EKM sağlayıcısı sürüm 1.0.4.0, [SQL IaaS uzantısı](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)aracılığıyla SQL Server VM yüklendi. SQL IaaS uzantısını yükseltmek, sağlayıcı sürümünü güncelleştirmeyecektir. Lütfen EKM sağlayıcısı sürümünü gerekirse (örneğin, bir SQL yönetilen örneği 'ne geçiş yaparken) el ile yükseltmeyi düşünün.


## <a name="enabling-and-configuring-akv-integration"></a>AKV Tümleştirmesini Etkinleştirme ve yapılandırma
Sağlama sırasında AKV tümleştirmesini etkinleştirebilir veya mevcut VM 'Ler için yapılandırabilirsiniz.

### <a name="new-vms"></a>Yeni VM 'Ler
Kaynak Yöneticisi ile yeni bir SQL Server sanal makine sağlıyorsanız Azure portal Azure Key Vault tümleştirmeyi etkinleştirmek için bir yol sağlar. Azure Key Vault özelliği yalnızca SQL Server Enterprise, Developer ve Evaluation sürümlerinde kullanılabilir.

![SQL Azure Anahtar Kasası Tümleştirme](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Sağlama hakkında ayrıntılı yönergeler için, bkz. [Azure portal SQL Server sanal makinesi sağlama](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Mevcut VM 'Ler

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Mevcut SQL Server sanal makineler için [SQL sanal makineler](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) kaynağınızı açın ve **Ayarlar**altında **güvenlik** ' i seçin. Azure Key Vault tümleştirmesini etkinleştirmek için **Etkinleştir** ' i seçin. 

![Mevcut VM 'Ler için SQL AKV tümleştirmesi](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

İşiniz bittiğinde, değişikliklerinizi kaydetmek için **güvenlik** sayfasının alt kısmındaki **Uygula** düğmesini seçin.

> [!NOTE]
> Burada oluşturduğumuz kimlik bilgisi adı, daha sonra bir SQL oturum açmayla eşlenecek. Bu, SQL oturum açmanın anahtar kasasına erişmesine izin verir. 


> [!NOTE]
> Ayrıca, bir şablon kullanarak AKV tümleştirmesini yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure Key Vault tümleştirme Için Azure hızlı başlangıç şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
