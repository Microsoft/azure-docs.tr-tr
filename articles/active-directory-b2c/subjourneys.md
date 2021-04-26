---
title: Azure Active Directory B2C için alt bağlantı | Microsoft Docs
description: Azure Active Directory B2C bir özel ilkenin Sub. newys öğesini belirtin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f037d4283b4b05081ef47e7223495f6e19d460e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97386876"
---
# <a name="sub-journeys"></a>Alt, neys

Alt işlemler, Kullanıcı yolculuğu içindeki düzenleme adımlarının akışını düzenlemek ve basitleştirmek için kullanılabilir. [Kullanıcı yolculukları](userjourneys.md), bağlı olan taraf uygulamasının kullanıcı için istenen talepleri elde etmesine izin veren bir ilkenin kullandığı belirgin yolları belirtir. Kullanıcı, bağlı olan tarafa sunulacak talepleri almak için bu yolları izler. Diğer bir deyişle, Kullanıcı yolculukları, Azure AD B2C kimlik deneyimi çerçevesi, isteği işleyen bir son kullanıcının iş mantığını tanımlar. Kullanıcı yolculuğu, başarılı bir işlem için izlenmesi gereken bir düzenleme sırası olarak temsil edilir. Bir düzenleme adımının [Claimsexchange](userjourneys.md#claimsexchanges) öğesi, çalıştıran tek bir [Teknik profile](technicalprofiles.md) bağlıdır.

Bir alt yolculukta, Kullanıcı yolculuğu içinde herhangi bir noktada çağrılabilecek bir düzenleme adımları gruplandırmadır. Yeniden kullanılabilir adım dizileri oluşturmak veya iş mantığını daha iyi temsil etmek için dallanmayı uygulamak üzere Sub. neys kullanabilirsiniz.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>Kullanıcı yolculuğu dallanma

Alt bağlantı, her ikisi de başarılı bir işlem için izlenmesi gereken bir düzenleme sırası olarak temsil edildiği için [Kullanıcı, neys](userjourneys.md)gibi davranır. Kullanıcı, neys kendi başına çağrılabilir ve yürütmek için bir Sendclaim adımı gerektirir. Alt bağlantı, Kullanıcı ve bağımsız olarak çağrılamaz ve bağımsız olarak çağrılamaz ve Kullanıcı yolculuğunda her zaman çağrılır.

Dalın anahtar bileşeni, Kullanıcı yolculuğunda daha iyi iş mantığı işlemeye izin veriydi. Ortak düzenleme adımları ayrı ayrı olarak çağrılacak ayrı parçalar halinde gruplandırılır. Bir alt yolculukta, birden fazla düzenleme adımı birlikte birbirine bağlanmış (aynı önkoşullara sahip) bir yolculuğun basitleşebilir. Bir alt yolculuğun yalnızca Kullanıcı yolculuğu aracılığıyla çağrılması, başka bir alt yolculuğa çağrı yapmamamalıdır.

İki tür Sub, neys vardır:

- **Call** -çağırıcının denetimini döndürür. Alt bir yolculuğun yürütülmesi ve sonra Denetim, Kullanıcı yolculuğu içinde yürütülmekte olan düzenleme adımına döndürülür.
- **Transfer** -denetimi alt yolculuğa aktarır (geri alınamaz dallanma). Alt bir yolculuğun, talepleri bağlı olan taraf uygulamasına geri döndürmesi için bir Sendclaim adımı olması gerekir.

## <a name="example-scenarios"></a>Örnek senaryolar

### <a name="call-sub-journey"></a>Alt yolculuğa çağrı

Aşağıdaki senaryolarda bir çağrı alt yolculuğu yararlı olur:

- Yaş aşımları: Yaş aşımları Için Kullanıcı, neys arasında çok sayıda paylaşılan bileşen vardır. Dallanma, ortak öğeleri paylaşılabilir bileşenlere derlemeye izin verir.  
- Ebeveyn onayı: dallandırma, kullanıcının, kullanıcının onay gerektirdiğini bulduktan sonra bir ana izin Kullanıcı yolculuğuna dallayabilmesine olanak tanıyarak, ebeveyn onayı tasarımında kolaylık sağlar. 
- Oturum açmak için kaydolun: bir kullanıcının dizinde zaten varolduğu, ancak aslında bir hesap oluşturduğunu unuttulabileceği bir senaryoyu düşünün. Bu, kullanıcıya girdiğiniz kimlik bilgilerinin zaten var olduğunu ve kullanıcının bu kullanıcı için bir kaydolma akışından oturum açma akışından bir kayıt akışı yapmaya zorlanmasını zorunlu hale getirmek yerine, bu tür bir durumda istenebilir.  

### <a name="transfer-sub-journey"></a>Alt yolculuğa aktar

Aşağıdaki senaryolarda bir aktarım alt yolculuğu yararlı olur:

- Bir blok sayfası gösteriliyor.
- Bir/B testi, isteği bir belirteci yürütmek ve vermek için bir alt yolculuğa yönlendirerek test edin.

## <a name="adding-a-subjourneys-element"></a>Bir Subbir neys öğesi ekleme

Aşağıda, `SubJourney` `Call` Denetim Kullanıcı yolculuğuna geri döndüren türünde bir öğe örneği verilmiştir.

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

Aşağıda, `SubJourney` `Transfer` bağlı olan taraf uygulamasına bir belirteç döndüren türünde bir öğe örneği verilmiştir.

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-sub-journey-step"></a>Sub yolculuğu adımını çağırma

`InvokeSubJourney`Bir alt yolculuğun yürütülmesi için türünün yeni bir düzenleme adımı kullanılır. Aşağıda, bu düzenleme adımının tüm yürütme öğelerini gösteren bir örnek verilmiştir.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Alt bir yolculukta başka bir alt yolculuğa çağrı olmamalıdır.

## <a name="components"></a>Bileşenler

İlke tarafından desteklenen alt bağlantıyı tanımlamak için, ilke dosyasının en üst düzey öğesinin altına bir **subbir neys** öğesi ekleyin.

**Subıneys** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| Alt yolculuğa | 1: n | Tüm Kullanıcı akışı için gereken tüm yapıları tanımlayan bir alt yolculuğun. |

**Subıneys** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Id | Yes | İlkedeki alt yolculuğa başvurmak için Kullanıcı yolculuğu tarafından kullanılabilen alt e-Poney tanımlayıcısı. [Aday](userjourneys.md#journeylist) öğesi 'nin **Subbir Newınreferenceıd** öğesi bu özniteliğe işaret ediyor. |
| Tür | Yes | Olası değerler: `Call` , veya `Transfer` . Daha fazla bilgi için bkz. [Kullanıcı yolculuğu dallandırma](#user-journey-branching)|

**Subyolculuney** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1: n | Başarılı bir işlem için izlenmesi gereken bir düzenleme sırası. Her Kullanıcı yolculuğu, sırayla yürütülen bir düzenleme adımları sıralı listesinden oluşur. Herhangi bir adım başarısız olursa, işlem başarısız olur. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Orchestration Step öğelerinin tüm listesi için bkz. [User, neys](userjourneys.md).

## <a name="next-steps"></a>Sonraki adımlar

Kullanıcı, [neys](userjourneys.md) hakkında bilgi edinin