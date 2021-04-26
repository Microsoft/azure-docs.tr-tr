---
title: Azure Spring Cloud 'da izinleri kullanma
description: Bu makalede, Azure Spring Cloud 'da izinler için nasıl özel roller oluşturacağınız gösterilmektedir.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c0b3bd81e0f73a7879382e28516378bd722bc17
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878781"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Azure Spring Cloud 'daki izinleri kullanma
Bu makalede, Azure yay bulut kaynakları için izinleri temsil eden özel roller oluşturma konusu gösterilmektedir. Özel roller, çeşitli stok izinleriyle [Azure yerleşik rollerini](../role-based-access-control/built-in-roles.md) genişletir.

Aşağıdaki özel rolleri uygulayacağız:

* **Geliştirici rolü**: 
    * Dağıtma
    * Test
    * Uygulamaları yeniden Başlat
    * Git deposundaki uygulama yapılandırmalarına uygulayabilir ve değişiklikler yapabilir
    * Günlük akışını alabilir
* **Ops-site güvenilirlik mühendisliği**: 
    * Uygulamaları yeniden Başlat
    * Günlük akışlarını al
    * Uygulamalarda veya yapılandırmalarda değişiklik yapılamaz
* **Azure Pipelines/Jenkins/GitHub eylemleri rolü**:
    * Oluşturma, okuma, güncelleştirme, silme işlemleri gerçekleştirebilir
    * , Terkform veya ARM şablonlarını kullanarak Azure Spring Cloud ve Service Instance içindeki uygulamalardaki her şeyi oluşturabilir ve yapılandırabilir: Azure Pipelines, Jenkins veya GitHub eylemleri

## <a name="define-developer-role"></a>Geliştirici rolünü tanımla

Geliştirici rolü, uygulamaları yeniden başlatma ve günlük akışlarını görme izinleri içerir, ancak uygulamalar, yapılandırma üzerinde değişiklik yapamaz.

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>Aboneliğe ve kaynak grubuna erişim denetimine (ıAM) git

Rol tanımlamaya başlamak için bu adımları izleyin.

1. Azure portal, özel rolün atanabilir olmasını istediğiniz aboneliği ve kaynak grubunu açın.
2. **Access Control (IAM)** öğesini açın.
3. **+ Ekle**'ye tıklayın.
4. **Özel rol Ekle**' ye tıklayın.
5. **İleri**’ye tıklayın.

   ![Özel rol oluştur](media/spring-cloud-permissions/create-custom-role.png)

