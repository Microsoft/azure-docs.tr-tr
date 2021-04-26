---
title: Kimlik varlıkları
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: a74c0cad971389168d643c9504f5bb809438a1ea
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097305"
---
### <a name="financial-account-identification"></a>Finansal hesap kimliği

Bu varlık kategorisi, mali bilgileri ve resmi tanımlama biçimlerini içerir.

#### <a name="category-aba-routing-number"></a>Kategori: ABA Yönlendirme numarası

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        ABA Yönlendirme numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        American banker Association (ABA) transit yönlendirme numaraları. İle de döndürülür `domain=phi` .

        Bu varlık kategorisini almak için parametresine ekleyin `ABARoutingNumber` `pii-categories` . `ABARoutingNumber` , algılanırsa API yanıtında de döndürülür.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="category-swift-code"></a>Kategori: SWIFT kodu

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        SWIFT kodu

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Ödeme yönergesi bilgileri için SWIFT kodları. İle de döndürülür `domain=phi` .

        Bu varlık kategorisini almak için parametresine ekleyin `SWIFTCode` `pii-categories` . `SWIFTCode` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>Kategori: kredi kartı

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        Kredi kartı

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Kredi kartı numaraları. İle de döndürülür `domain=phi` .

        Bu varlık kategorisini almak için parametresine ekleyin `CreditCardNumber` `pii-categories` . `CreditCardNumber` algılanırsa API yanıtında döndürülür.

    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>Kategori: uluslararası bankacılık hesap numarası (IBAN) 

Bu kategori aşağıdaki varlığı içerir:

:::row:::
    :::column span="":::
        **Varlık**

        Kredi kartı

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Ödeme yönergesi bilgileri için ıBAN kodları. İle de döndürülür `domain=phi` .

        Bu varlık kategorisini almak için parametresine ekleyin `InternationlBankingAccountNumber` `pii-categories` . `InternationlBankingAccountNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="2":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>Kamu ve ülkeye/bölgeye özgü kimlik

> [!NOTE]
> Aşağıdaki mali ve ülkeye özgü varlıklar `domain=phi` parametresiyle döndürülmez:
> * Passport numaraları
> * Vergi kimlikleri

Aşağıdaki varlıklar, ülkeye göre gruplandırılır ve listelenir:

#### <a name="argentina"></a>Arjantin

:::row:::
    :::column span="":::
        **Varlık**

        Arjantin Ulusal kimlik (DNı) numarası 

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar** İle de döndürülür `domain=phi` .
        
        Bu varlık kategorisini almak için parametresine ekleyin `ARNationalIdentityNumber` `pii-categories` . `ARNationalIdentityNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>Avusturya

:::row:::
    :::column span="":::
        **Varlık**

        Avusturya kimlik kartı

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `ATIdentityCard` `pii-categories` . `ATIdentityCard` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Avusturya vergi kimlik numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `ATTaxIdentificationNumber` `pii-categories` . `ATTaxIdentificationNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Avusturya değeri eklenen vergi (KDV) numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `ATValueAddedTaxNumber` `pii-categories` . `ATValueAddedTaxNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>Avustralya

:::row:::
    :::column span="":::
        **Varlık**

        Avustralya banka hesap numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `AUDriversLicenseNumber` `pii-categories` . `AUDriversLicenseNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Avustralya iş numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `AUBusinessNumber` `pii-categories` . `AUBusinessNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Avustralya şirket numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `AUCompanyNumber` `pii-categories` . `AUCompanyNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Avustralya sürücüsünün lisansı  

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `AUDriversLicense` `pii-categories` . `AUDriversLicense` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Avustralya tıbbi hesap numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `AUMedicalAccountNumber` `pii-categories` . `AUMedicalAccountNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Avustralya Passport numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `ATPassportNumber` `pii-categories` . `ATPassportNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Avustralya Vergi dosya numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `ATTaxIdentificationNumber` `pii-categories` . `ATTaxIdentificationNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>Belçika

:::row:::
    :::column span="":::
        **Varlık**

        Belçika Ulusal numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `BENationalNumber` `pii-categories` . `BENationalNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Belçika değer katma vergisi (KDV) numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `BEValueAddedTaxNumber` `pii-categories` . `BEValueAddedTaxNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>Brezilya 

