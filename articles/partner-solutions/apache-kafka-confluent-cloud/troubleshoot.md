---
title: Confluent bulutu için Apache Kafka sorun giderme-Azure iş ortağı çözümleri
description: Bu makalede, Azure 'da confluent bulutu için sorun giderme ve sık sorulan sorular (SSS) hakkında bilgi verilmektedir.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/18/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: b1e4b06fcbecf11d7d5f58a583fe3bd6643d99ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709418"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Yapılandırmalı bulut çözümleri için Apache Kafka sorunlarını giderme

Bu belge, confluent bulutu için Apache Kafka kullanan çözümlerinizde sorun giderme hakkında bilgiler içerir.

Bir yanıt bulamadıysanız veya bir sorunu çözemezseniz, [Azure Portal bir istek oluşturun](manage.md#get-support) [veya iletişim kurun](https://support.confluent.io).

## <a name="cant-find-offer-in-the-marketplace"></a>Market 'te teklif bulunamıyor

Teklifi Azure Marketi 'nde bulmak için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com) **kaynak oluştur**' u seçin.
1. _Konluent bulutu üzerinde Apache Kafka_ arayın.
1. Uygulama kutucuğunu seçin.

Teklif görüntülenmiyorsa, [confluent desteğiyle](https://support.confluent.io)iletişim kurun. Azure Active Directory kiracı KIMLIĞINIZ, izin verilen kiracılar listesinde olmalıdır. Kiracı KIMLIĞINIZI bulma hakkında bilgi edinmek için bkz. [Azure Active Directory KIRACı kimliğinizi bulma](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md).

## <a name="purchase-errors"></a>Satın alma hataları

* Geçerli bir kredi kartı Azure aboneliğine bağlı olmadığından veya bir ödeme yöntemi abonelikle ilişkili olmadığından satın alma başarısız olur.

  Farklı bir Azure aboneliği kullanın. Ya da, abonelik için kredi kartı veya ödeme yöntemi ekleyebilir veya güncelleştirebilirsiniz. Daha fazla bilgi için bkz. [kredi ve ödeme yöntemini güncelleştirme](../../cost-management-billing/manage/change-credit-card.md).

* EA aboneliği Market satın alımlara izin vermez.

  Farklı bir abonelik kullanın. Ya da EA aboneliğinizin Market satın alma için etkinleştirilip etkinleştirilmediğini denetleyin. Daha fazla bilgi için bkz. [Market satın alımlarını etkinleştirme](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases). Bu seçenekler sorunu çözmezse, bağlantı [desteğiyle](https://support.confluent.io)iletişim kurun.

## <a name="conflict-error"></a>Çakışma hatası

Daha önce, daha önce, daha önce, diğer bir bulut kuruluş kaynağı oluşturmak için yeni bir e-posta adresi kullanmanız gerekir. Önceden kaydedilmiş bir e-posta adresi kullanılırken bir **Çakışma** hatası alırsınız. Bu kez yeni bir e-posta adresi ile yeniden kaydolun.

## <a name="deploymentfailed-error"></a>DeploymentFailed hatası

Bir **Deploymentfailed** hatası alırsanız, Azure aboneliğinizin durumunu kontrol edin. Askıya alınmadığına ve fatura sorununa sahip olmadığından emin olun.

## <a name="resource-isnt-displayed"></a>Kaynak görüntülenmiyor

Confluent bulutu için **genel bakış** veya **silme** Blade 'leri portalda görüntülenmiyorsa, sayfayı yenilemeyi deneyin. Bu hata, portalda aralıklı bir sorun olabilir. Bu işe yaramazsa, [confluent desteğiyle](https://support.confluent.io)iletişim kurun.

Azure **tüm kaynaklar** listesinde confluent bulut kaynağı bulunmazsa, [confluent desteğiyle](https://support.confluent.io)iletişim kurun.

## <a name="resource-creation-takes-long-time"></a>Kaynak oluşturma uzun zaman alır

Dağıtım işleminin tamamlaması üç saatten uzun sürerse desteğe başvurun.

Dağıtım başarısız olursa ve confluent bulut kaynağının durumu varsa `Failed` , kaynağı silin. Silme işleminden sonra, kaynağı oluşturmayı yeniden deneyin.

## <a name="offer-plan-doesnt-load"></a>Teklif planı yüklenmiyor

Bu hata Azure portal aralıklı bir sorun olabilir. Teklifi dağıtmayı yeniden deneyin.

## <a name="unable-to-delete"></a>Açık tanıtıcılardan dolayı

Kullanılamıyor kaynakları silemediğinizi, kaynağı silme izninizin olduğunu doğrulayın. Microsoft. confluent/*/Delete eylemlerini alma izniniz olmalıdır. İzinleri görüntüleme hakkında daha fazla bilgi için bkz. [Azure Portal kullanarak Azure rol atamalarını listeleme](../../role-based-access-control/role-assignments-list-portal.md).

Doğru izinleriniz varsa ancak yine de kaynağı silemseniz [, iletişim kurun](https://support.confluent.io). Bu durum, confluent 'in bekletme ilkesiyle ilgili olabilir. Yapılandırmalı destek, sizin için kuruluş ve e-posta adreslerini silebilir.

## <a name="unable-to-use-single-sign-on"></a>Çoklu oturum açma kullanılamıyor

Bu bulut SaaS portalı için SSO çalışmıyorsa, doğru Azure Active Directory e-postasını kullandığınızı doğrulayın. Ayrıca, bir hizmet olarak yazılım (SaaS) portalı için erişim izni vermek için de göz duymuş olmanız gerekir. Daha fazla bilgi için bkz. [Çoklu oturum açma kılavuzu](manage.md#single-sign-on).

Sorun devam ederse, bağlantı [desteğiyle](https://support.confluent.io)iletişim kurun.

## <a name="next-steps"></a>Sonraki adımlar

Apache Kafka [örneğinizi yönetme](manage.md) hakkında bilgi edinin.
