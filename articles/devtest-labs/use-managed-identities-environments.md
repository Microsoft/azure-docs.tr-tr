---
title: DevTest Labs 'de ortam oluşturmak için Azure yönetilen kimliklerini kullanma | Microsoft Docs
description: Azure DevTest Labs 'de laboratuarda ortamları dağıtmak için Azure 'da Yönetilen kimlikler kullanmayı öğrenin.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f3e4b4d7030eb26c25b291e03caaa430d1979c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98185793"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Laboratuvar ortamında ortamları dağıtmak için Azure yönetilen kimliklerini kullanma 

Laboratuvar sahibi olarak, bir laboratuvarda ortamları dağıtmak için yönetilen bir kimlik kullanabilirsiniz. Bu özellik, ortamda Anahtar Kasası, paylaşılan görüntü galerileri ve ortamın kaynak grubu dışındaki ağlar gibi Azure kaynaklarına başvuru içeren veya bu kaynaklara sahip olan senaryolarda faydalıdır. Bu ortamın kaynak grubuyla sınırlı olmayan korumalı alan ortamları oluşturulmasına olanak sağlar.

> [!NOTE]
> Şu anda, laboratuvar başına tek bir kullanıcı tarafından atanan kimlik desteklenir. 

## <a name="prerequisites"></a>Önkoşullar

- [Azure Portal kullanarak, Kullanıcı tarafından atanan yönetilen kimlik için rol oluşturma, listeleme, silme veya atama](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 
    
    Yönetilen kimliğinizin laboratuvarınız ile aynı bölgede ve abonelikte oluşturulduğundan emin olun. Yönetilen kimliğin aynı kaynak grubunda olması gerekmez.

## <a name="use-azure-portal"></a>Azure portalı kullanma

Bu bölümde, laboratuvar sahibi olarak, laboratuvara Kullanıcı tarafından yönetilen bir kimlik eklemek için Azure portal kullanın. 

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **DevTest Labs** için arama yapın.
1. Laboratuvarlar listesinden istediğiniz Laboratuvarı seçin.
1. **Yapılandırma ve ilke**  ->  **kimliği (Önizleme)** öğesini seçin. 
1. Kullanıcı tarafından atanan bir kimlik eklemek için **Kullanıcı atandı** sekmesini seçin.
1. **Ekle** tuşuna basın.
1. Oluşturduğunuz ve/veya açılan listeden erişime sahip olan mevcut bir kullanıcıyı seçin.
 
    ![Kullanıcı tarafından yönetilen kimlik Ekle](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. Sayfanın üstünde **Kaydet** ' e basın.

    Bu kimlik kaydedildikten sonra laboratuvar, tüm laboratuar ortamlarını dağıtmakta bu kimliği kullanır. Ayrıca, listeden kimlik ' i seçerek Azure 'daki kimlik kaynağına erişebilirsiniz. 

Laboratuvara eklenen kimliğin, ortamın erişmesi gereken dış kaynaklar için izinleri olduğu sürece laboratuvar sahibinin bir ortamı dağıtmada özel bir şey yapması gerekmez. 

Laboratuvara atanan kullanıcı tarafından yönetilen kimliği değiştirmek için, önce laboratuvara eklenmiş olan kimliği kaldırın ve ardından laboratuvara bir tane ekleyin. Laboratuvara eklenen bir kimliği kaldırmak için... seçeneğini belirleyin **. (üç nokta)** ve **Kaldır**' a tıklayın. 

## <a name="use-api"></a>API kullanma

1. Kimlik oluşturduktan sonra, bu kimliğin kaynak KIMLIĞI ' ni aklınızda edin. Aşağıdaki örnekteki gibi görünmelidir: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Laboratuvara aşağıdaki örneğe benzer yeni bir kaynak eklemek için bir PUT https yöntemi gerçekleştirin `ServiceRunner` . Hizmet Çalıştırıcısı kaynağı, DevTest Labs 'de yönetilen kimlikleri yönetmek ve denetlemek için bir ara sunucu kaynağıdır. Hizmet Çalıştırıcısı adı geçerli bir ad olabilir, ancak yönetilen kimlik kaynağının adını kullanmanızı öneririz. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Aşağıda bir örnek verilmiştir: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Kullanıcı tarafından atanan kimlik laboratuvara eklendikten sonra, Azure DevTest Labs hizmeti Azure Resource Manager ortamları dağıturken bunu kullanacaktır. Örneğin, bir dış paylaşılan görüntü Galerisi görüntüsüne erişmek için Kaynak Yöneticisi şablonunuz olması gerekiyorsa, laboratuvara eklediğiniz kimliğin paylaşılan görüntü Galerisi kaynağı için gereken en düşük izinlere sahip olduğundan emin olun. 