:::row:::
    :::column span="":::
        **Varlık**

        Brezilya legal varlık numarası (CNPJ)

        

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `BRLegalEntityNumber` `pii-categories` . `BRLegalEntityNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Brezilya CPF numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `BRCPFNumber` `pii-categories` . `BRCPFNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Brezilya Ulusal KIMLIK kartı (RG)

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `BRNationalIDRG` `pii-categories` . `BRNationalIDRG` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>Kanada

:::row:::
    :::column span="":::
        **Varlık**

        Kanada banka hesap numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `CABankAccountNumber` `pii-categories` . `CABankAccountNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanada sürücüsünün lisans numarası

    :::column-end:::

    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `CADriversLicenseNumber` `pii-categories` . `CADriversLicenseNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanada sağlık hizmeti numarası

        
    :::column-end:::

    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `CAHealthServiceNumber` `pii-categories` . `CAHealthServiceNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanada Passport numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `CAPassportNumber` `pii-categories` . `CAPassportNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanada Kişisel sağlık kimlik numarası (PHIN)

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `CAPersonalHealthIdentification` `pii-categories` . `CAPersonalHealthIdentification` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanada Sosyal sigorta numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `CASocialInsuranceNumber` `pii-categories` . `CASocialInsuranceNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>Şili 

:::row:::
    :::column span="":::
        **Varlık**

        Şili kimlik kartı numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `CLIdentityCardNumber` `pii-categories` . `CLIdentityCardNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>Çin

:::row:::
    :::column span="":::
        **Varlık**

        Çin yerleşik kimlik kartı (ÇHC) numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `CNResidentIdentityCardNumber` `pii-categories` . `CNResidentIdentityCardNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Avrupa Birliği (AB)

:::row:::
    :::column span="":::
        **Varlık**

        AB banka kartı numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `EUDebitCardNumber` `pii-categories` . `EUDebitCardNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        AB sürücüsünün lisans numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `EUDriversLicenseNumber` `pii-categories` . `EUDriversLicenseNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        AB GPU koordinatları

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `EUGPSCoordinates` `pii-categories` . `EUGPSCoordinates` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        AB Ulusal kimlik numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `EUNationalIdentificationNumber` `pii-categories` . `EUNationalIdentificationNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        AB Passport numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `EUPassportNumber` `pii-categories` . `EUPassportNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        AB sosyal güvenlik numarası (SSN) veya eşdeğer KIMLIK

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `EUSocialSecurityNumber` `pii-categories` . `EUSocialSecurityNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        AB vergi kimlik numarası (TIN)

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `EUTaxIdentificationNumber` `pii-categories` . `EUTaxIdentificationNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>Fransa

:::row:::
    :::column span="":::
        **Varlık**

        Fransa sürücüsünün lisans numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `FRDriversLicenseNumber` `pii-categories` . `FRDriversLicenseNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Fransa sağlık sigortası numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `FRHealthInsuranceNumber` `pii-categories` . `FRHealthInsuranceNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Fransa Ulusal KIMLIK kartı (CNı)

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `FRNationalID` `pii-categories` . `FRNationalID` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Fransa Passport numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `FRPassportNumber` `pii-categories` . `FRPassportNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Fransa sosyal güvenlik numarası (ıNSEE)

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `FRSocialSecurityNumber` `pii-categories` . `FRSocialSecurityNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Fransa vergi kimlik numarası (Numéro SPI)

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `FRTaxIdentificationNumber` `pii-categories` . `FRTaxIdentificationNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Fransa değeri eklenen vergi (KDV) numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `FRValueAddedTaxNumber` `pii-categories` . `FRValueAddedTaxNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>Almanya

:::row:::
    :::column span="":::
        **Varlık**

        Almanya sürücüsünün lisans numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `DEDriversLicenseNumber` `pii-categories` . `DEDriversLicenseNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Almanya kimlik kartı numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `DEIdentityCardNumber` `pii-categories` . `DEIdentityCardNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Almanya Passport numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `DEPassportNumber` `pii-categories` . `DEPassportNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Almanya vergi kimlik numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `DETaxIdentificationNumber` `pii-categories` . `DETaxIdentificationNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Almanya katma değer vergi numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `DEValueAddedNumber` `pii-categories` . `DEValueAddedNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>Hong Kong

:::row:::
    :::column span="":::
        **Varlık**

        Hong Kong kimlik kartı (HKıD) numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `HKIdentityCardNumber` `pii-categories` . `HKIdentityCardNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Macaristan

:::row:::
    :::column span="":::
        **Varlık**

        Macaristan kişisel kimlik numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `HUPersonalIdentificationNumber` `pii-categories` . `HUPersonalIdentificationNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Macaristan vergi kimlik numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `HUTaxIdentificationNumber` `pii-categories` . `HUTaxIdentificationNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Macaristan değeri eklenen vergi numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `HUValueAddedNumber` `pii-categories` . `HUValueAddedNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>Hindistan

