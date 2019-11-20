---
title: Teklif yayımlamayı otomatikleştirin | Azure Marketi
description: Sanal makine yayımlama iş akışını programlama yoluyla nasıl otomatikleştirebileceğinizi açıklar.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6464c8354c9d56092380e4b76c1ea962f5102c72
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824341"
---
<a name="automate-offer-publishing"></a>Teklif yayımlamayı otomatikleştirin
=========================

Ayrıca, [API başvurusu](./cloud-partner-portal-api-overview.md) bölümündeki API 'LERI kullanarak VM yayımlama iş akışını programlı bir şekilde otomatikleştirebilirsiniz. Otomasyon planlama sırasında göz önünde bulundurmanız gereken iki farklı senaryo vardır: ilk yayımlama ve sonraki teklif yayımlamayı sunma.


<a name="offer-initial-publishing"></a>Teklif ilk yayımlama
-------------------------

Bir teklifi ilk kez yayımladığınızda Market 'e yüklemeden önce birkaç ek adım gerekir.  Örneğin, meta verileri hazırlamanız ve bir teklif taslağı oluşturmanız gerekir. İlk yayımlama iş akışı aşağıdaki diyagramda gösterilmiştir.

![İlk teklif yayınının etkileşimleri](media/cloud-partner-portal-automate-offer-publishing/first-time-offer-publishing.png)

Aşağıdaki örnek kod bu adımları gösterir.

``` csharp
  CreateOfferAndPublish()
  {
      offer = CreateOfferObject()
      // Set all offer attributes
      offer.offerTypeId = "microsoft-azure-virtualmachines",
      // Create offer on server
     result = CloudPartnerPortal.Client.PutOffer(offer);
      if(result.status != success)
      {
        // Do failure actions here
      }
      // Publish created offer
      Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
      if(op.HTTPStatus != OK)
      {
        /*Queuing the publish failed, check error message and dofailure actions*/
        return failure;
      }
      // Return success as operation is successfully queued
      return success;
  }

  ValidateAndGoLive()    
  {
      // Confirm the version in preview slot is the version that needs to go live
      offer = CloudPartnerPortal.Client.GetOffer(offerName, "Preview");
      if(!offer[skuName].containsVersion(VMDisk.Version))
      {
          UpdateOfferAndPublish()
      }
      // Go Live on offer and check result
      Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
      if(op.HTTPStatus != OK)
      {
          // Queuing the publish failed, check error message and do failure actions
          return failure;
      }  
     
      // Return success as operation is successfully queued
      return success;
  }
```


<a name="subsequent-offer-publishing"></a>Sonraki teklif yayımlaması
---------------------------

Sanal makine (VM) teklifi bir sürekli tümleştirme ardışık düzenine tümleştirildiğinde, her yeni sanal sabit disk (VHD) oluşturulduğunda yayımlama iş akışını otomatik hale getirebilirsiniz.  Bu iş akışı aşağıdaki diyagram ve örnek kod tarafından gösterilmiştir.

![Sonraki teklif yayınlarının etkileşimleri](media/cloud-partner-portal-automate-offer-publishing/update-offer-and-publish.png)

``` csharp
    UpdateOfferAndPublish()
    {
        // The routine wakes up after a build finishes. 
        // It determines if the current offer is undergoing publishing and take appropriate action
        // Wake up and get draft offer using offer name
        offer = CloudPartnerPortal.Client.GetOffer(offerName);
        if(!offer[skuName].containsVersion(VMDisk.Version))
        {
                // Update the list of VMs with new virtual machine. 
                // This assumes that less than 8 disks are present in the SKU
                // If 8 disks are already present, the client must remove one of the previous disks before adding new disk
                offer[skuName].addDisk(VMDisk)
                // Update the offer on server with new VMDisk
                CloudPartnerPortal.Client.PutOffer(offer);
        }
        // Publish offer and check result. Publish is idempotent, so if a publish was already in progress with latest version, this would return success.
        Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
        if(op.HTTPStatus != OK)
        {
              // Queuing the publish failed, check error message and do failure actions
              return failure;
        }
        
        // Return success as operation is successfully queued
        return success;
    }

    CheckLastOperationStatus()
    {
        Operation op = CloudPartnerPortal.Client.GetLastOpertation(offer.offerName)
        if(op.status == completed)
        {
            return success;
        }
        if(op.status == failure)
           // Do failure actions here
        
        else 
           // Last operation is still running
           // Go to sleep.           
    }

    ValidateAndGoLive()
    {
        // Confirm the version in preview slot is the version that needs to go live
        offer = CloudPartnerPortal.Client.GetOffer(offerName, "Preview");
        if(!offer[skuName].containsVersion(VMDisk.Version))
        {
            UpdateOfferAndPublish()
        }
        // Go Live on offer and check result
        Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
        if(op.HTTPStatus != OK)
        {
              // Queuing the publish failed, check error message and do failure actions
              return failure;
        }
        // Return success as operation is successfully queued
        return success;
    }
```
