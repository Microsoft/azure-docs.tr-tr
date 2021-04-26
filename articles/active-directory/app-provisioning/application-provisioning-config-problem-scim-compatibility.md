---
title: Etki alanları arası kimlik yönetimi (SCıM) 2,0 protokol uyumluluğu için sistem ile ilgili bilinen sorunlar-Azure AD
description: Azure AD 'de SCıM 2,0 ' i destekleyen Galeri dışı bir uygulama eklenirken karşılaşılan yaygın protokol uyumluluk sorunlarını çözme
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 04/07/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 268931715500631ac2d10feacebff1d5c65931bf
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028800"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Azure AD Kullanıcı sağlama hizmeti 'nin SCıM 2,0 protokol uyumluluğuyla ilgili bilinen sorunlar ve çözümleri

Azure Active Directory (Azure AD), [etki alanları arası kimlik yönetimi (SCıM) 2,0 protokol belirtimi Için sistemde](https://tools.ietf.org/html/draft-ietf-scim-api-19)tanımlanan arabirimi içeren bir Web hizmeti tarafından belirtilen herhangi bir uygulamaya veya sisteme Kullanıcı ve grupları otomatik olarak sağlayabilir. 

Azure AD 'nin SCıM 2,0 protokolü desteği, kullanıcıların ve grupların Azure AD 'den SCıM 2,0 ' i destekleyen uygulamalara otomatik olarak sağlanması için kullandığı protokolün belirli kısımlarını listeleyen, [etki alanları arası kimlik yönetimi (scım Azure Active Directory) Için sistem kullanma](use-scim-to-provision-users-and-groups.md)bölümünde açıklanmıştır.

Bu makalede, Azure AD Kullanıcı sağlama hizmeti 'nin SCıM 2,0 protokolüne uygunluğunu ve bu sorunları geçici olarak nasıl çözebileceğinizi açıklayan geçerli ve geçmişteki sorunlar açıklanmaktadır.

## <a name="understanding-the-provisioning-job"></a>Sağlama işini anlama
Sağlama hizmeti bir uygulamaya karşı işlem yapmak için bir iş kavramını kullanır. İş kimliği, [ilerleme çubuğunda](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar)bulunabilir. Tüm yeni sağlama uygulamaları, "Scim" ile başlayan bir iş kimliği ile oluşturulur. SCIM işi hizmetin geçerli durumunu temsil eder. Eski işlerin "customappsso" KIMLIĞI vardır. Bu iş, 2018 içindeki hizmetin durumunu temsil eder. 

Galeride bir uygulama kullanıyorsanız, iş genellikle uygulamanın adını içerir (örneğin, yakınlaştırma kar tanesi, dataBricks vb.). Galeri uygulaması kullanırken bu belgeleri atlayabilirsiniz. Bu, birincil olarak, JobId SCıM veya customAppSSO içeren Galeri olmayan uygulamalar için geçerlidir.

## <a name="scim-20-compliance-issues-and-status"></a>SCıM 2,0 uyumluluk sorunları ve durumu
Aşağıdaki tabloda, sabit olarak işaretlenen herhangi bir öğe, SCıM işinde uygun davranışın bulunduğu anlamına gelir. Yaptığımız değişiklikler için geriye dönük uyumluluk sağlamaya çalıştık. Ancak, eski davranışın uygulanması önerilmez. Yeni uygulamalar için yeni davranışı kullanmanızı ve mevcut uygulamaları güncellemeyi öneririz.

> [!NOTE]
> 2018 ' de yapılan değişiklikler için, customappsso davranışına geri dönebilirsiniz. 2018 ' den bu yana yapılan değişiklikler için, eski davranışa geri dönmek üzere URL 'Leri kullanabilirsiniz. Eski iş öğesine geri dönmenize veya bir bayrak kullanarak, yaptığımız değişiklikler için geriye dönük uyumluluk sağlamaya çalıştık. Ancak, daha önce belirtildiği gibi eski davranışı uygulamayı önermiyoruz. Yeni uygulamalar için yeni davranışı kullanmanızı ve mevcut uygulamaları güncellemeyi öneririz.

| **SCıM 2,0 uyumluluk sorunu** |  **Düzenle?** | **Onarma tarihi**  |  **Geriye dönük uyumluluk** |
|---|---|---|
| Azure AD, uygulamanın SCıM uç nokta URL 'sinin kökünde olması için "/Scim" gerektirir  | Yes  |  18 Aralık 2018 | customappSSO sürümüne düşürme |
| Uzantı öznitelikleri, ":" gösterimi yerine öznitelik adlarından önce nokta "." gösterimini kullanır |  Yes  | 18 Aralık 2018  | customappSSO sürümüne düşürme |
| Çok değerli öznitelikler için düzeltme eki istekleri geçersiz yol filtresi sözdizimi içeriyor | Yes  |  18 Aralık 2018  | customappSSO sürümüne düşürme |
| Grup oluşturma istekleri geçersiz bir şema URI 'SI içeriyor | Yes  |  18 Aralık 2018  |  customappSSO sürümüne düşürme |
| Uyumluluk sağlamak için düzeltme eki davranışlarını güncelleştirme (örn. Boolean ve uygun Grup üyeliği kaldırma işlemleri) | No | TBD| Önizleme bayrağını kullan |

## <a name="flags-to-alter-the-scim-behavior"></a>SCıM davranışını değiştirecek bayraklar
Varsayılan SCıM istemci davranışını değiştirmek için uygulamanızın kiracı URL 'sinde aşağıdaki bayrakları kullanın.

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.jpg" alt-text="Daha sonraki davranışa yönelik SCıM bayrakları.":::

* Düzeltme Eki davranışlarını güncelleştirmek ve SCıM uyumluluğunu sağlamak için aşağıdaki URL 'YI kullanın (örneğin, Boolean olarak etkin ve uygun Grup üyeliği kaldırma işlemleri). Bu davranış Şu anda yalnızca bayrak kullanılırken kullanılabilir, ancak önümüzdeki birkaç ay içinde varsayılan davranış olur. Not Bu önizleme bayrağının Şu anda isteğe bağlı sağlama ile çalışmadığına bakın. 
  * **URL (SCıM uyumlu):** aadOptscim062020
  * **SCıM RFC başvuruları:** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2
  * **Durum**
  ```json
   PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
   {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "remove",
            "path": "members[value eq \"16b083c0-f1e8-4544-b6ee-27a28dc98761\"]"
        }
    ]
   }

    PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "members",
            "value": [
                {
                    "value": "10263a6910a84ef9a581dd9b8dcc0eae"
                }
            ]
        }
    ]
    } 

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].value",
            "value": "someone@contoso.com"
        },
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].primary",
            "value": true
        },
        {
            "op": "replace",
            "value": {
                "active": false,
                "userName": "someone"
            }
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "active",
            "value": false
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department",
            "value": "Tech Infrastructure"
        }
    ]
    }
   
  ```

  * **Düşürme URL 'si:** Yeni SCıM uyumlu davranışı Galeri dışı uygulamada varsayılan haline geldikten sonra, eski, SCıM olmayan davranışa geri dönmek için aşağıdaki URL 'yi kullanabilirsiniz: AzureAdScimPatch2017
  


## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>Eski customappsso işinden SCıM işine yükseltme
Aşağıdaki adımları takip etmek, mevcut customappsso işinizi silecek ve yeni bir SCIM işi oluşturacak. 
 
1. Azure portal ' de oturum açın https://portal.azure.com .
2. Azure portal **Azure Active Directory > Enterprise Applications** bölümünde, mevcut SCIM uygulamanızı bulun ve seçin.
3. Mevcut SCıM uygulamanızın **Özellikler** bölümünde, **nesne kimliğini** kopyalayın.
4. Yeni bir Web tarayıcısı penceresinde, ' a gidin https://developer.microsoft.com/graph/graph-explorer ve uygulamanızın eklendiği Azure AD kiracısı için yönetici olarak oturum açın.
5. Graph Explorer 'da, sağlama işinizin KIMLIĞINI bulmak için aşağıdaki komutu çalıştırın. "[Nesne-kimliği]" değerini üçüncü adımdan kopyalanmış hizmet sorumlusu KIMLIĞI (nesne KIMLIĞI) ile değiştirin.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Işleri al](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Işleri al") 


6. Sonuçlarda, "customappsso" veya "Scim" ile başlayan tam "ID" dizesini kopyalayın.
7. Öznitelik eşleme yapılandırmasını almak için aşağıdaki komutu çalıştırın, böylece bir yedekleme yapabilirsiniz. Daha önce olduğu gibi aynı [nesne-kimliği] kullanın ve [iş-kimliği] değerini son adımdan kopyalanmış sağlama işi KIMLIĞIYLE değiştirin.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Şemayı al](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Şemayı al") 

8. JSON çıkışını son adımdan kopyalayın ve bir metin dosyasına kaydedin. JSON, eski uygulamanıza eklediğiniz herhangi bir özel öznitelik eşlemesi içerir ve yaklaşık binlerce satır JSON satırı olmalıdır.
9. Sağlama işini silmek için aşağıdaki komutu çalıştırın:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. En son hizmet düzeltmelerini içeren yeni bir sağlama işi oluşturmak için aşağıdaki komutu çalıştırın.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Son adımın sonuçlarında, "Scim" ile başlayan tam "ID" dizesini kopyalayın. İsteğe bağlı olarak, aşağıdaki komutu çalıştırarak eski öznitelik eşlemelerinizi yeniden uygulayın, [New-id] öğesini kopyaladığınız yeni iş KIMLIĞIYLE değiştirerek ve adım #7 istek gövdesi olarak JSON çıkışını girerek.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. İlk web tarayıcı penceresine dönün ve uygulamanız için **sağlama** sekmesini seçin.
13. Yapılandırmanızı doğrulayın ve sonra sağlama işini başlatın. 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>SCıM işinden customappsso işine indirgenme (önerilmez)
 Eski davranışa geri dönerek, ancak customappsso yaptığımız güncelleştirmelerden bazılarının avantajına sahip olmadığı ve sonsuza kadar desteklenmeme gibi bir işlem yapmanızı önermeyiz. 

1. Azure portal ' de oturum açın https://portal.azure.com .
2. **Azure Active Directory > kurumsal uygulamalar > uygulama oluştur** Azure Portal bölümünde, **Galeri olmayan** yeni bir uygulama oluşturun.
3. Yeni özel uygulamanızın **Özellikler** bölümünde, **nesne kimliğini** kopyalayın.
4. Yeni bir Web tarayıcısı penceresinde, ' a gidin https://developer.microsoft.com/graph/graph-explorer ve uygulamanızın eklendiği Azure AD kiracısı için yönetici olarak oturum açın.
5. Graph Explorer 'da, uygulamanız için sağlama yapılandırmasını başlatmak üzere aşağıdaki komutu çalıştırın.
   "[Nesne-kimliği]" değerini üçüncü adımdan kopyalanmış hizmet sorumlusu KIMLIĞI (nesne KIMLIĞI) ile değiştirin.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. İlk web tarayıcı penceresine dönün ve uygulamanız için **sağlama** sekmesini seçin.
7. Normalde yaptığınız gibi Kullanıcı sağlama yapılandırmasını doldurun.


## <a name="next-steps"></a>Sonraki adımlar
[SaaS uygulamalarına sağlama ve sağlamayı kaldırma hakkında daha fazla bilgi edinin](user-provisioning.md)