:::row:::
    :::column span="":::
        **Varlık**

        Hindistan kalıcı hesap numarası (PAN)

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `INPermanentAccount` `pii-categories` . `INPermanentAccount` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Hindistan benzersiz tanımlama (aadhaar) numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `INUniqueIdentificationNumber` `pii-categories` . `INUniqueIdentificationNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Endonezya

:::row:::
    :::column span="":::
        **Varlık**

        Endonezya kimlik kartı (KTP) numarası

    :::column-end:::
    :::column span="2":::

        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `IDIdentityCardNumber` `pii-categories` . `IDIdentityCardNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>İrlanda

:::row:::
    :::column span="":::
        **Varlık**

        İrlanda Personal kamu hizmeti (PPS) numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `IEPersonalPublicServiceNumber` `pii-categories` . `IEPersonalPublicServiceNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        İrlanda Personal kamu hizmeti (PPS) numarası v2

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `IEPersonalPublicServiceNumberV2` `pii-categories` . `IEPersonalPublicServiceNumberV2` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>İsrail

:::row:::
    :::column span="":::
        **Varlık**

        İsrail Ulusal KIMLIĞI

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `ILNationalID` `pii-categories` . `ILNationalID` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        İsrail banka hesap numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `ILBankAccountNumber` `pii-categories` . `ILBankAccountNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>İtalya

:::row:::
    :::column span="":::
        **Varlık**

        İtalya sürücüsünün lisans KIMLIĞI

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `ITDriversLicenseNumber` `pii-categories` . `ITDriversLicenseNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        İtalya mali kodu

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `ITFiscalCode` `pii-categories` . `ITFiscalCode` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        İtalya değeri eklenen vergi numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `ITValueAddedTaxNumber` `pii-categories` . `ITValueAddedTaxNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japonya

:::row:::
    :::column span="":::
        **Varlık**

        Japon banka hesap numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `JPBankAccountNumber` `pii-categories` . `JPBankAccountNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japon sürücüsünün lisans numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `JPDriversLicenseNumber` `pii-categories` . `JPDriversLicenseNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonya "benim numaranız" (kişisel)

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `JPMyNumberPersonal` `pii-categories` . `JPMyNumberPersonal` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonya "My Number" (Kurumsal)

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `JPMyNumberCorporate` `pii-categories` . `JPMyNumberCorporate` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonya yerleşik kayıt numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `ITValueAddedTaxNumber` `pii-categories` . `ITValueAddedTaxNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japon konut kartı numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `JPResidenceCardNumber` `pii-categories` . `JPResidenceCardNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonya sosyal sigorta numarası (SIN)

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `JPSocialInsuranceNumber` `pii-categories` . `JPSocialInsuranceNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japonya Passport numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `JPPassportNumber` `pii-categories` . `JPPassportNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Lüksemburg

:::row:::
    :::column span="":::
        **Varlık**

        Lüksemburg ulusal kimlik numarası (doğal kişiler)

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `LUNationalIdentificationNumberNatural` `pii-categories` . `LUNationalIdentificationNumberNatural` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Lüksemburg ulusal kimlik numarası (doğal olmayan kişiler)

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `LUNationalIdentificationNumberNonNatural` `pii-categories` . `LUNationalIdentificationNumberNonNatural` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Varlık**

        Malta kimlik kartı numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `MTIdentityCardNumber` `pii-categories` . `MTIdentityCardNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Malta vergi kimlik numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `MTTaxIDNumber` `pii-categories` . `MTTaxIDNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Yeni Zelanda

:::row:::
    :::column span="":::
        **Varlık**

        Yeni Zelanda banka hesap numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `NZBankAccountNumber` `pii-categories` . `NZBankAccountNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Yeni Zelanda sürücüsünün lisans numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `NZDriversLicenseNumber` `pii-categories` . `NZDriversLicenseNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Yeni Zelanda Inland gelir numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `NZInlandRevenueNumber` `pii-categories` . `NZInlandRevenueNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Yeni Zelanda sağlık numarası Bakanlığı

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `NZMinistryOfHealthNumber` `pii-categories` . `NZMinistryOfHealthNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Yeni Zelanda sosyal Welfare numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `NZSocialWelfareNumber` `pii-categories` . `NZSocialWelfareNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filipinler

:::row:::
    :::column span="":::
        **Varlık**

        Filipinler Birleşik çok amaçlı KIMLIK numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `PHUnifiedMultiPurposeIDNumber` `pii-categories` . `PHUnifiedMultiPurposeIDNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portekiz 

