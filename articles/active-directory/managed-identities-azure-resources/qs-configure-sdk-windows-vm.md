---
title: Azure kaynakları için yönetilen kimliklere sahip bir VM 'yi yapılandırmak için bir Azure SDK kullanma
description: Azure SDK kullanarak bir Azure VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırmak ve kullanmak için adım adım yönergeler.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b4f5a26f240656911fb9627d273de8b69edff6a
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809177"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Azure SDK kullanarak Azure kaynakları için yönetilen kimliklerle VM yapılandırma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory (AD) içinde otomatik olarak yönetilen bir kimlikle Azure hizmetleri sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz. 

Bu makalede, Azure SDK kullanarak bir Azure VM için Azure kaynakları için yönetilen kimliklerin nasıl etkinleştirileceğini ve kaldırılacağını öğrenirsiniz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Azure kaynakları için yönetilen kimliklerle Azure SDK 'Ları desteği 

Azure, bir dizi [Azure SDK 'sı](https://azure.microsoft.com/downloads)aracılığıyla birden çok programlama platformunu destekler. Bunların bazıları Azure kaynakları için yönetilen kimlikleri destekleyecek şekilde güncelleştirilmiştir ve kullanımı göstermek için karşılık gelen örnekler sağlar. Ek destek eklendikçe bu liste güncelleştirilir:

| SDK | Örnek |
| --- | ------ | 
| .NET   | [Azure kaynakları için yönetilen kimliklerle etkin bir VM 'den kaynak yönetme etkin](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Azure kaynakları için Yönetilen kimlikler ile etkinleştirilen bir VM 'den depolamayı yönetme](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Sistem tarafından atanan yönetilen kimlik etkin bir VM oluşturma](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Sistem tarafından atanan yönetilen kimlik etkin bir VM oluşturma](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Sistem tarafından atanan bir kimlik etkinken Azure VM oluşturma](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Sonraki adımlar

- Azure portal, PowerShell, CLı ve kaynak şablonlarını nasıl kullanabileceğinizi öğrenmek için **bir Azure VM Için kimlik yapılandırma**bölümündeki ilgili makalelere bakın.
