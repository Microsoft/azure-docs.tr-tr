---
title: Azure 'da Windows VM 'lerinde Key Vault SQL Server Tümleştirme (Kaynak Yöneticisi) | Microsoft Docs
description: Azure Key Vault ile kullanmak üzere SQL Server şifreleme yapılandırmasını nasıl otomatikleştirebileceğinizi öğrenin. Bu konuda, Kaynak Yöneticisi ile oluşturulan SQL sanal makineleriyle Azure Key Vault tümleştirmenin nasıl kullanılacağı açıklanmaktadır.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a6955b7fc4948faaea6db426545f8cc3d1eece35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359906"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Azure VM 'lerinde SQL Server için Azure Key Vault tümleştirmesini yapılandırma (Kaynak Yöneticisi)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[Saydam veri şifrelemesi (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption), [sütun DÜZEYINDE şifreleme (CLE)](/sql/t-sql/functions/cryptographic-functions-transact-sql)ve [yedekleme şifrelemesi](/sql/relational-databases/backup-restore/backup-encryption)gibi birden çok SQL Server şifreleme özelliği vardır. Bu şifreleme biçimleri, şifreleme için kullandığınız şifreleme anahtarlarını yönetmenizi ve depolamanızı gerektirir. Azure Key Vault hizmeti, bu anahtarların güvenli ve yüksek oranda kullanılabilir bir konumda güvenliğini ve yönetimini geliştirmek için tasarlanmıştır. [SQL Server Bağlayıcısı](https://www.microsoft.com/download/details.aspx?id=45344) , SQL Server Azure Key Vault bu anahtarları kullanmasına olanak sağlar.

Şirket içi SQL Server çalıştırıyorsanız, Şirket [içi SQL Server örneğinden Azure Key Vault erişmek](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)için izleyebileceğiniz adımlar vardır. Ancak, Azure VM 'lerinde SQL Server için *Azure Key Vault tümleştirme* özelliğini kullanarak zamandan tasarruf edebilirsiniz.

Bu özellik etkinleştirildiğinde, SQL Server Bağlayıcısı otomatik olarak yüklenir, EKM sağlayıcısını Azure Key Vault erişim için yapılandırır ve kasanıza erişmenize izin veren kimlik bilgisini oluşturur. Daha önce bahsedilen şirket içi belgelerdeki adımlara bakarsanız, bu özelliğin 2 ve 3. adımları otomatikleştirdiği hakkında bilgi alabilirsiniz. Hala el ile yapmanız gereken tek şey, anahtar kasasını ve anahtarları oluşturmaktır. Buradan, SQL Server VM kurulumunun tamamı otomatikleştirilebilir. Bu özellik bu kurulumu tamamladıktan sonra, veritabanlarınızı veya yedeklemelerinizi normal şekilde şifrelemeye başlamak için Transact-SQL (T-SQL) deyimlerini çalıştırabilirsiniz.

[!INCLUDE [Prepare for Key Vault integration](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Genişletilebilir anahtar yönetimi (EKM) sağlayıcısı sürüm 1.0.4.0, [SQL hizmet olarak altyapı (IaaS) uzantısı](./sql-server-iaas-agent-extension-automate-management.md)aracılığıyla SQL Server VM yüklenir. SQL IaaS uzantısını yükseltmek, sağlayıcı sürümünü güncelleştirmeyecektir. Lütfen EKM sağlayıcısı sürümünü gerekirse (örneğin, bir SQL yönetilen örneği 'ne geçiş yaparken) el ile yükseltmeyi düşünün.


## <a name="enabling-and-configuring-key-vault-integration"></a>Key Vault Tümleştirmesini Etkinleştirme ve yapılandırma
Sağlama sırasında Key Vault tümleştirmeyi etkinleştirebilir veya mevcut VM 'Ler için yapılandırabilirsiniz.

### <a name="new-vms"></a>Yeni VM 'Ler
Kaynak Yöneticisi ile yeni bir SQL sanal makinesi sağlıyorsanız Azure portal, Azure Key Vault tümleştirmeyi etkinleştirmek için bir yol sağlar. Azure Key Vault özelliği yalnızca SQL Server Enterprise, Developer ve Evaluation sürümlerinde kullanılabilir.

![SQL Azure Anahtar Kasası Tümleştirme](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

Sağlama hakkında ayrıntılı yönergeler için bkz. [Azure Portal BIR SQL sanal makinesi sağlama](create-sql-vm-portal.md).

### <a name="existing-vms"></a>Mevcut VM 'Ler

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Mevcut SQL sanal makineleri için [SQL sanal makineler](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) kaynağınızı açın ve **Ayarlar** altında **güvenlik** ' i seçin. Azure Key Vault tümleştirmesini etkinleştirmek için **Etkinleştir** ' i seçin. 

![Mevcut VM 'Ler için SQL Key Vault tümleştirmesi](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

İşiniz bittiğinde, değişikliklerinizi kaydetmek için **güvenlik** sayfasının alt kısmındaki **Uygula** düğmesini seçin.

> [!NOTE]
> Burada oluşturduğumuz kimlik bilgisi adı, daha sonra bir SQL oturum açmayla eşlenecek. Bu, SQL oturum açmanın anahtar kasasına erişmesine izin verir. 


> [!NOTE]
> Ayrıca, bir şablon kullanarak Key Vault tümleştirmeyi yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure Key Vault tümleştirme Için Azure hızlı başlangıç şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [Key Vault integration next steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]