:::row:::
    :::column span="":::
        **Varlık**

        Portekiz vatandaşlık kart numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `PTCitizenCardNumber` `pii-categories` . `PTCitizenCardNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Portekiz vergi kimlik numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `PTTaxIdentificationNumber` `pii-categories` . `PTTaxIdentificationNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapur

:::row:::
    :::column span="":::
        **Varlık**

        Singapur Ulusal kayıt KIMLIĞI kartı (NRIC) numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `PTTaxIdentificationNumber` `pii-categories` . `PTTaxIdentificationNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Güney Afrika

:::row:::
    :::column span="":::
        **Varlık**

        Güney Afrika kimlik numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `ZAIdentificationNumber` `pii-categories` . `ZAIdentificationNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Güney Kore

:::row:::
    :::column span="":::
        **Varlık**

        Güney Kore yerleşik kayıt numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `KRResidentRegistrationNumber` `pii-categories` . `KRResidentRegistrationNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>İspanya

:::row:::
    :::column span="":::
        **Varlık**

        İspanya DNı dili

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `ESDNI` `pii-categories` . `ESDNI` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        İspanya sosyal güvenlik numarası (SSN)

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `ESSocialSecurityNumber` `pii-categories` . `ESSocialSecurityNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        İspanya vergi kimlik numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `ESTaxIdentificationNumber` `pii-categories` . `ESTaxIdentificationNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>İsviçre

:::row:::
    :::column span="":::
        **Varlık**

        İsviçre sosyal güvenlik numarası AHV

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `CHSocialSecurityNumber` `pii-categories` . `CHSocialSecurityNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Tayvan 

:::row:::
    :::column span="":::
        **Varlık**

        Tayvan Ulusal KIMLIĞI

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `TWNationalID` `pii-categories` . `TWNationalID` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Tayvan yerleşik sertifikası (yay/TARC)

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `TWResidentCertificate` `pii-categories` . `TWResidentCertificate` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Tayvan Passport numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `TWPassportNumber` `pii-categories` . `TWPassportNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Birleşik Krallık

:::row:::
    :::column span="":::
        **Varlık**

        Krallık Sürücünün lisans numarası

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `UKDriversLicenseNumber` `pii-categories` . `UKDriversLicenseNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       Krallık Elektrotoral toplama numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `UKNationalInsuranceNumber` `pii-categories` . `UKNationalInsuranceNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Krallık Ulusal Sistem Sağlığı Hizmeti (NHS) numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `UKNationalHealthNumber` `pii-categories` . `UKNationalHealthNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Krallık Ulusal sigorta numarası (NINO)

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `UKNationalInsuranceNumber` `pii-categories` . `UKNationalInsuranceNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Krallık veya ABD Passport numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `USUKPassportNumber` `pii-categories` . `USUKPassportNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Krallık Benzersiz vergi mükellefi başvuru numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `UKUniqueTaxpayerNumber` `pii-categories` . `UKUniqueTaxpayerNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>Birleşik Devletler

:::row:::
    :::column span="":::
        **Varlık**

        ABD sosyal güvenlik numarası (SSN)

    :::column-end:::
    :::column span="2":::
        **Ayrıntılar**

        Bu varlık kategorisini almak için parametresine ekleyin `USSocialSecurityNumber` `pii-categories` . `USSocialSecurityNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::
      **Desteklenen Belge dilleri**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       ABD sürücüsünün lisans numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `USDriversLicenseNumber` `pii-categories` . `USDriversLicenseNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       ABD veya Birleşik Krallık Passport numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `USUKPassportNumber` `pii-categories` . `USUKPassportNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       ABD bireysel vergi mükellefi kimlik numarası (ITIN)

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `USIndividualTaxpayerIdentification` `pii-categories` . `USIndividualTaxpayerIdentification` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       ABD Ilaç zorlama Kurumu (DEA) numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `DrugEnforcementAgencyNumber` `pii-categories` . `DrugEnforcementAgencyNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       ABD banka hesap numarası

    :::column-end:::
    :::column span="2":::

        Bu varlık kategorisini almak için parametresine ekleyin `USBankAccountNumber` `pii-categories` . `USBankAccountNumber` algılanırsa API yanıtında döndürülür.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
