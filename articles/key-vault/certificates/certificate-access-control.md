---
title: Azure Key Vault sertifikaları erişim denetimi hakkında
description: Azure Key Vault sertifikaları erişim denetimine genel bakış
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 54874f30384d7f4827b13a597a469bfc67bc8fd2
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752489"
---
# <a name="certificate-access-control"></a>Sertifika Access Control

 Sertifikalar için erişim denetimi, Key Vault tarafından yönetilir ve bu sertifikaları içeren Key Vault tarafından sağlanır. Sertifikalar için erişim denetimi ilkesi, aynı Key Vault anahtarlar ve gizli diziler için erişim denetimi ilkelerinden farklıdır. Kullanıcılar, senaryoya uygun bir veya daha fazla kasa oluşturarak sertifikaların uygun segmentlerini ve yönetimini korumanıza olanak sağlayabilir.  

 Aşağıdaki izinler, bir anahtar kasasındaki gizli dizi erişim denetimi girişinde, her bir sorumlu temelinde kullanılabilir ve gizli bir nesne üzerinde izin verilen işlemleri yakından yansıtır:  

- Sertifika yönetimi işlemlerine yönelik izinler
  - **Get**: geçerli sertifika sürümünü veya bir sertifika sürümünü alın
  - **liste**: geçerli sertifikaları veya bir sertifikanın sürümlerini listeleyin  
  - **güncelleştirme**: bir sertifikayı güncelleştirme
  - **oluşturma**: Key Vault sertifikası oluşturma
  - **içeri aktarma**: sertifika malzemesini bir Key Vault sertifikasına aktarma
  - **Sil**: bir sertifikayı, ilkesini ve tüm sürümlerini silme  
  - **kurtar**: silinen bir sertifikayı kurtar
  - **yedekleme**: bir sertifikayı anahtar kasasında yedekleme
  - **geri yükleme**: yedeklenen sertifikayı bir anahtar kasasına geri yükleme
  - **managecontacts**: Key Vault sertifikası kişilerini yönetme  
  - **manageverenler**: Key Vault sertifika yetkililerini/verenler yönetme
  - **getısers**: bir sertifikanın yetkililerini/verenler al
  - **listissuers**: bir sertifikanın yetkililerini/verenler listeleyin  
  - **setısers**: Key Vault sertifikanın yetkililerini/verenler oluşturma veya güncelleştirme  
  - **deleteverenler**: Key Vault sertifikasının yetkililerini/verenler silme  
 
- Ayrıcalıklı işlemler için izinler
  - **Temizle**: silinen bir sertifikayı temizle (kalıcı olarak sil)

Daha fazla bilgi için [Key Vault REST API başvurusu Içindeki sertifika işlemlerine](/rest/api/keyvault)bakın. İzinleri oluşturma hakkında bilgi için bkz. [kasa-güncelleştirme erişim ilkesi](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="troubleshoot"></a>Sorun giderme
Eksik erişim ilkesi nedeniyle hata görebilirsiniz. Örneğin ```Error type : Access denied or user is unauthorized to create certificate``` , bu hatayı çözmek için sertifikalar/oluşturma izni eklemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Key Vault Hakkında](../general/overview.md)
- [Anahtarlar, gizli diziler ve sertifikalar hakkında](../general/about-keys-secrets-certificates.md)
- [Kimlik doğrulama, istekler ve yanıtlar](../general/authentication-requests-and-responses.md)
- [Key Vault Geliştirici Kılavuzu](../general/developers-guide.md)
