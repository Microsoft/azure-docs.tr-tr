---
title: API konuları | Azure Marketi
description: Market API 'Leri kullanılırken sürüm oluşturma, hata işleme ve yetkilendirme sorunları.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 07cdb5e44dde0ca655191111d0a23dbab85b4cb2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819734"
---
# <a name="api-considerations"></a>API konuları


<a name="api-versioning"></a>API sürümü oluşturma
--------------

API 'nin aynı anda kullanılabilen birden fazla sürümü olabilir. İstemciler, sorgu dizesinin bir parçası olarak `api-version` parametresini sağlayarak hangi sürümü çağırmak istediğinizi belirtmelidir.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Bilinmeyen veya geçersiz API sürümüne sahip bir isteğin yanıtı bir HTTP kodudur 400. Bu hata, yanıt gövdesinde bilinen API sürümlerinin koleksiyonunu döndürür.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Hatalar
------

API, karşılık gelen HTTP durum kodlarıyla ve isteğe bağlı olarak JSON olarak serileştirildiği yanıtta ek bilgilerle hatalara yanıt verir.
Bir hata aldığınızda, özellikle 400 sınıfı bir hata, temeldeki nedeni düzeltmeden önce isteği yeniden denemeyin. Örneğin, yukarıdaki örnek yanıtta, isteği yeniden göndermeden önce API sürümü parametresini onarın.

<a name="authorization-header"></a>Yetkilendirme üst bilgisi
--------------------

Bu başvurudaki tüm API 'Ler için, yetkilendirme üst bilgisini Azure Active Directory (Azure AD) tarafından alınan taşıyıcı belirteçle birlikte geçirmeniz gerekir. Bu üst bilgi, geçerli bir yanıt almak için gereklidir; Yoksa `401 Unauthorized` bir hata döndürülür. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
