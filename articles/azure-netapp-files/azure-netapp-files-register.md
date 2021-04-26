---
title: Azure NetApp Files için kaydolun | Microsoft Docs
description: Bir eklenebileceğinizi isteği göndererek ve Azure NetApp Files için Azure Kaynak sağlayıcısı 'nı kaydederek Azure NetApp Files kaydolma hakkında bilgi edinin.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/09/2020
ms.author: b-juche
ms.openlocfilehash: a61d6ba1f908a7dae56db066bfae329fc26f1c5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696124"
---
# <a name="register-for-azure-netapp-files"></a>Azure NetApp Files için kaydolma

> [!IMPORTANT] 
> Azure NetApp Files kaynak sağlayıcısını kaydetmeden önce, Azure NetApp Files ekibinden hizmete erişim verildiğini onaylayan bir e-posta almış olmanız gerekir. 

Bu makalede, hizmeti kullanmaya başlayabilmeniz için Azure NetApp Files nasıl kaydolacağınızı öğrenin.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Hizmete erişmek için bir eklenebileceğinizi isteği gönder

1. Aşağıdaki sayfaya gidin ve Azure NetApp Files hizmetine erişmek için bir eklenebileceğinizi isteği gönder:  
    [**Azure NetApp Files eklenebileceğinizi gönderim sayfası**](https://aka.ms/azurenetappfiles) 

    Waitlist kayıt hizmeti, anında hizmet erişimini garanti etmez. 

2. Diğer görevlerle devam etmeden önce Azure NetApp Files ekibinden resmi bir onay e-postası için bekleyin. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>NetApp kaynak sağlayıcısını kaydetme

Hizmeti kullanmak için, Azure NetApp Files için Azure Kaynak sağlayıcısını kaydetmeniz gerekir.

> [!NOTE] 
> Hizmet için erişim izni verilmeden de NetApp kaynak sağlayıcısını başarıyla kaydedebileceksiniz. Ancak, erişim yetkilendirmesi olmadan bir NetApp hesabı ya da başka bir Azure NetApp Files kaynağı oluşturmaya yönelik Azure portal veya API isteği aşağıdaki hata ile reddedilir:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. Azure portal sağ üst köşedeki Azure Cloud Shell simgesine tıklayın:

      ![Azure Cloud Shell simgesi](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Azure hesabınızda birden çok aboneliğiniz varsa Azure NetApp Files için onaylanmış olanı seçin:
    
    ```azurepowershell
    az account set --subscription <subscriptionId>
    ```

3. Azure Cloud Shell konsolunda, aboneliğinizin onaylandığını doğrulamak için aşağıdaki komutu girin:
    
    ```azurepowershell
    az feature list | grep NetApp
    ```

   Komut çıktısı şu şekilde görünür:
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>` abonelik KIMLIĞINIZ.

    Özellik adını görmüyorsanız `Microsoft.NetApp/ANFGA` hizmete erişiminiz yok demektir. Bu adımla durdur. Devam etmeden önce hizmet erişimi istemek üzere [hizmete erişmek için bir eklenebileceğinizi isteği gönderme](#waitlist) konusundaki yönergeleri izleyin. 

4. Azure Cloud Shell konsolunda, Azure Kaynak sağlayıcısı 'nı kaydetmek için aşağıdaki komutu girin: 
    
    ```azurepowershell
    az provider register --namespace Microsoft.NetApp --wait
    ```

   `--wait`Parametresi, konsolun kaydın tamamlanmasını beklemesini ister. Kayıt işleminin tamamlanması biraz zaman alabilir.

5. Azure Cloud Shell konsolunda, Azure Kaynak sağlayıcısı 'nın kaydedildiğini doğrulamak için aşağıdaki komutu girin: 
    
    ```azurepowershell
    az provider show --namespace Microsoft.NetApp
    ```

   Komut çıktısı şu şekilde görünür:
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>` abonelik KIMLIĞINIZ.  `state`Parametre değeri gösterir `Registered` .

6. Azure portal, **abonelikler** dikey penceresine tıklayın.
7. Abonelikler dikey penceresinde abonelik KIMLIĞINIZ ' ne tıklayın. 
8. Abonelik ayarları ' nda, Microsoft. NetApp sağlayıcısı 'nın kayıtlı durumu olduğunu doğrulamak için **kaynak sağlayıcıları** ' na tıklayın: 

      ![Kayıtlı Microsoft. NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Sonraki adımlar

[NetApp hesabı oluşturma](azure-netapp-files-create-netapp-account.md)