6. **Izin Ekle**' ye tıklayın.

   ![İzin ekleme başlangıcı](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>Azure yay bulut izinlerini arayın:
7. Arama kutusunda, *Microsoft.app* için arama yapın.
*Microsoft Azure yay bulutu*' nı seçin.

   ![Azure yay bulutu 'nı seçin](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. Geliştirici rolü için izinleri seçin:

**Microsoft. AppPlatform/Spring**' den şunları seçin:
* Yazma: Azure yay bulut hizmeti örneğini oluşturun veya güncelleştirin
* Okuyun: Azure yay bulut hizmeti örneğini al
* Diğer: Azure yay bulut hizmeti örneği test anahtarlarını listeleyin

**Microsoft. AppPlatform/Spring/Apps**' den şunları seçin:
* Okuma: Microsoft Azure Spring Cloud uygulamasını okuyun
* Diğer: Microsoft Azure yay bulut uygulaması kaynak yükleme URL 'sini al

**Microsoft. AppPlatform/yay/uygulamalar/bağlamalar**' nden şunları seçin:
* Okuma: Microsoft Azure yay bulut uygulaması bağlamasını oku

**Microsoft. AppPlatform/yay/uygulamalar/dağıtımlar**' da şunları seçin:
* Yazma: yazma Microsoft Azure yay bulutu uygulaması dağıtımı
* Okuma: Microsoft Azure yay bulut uygulaması dağıtımını okuyun
* Diğer: Microsoft Azure yay bulut uygulaması dağıtımını Başlat
* Diğer: Microsoft Azure yay bulut uygulaması dağıtımını durdur
* Diğer: Microsoft Azure yay bulut uygulaması dağıtımını yeniden başlatın
* Diğer: Microsoft Azure yay bulut uygulaması dağıtım günlük dosyası URL 'SI al

**Microsoft. AppPlatform/yay/uygulamalar/etki alanları**' ndan şunları seçin:
* Okuma: Microsoft Azure yay bulut uygulaması özel etki alanını okuyun

**Microsoft. AppPlatform/yay/sertifikalar**' dan şunları seçin:
* Okuma: Microsoft Azure yay bulut sertifikasını okuyun

**Microsoft. AppPlatform/Locations/operationResults/Spring**' den şunları seçin:
* Okuma: okuma işlemi sonucu

**Microsoft. AppPlatform/Locations/operationStatus/operationId**'den şunları seçin:
* Okuma: okuma işlemi durumu

    [![Geliştirme izinleri ](media/spring-cloud-permissions/developer-permissions-box.png) oluşturma](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. **Ekle**'ye tıklayın.

10. İzinleri gözden geçirin.

11. **Gözden geçir ve oluştur**’a tıklayın.

## <a name="define-devops-engineer-role"></a>DevOps mühendis rolünü tanımlayın
Bu yordam, Azure yay bulut uygulamalarını dağıtma, test etme ve yeniden başlatma izinlerine sahip bir rol tanımlar.

1. Abonelik, kaynak grubu ve erişim erişimi denetimi (ıAM) üzerinde gezinmek için yordamı tekrarlayın.
2. DevOps mühendis rolü için izinleri seçin:

**Microsoft. AppPlatform/Spring**' den şunları seçin:
* Yazma: Azure yay bulut hizmeti örneğini oluşturun veya güncelleştirin
* Sil: Azure yay bulut hizmeti örneğini Sil
* Okuyun: Azure yay bulut hizmeti örneğini al
* Diğer: Azure yay bulut hizmeti örneği test uç noktasını etkinleştirme
* Diğer: Azure yay bulut hizmeti örneği test uç noktasını devre dışı bırak
* Diğer: Azure yay bulut hizmeti örneği test anahtarlarını listeleyin
* Diğer: Azure yay bulut hizmeti örneği test anahtarını yeniden üret

**Microsoft. AppPlatform/Spring/Apps**' den şunları seçin:
* Write: yazma Microsoft Azure yay bulutu uygulaması
* Sil: Microsoft Azure Spring Cloud uygulamasını Sil
* Okuma: Microsoft Azure Spring Cloud uygulamasını okuyun
* Diğer: Microsoft Azure yay bulut uygulaması kaynak yükleme URL 'sini al
* Diğer: Microsoft Azure yay bulut uygulaması özel etki alanını doğrula

**Microsoft. AppPlatform/yay/uygulamalar/bağlamalar**' nden şunları seçin:
* Yazma: yazma Microsoft Azure yay bulut uygulaması bağlama
* Sil: Microsoft Azure Spring Cloud Application bağlamasını Sil
* Okuma: Microsoft Azure yay bulut uygulaması bağlamasını oku

**Microsoft. AppPlatform/yay/uygulamalar/dağıtımlar**' da şunları seçin:
* Yazma: yazma Microsoft Azure yay bulutu uygulaması dağıtımı
* Sil: Azure yay bulut uygulaması dağıtımını Sil
* Okuma: Microsoft Azure yay bulut uygulaması dağıtımını okuyun
* Diğer: Microsoft Azure yay bulut uygulaması dağıtımını Başlat
* Diğer: Microsoft Azure yay bulut uygulaması dağıtımını durdur
* Diğer: Microsoft Azure yay bulut uygulaması dağıtımını yeniden başlatın
* Diğer: Microsoft Azure yay bulut uygulaması dağıtım günlük dosyası URL 'SI al

**Microsoft. AppPlatform/yay/uygulamalar/dağıtımlar/SKU**'lardan şunları seçin:
* Okuma: uygulama dağıtımı kullanılabilir SKU 'larını listeleyin

**Microsoft. AppPlatform/konumlardan** şunları seçin:
* Diğer: ad kullanılabilirliğini denetle

Microsoft. AppPlatform/Locations/operationResults/yay Select: Read: okuma işlemi sonucu

**Microsoft. AppPlatform/Locations/operationStatus/operationId**'den şunları seçin:
* Okuma: okuma işlemi durumu

**Microsoft. AppPlatform/SKU**'lardan şunları seçin:
* Okuma: kullanılabilir SKU 'ları listeleme

   [![Geliştirme/işlem izinleri ](media/spring-cloud-permissions/dev-ops-permissions.png)](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. **Ekle**'ye tıklayın.

4. İzinleri gözden geçirin.

5. **Gözden geçir ve oluştur**’a tıklayın.

## <a name="define-ops---site-reliability-engineering-role"></a>Ops-site güvenilirlik mühendisliği rolünü tanımlayın
Bu yordam, Azure yay bulut uygulamalarını dağıtma, test etme ve yeniden başlatma izinlerine sahip bir rol tanımlar.

1. Abonelik, kaynak grubu ve erişim erişimi denetimi (ıAM) üzerinde gezinmek için yordamı tekrarlayın.

2. Ops-site güvenilirlik mühendisliği rolü için izinleri seçin:

**Microsoft. AppPlatform/Spring**' den şunları seçin:
* Okuyun: Azure yay bulut hizmeti örneğini al
* Diğer: Azure yay bulut hizmeti örneği test anahtarlarını listeleyin

**Microsoft. AppPlatform/Spring/Apps**' den şunları seçin:
* Okuma: Microsoft Azure Spring Cloud uygulamasını okuyun

**Microsoft. AppPlatform/uygulamalar/dağıtımlar**' da şunları seçin:
* Okuma: Microsoft Azure yay bulut uygulaması dağıtımını okuyun
* Diğer: Microsoft Azure yay bulut uygulaması dağıtımını Başlat
* Diğer: Microsoft Azure yay bulut uygulaması dağıtımını durdur
* Diğer: Microsoft Azure yay bulut uygulaması dağıtımını yeniden başlatın

**Microsoft. AppPlatform/Locations/operationResults/Spring**' den şunları seçin:
* Okuma: okuma işlemi sonucu

**Microsoft. AppPlatform/Locations/operationStatus/operationId**'den şunları seçin:
* Okuma: okuma işlemi durumu

   [![Ops/SRE izinleri ](media/spring-cloud-permissions/ops-sre-permissions.png)](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. **Ekle**'ye tıklayın.

4. İzinleri gözden geçirin.

5. **Gözden geçir ve oluştur**’a tıklayın.

## <a name="define-azure-pipelinesprovisioning-role"></a>Azure Pipelines/sağlama rolünü tanımlayın
Bu Jenkins/GitHub eylemleri rolü, Azure Spring Cloud ve bir hizmet örneği ile uygulamalardaki her şeyi oluşturabilir ve yapılandırabilir. Bu rol kodu serbest bırakmak veya dağıtmak içindir.

1. Abonelik, kaynak grubu ve erişim erişimi denetimi (ıAM) üzerinde gezinmek için yordamı tekrarlayın.

2. **İzinler** seçeneklerini açın.

3. Azure Pipelines/sağlama rolü için izinleri seçin:
  
**Microsoft. AppPlatform/Spring**' den şunları seçin:
* Yazma: Azure yay bulut hizmeti örneğini oluşturun veya güncelleştirin
* Sil: Azure yay bulut hizmeti örneğini Sil
* Okuyun: Azure yay bulut hizmeti örneğini al
* Diğer: Azure yay bulut hizmeti örneği test uç noktasını etkinleştirme
* Diğer: Azure yay bulut hizmeti örneği test uç noktasını devre dışı bırak
* Diğer: Azure yay bulut hizmeti örneği test anahtarlarını listeleyin
* Diğer: Azure yay bulut hizmeti örneği test anahtarını yeniden üret

**Microsoft. AppPlatform/Spring/Apps**' den şunları seçin:
* Write: yazma Microsoft Azure yay bulutu uygulaması
* Sil: Microsoft Azure Spring Cloud uygulamasını Sil
* Okuma: Microsoft Azure Spring Cloud uygulamasını okuyun
* Diğer: Microsoft Azure yay bulut uygulaması kaynak yükleme URL 'sini al
* Diğer: Microsoft Azure yay bulut uygulaması özel etki alanını doğrula

**Microsoft. AppPlatform/yay/uygulamalar/bağlamalar**' nden şunları seçin:
* Yazma: yazma Microsoft Azure yay bulut uygulaması bağlama
* Sil: Microsoft Azure Spring Cloud Application bağlamasını Sil
* Okuma: Microsoft Azure yay bulut uygulaması bağlamasını oku

**Microsoft. AppPlatform/yay/uygulamalar/dağıtımlar**' da şunları seçin:
* Yazma: yazma Microsoft Azure yay bulutu uygulaması dağıtımı
* Sil: Azure yay bulut uygulaması dağıtımını Sil
* Okuma: Microsoft Azure yay bulut uygulaması dağıtımını okuyun
* Diğer: Microsoft Azure yay bulut uygulaması dağıtımını Başlat
* Diğer: Microsoft Azure yay bulut uygulaması dağıtımını durdur
* Diğer: Microsoft Azure yay bulut uygulaması dağıtımını yeniden başlatın
* Diğer: Microsoft Azure yay bulut uygulaması dağıtım günlük dosyası URL 'SI al

**Microsoft. AppPlatform/SKU**'lardan şunları seçin:
* Okuma: kullanılabilir SKU 'ları listeleme

**Microsoft. AppPlatform/konumlardan** şunları seçin:
* Diğer: ad kullanılabilirliğini denetle

**Microsoft. AppPlatform/Locations/operationResults/Spring**' den şunları seçin:
* Okuma: okuma işlemi sonucu

**Microsoft. AppPlatform/Locations/operationStatus/operationId**'den şunları seçin:
* Okuma: okuma işlemi durumu

**Microsoft. AppPlatform/SKU**'lardan şunları seçin:
* Okuma: kullanılabilir SKU 'ları listeleme

   [İşlem ![ hatları izinleri ](media/spring-cloud-permissions/pipelines-permissions-box.png)](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. **Ekle**'ye tıklayın.

5. İzinleri gözden geçirin.

6. **Gözden geçir ve oluştur**’a tıklayın.


## <a name="see-also"></a>Ayrıca bkz.
* [Azure portalı kullanarak özel Azure rolleri oluşturma veya güncelleştirme](../role-based-access-control/custom-roles-portal.md)

Özel izinleri tanımlayan üç yöntem hakkında daha fazla bilgi için bkz.:
* [Rolü kopyalama](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [Sıfırdan başlama](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [JSON 'dan Başlat](../role-based-access-control/custom-roles-portal.md#start-from-json)