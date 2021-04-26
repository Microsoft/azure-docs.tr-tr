---
title: Azure Red Hat OpenShift için bir Azure AD kiracısı oluşturma
description: Microsoft Azure Red Hat OpenShift kümenizi barındırmak için Azure Active Directory (Azure AD) kiracısı oluşturma işlemini burada bulabilirsiniz.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: b98f02adeb850f16127658c7d02d44754512e216
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100635018"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift için bir Azure AD kiracısı oluşturma

> [!IMPORTANT]
> Azure Red Hat Openshıft 3,11, 30 Haziran 2022 tarihinde kullanımdan kaldırılacaktır. Yeni Azure Red Hat OpenShift 3,11 kümelerinin oluşturulmasına yönelik destek, 30 Kasım 2020 ile devam eder. Aşağıdaki kullanımdan kaldırma, kalan Azure Red Hat Openshıft 3,11 kümeleri güvenlik açıklarını engellemek için kapatılacak.
> 
> [Azure Red Hat OpenShift 4 kümesi oluşturmak](tutorial-create-cluster.md)için bu kılavuzu izleyin.
> Belirli sorularınız varsa [lütfen bizimle iletişime geçin](mailto:arofeedback@microsoft.com).

Microsoft Azure Red Hat OpenShift, kümenizin oluşturulacağı bir [Azure Active Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) kiracısı gerektirir. *Kiracı* , bir kuruluşun veya uygulama geliştiricisinin azure, Microsoft Intune veya Microsoft 365 kaydolduktan sonra Microsoft ile bir ilişki oluşturduklarında aldığı adanmış BIR Azure ad örneğidir. Her Azure AD kiracısı farklı ve diğer Azure AD kiracılarından ayrıdır ve kendi iş ve okul kimliklerine ve uygulama kayıtlarına sahiptir.

Zaten bir Azure AD kiracınız yoksa, bir tane oluşturmak için bu yönergeleri izleyin.

## <a name="create-a-new-azure-ad-tenant"></a>Yeni Azure AD kiracısı oluşturma

Kiracı oluşturmak için:

1. Azure Red Hat OpenShift kümeniz ile ilişkilendirmek istediğiniz hesabı kullanarak [Azure Portal](https://portal.azure.com/) oturum açın.
2. Yeni bir kiracı (yeni *Azure Active Directory* olarak da bilinir) oluşturmak için [Azure Active Directory dikey penceresini](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) açın.
3. **Kuruluş adı** belirtin.
4. **İlk etki alanı adı** belirtin. Buna *onmicrosoft.com* eklenmiş olması gerekir. *Kuruluş adı* değerini buradan yeniden kullanabilirsiniz.
5. Kiracının oluşturulacağı bir ülke veya bölge seçin.
6. **Oluştur**’a tıklayın.
7. Azure AD kiracınız oluşturulduktan sonra **Yeni dizin bağlantısını yönetmek için buraya tıklayın ' ı** seçin. Yeni kiracı adınız Azure portal sağ üst kısmında görüntülenmelidir:  

    ![Sağ üst köşedeki kiracı adını gösteren portalın ekran görüntüsü][tenantcallout]  

8. Azure Red Hat Openshıft kümenizin nerede oluşturulacağını daha sonra belirleyebilmeniz için *KIRACı kimliğini* unutmayın. Portalda, artık yeni kiracınız için Azure Active Directory genel bakış dikey penceresini görmeniz gerekir. **Özellikler** ' i seçin ve **Dizin kimliğiniz** için değeri kopyalayın. `TENANT` [Azure Red Hat OpenShift kümesi oluşturma](tutorial-create-cluster.md) öğreticisinde bu değere başvuracağız.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Kaynaklar

[Azure AD kiracılar](../active-directory/develop/quickstart-create-new-tenant.md)hakkında daha fazla bilgi için [Azure Active Directory belgelerine](../active-directory/index.yml) göz atın.

## <a name="next-steps"></a>Sonraki adımlar

Hizmet sorumlusu oluşturmayı, bir istemci gizli anahtarı ve kimlik doğrulama geri çağırma URL 'sini oluşturmayı ve Azure Red Hat OpenShift kümenizdeki uygulamaları test etmek için yeni bir Active Directory Kullanıcı oluşturmayı öğrenin.

[Azure AD uygulama nesnesi veya kullanıcısı oluşturma](howto-aad-app-configuration.md)