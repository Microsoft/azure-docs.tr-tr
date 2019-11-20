---
title: Azure Logic Apps kapsama göre eylemleri gruplandırma ve çalıştırma
description: Azure Logic Apps grup durumuna göre çalışan kapsamlı eylemler oluşturun
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: b0f53d1dbcd5b8bbbe38ffe3dd9ba62087ed3432
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680013"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Azure Logic Apps kapsamları kullanarak Grup durumuna göre eylemleri çalıştırma

Eylemleri yalnızca başka bir eylem grubu başarılı veya başarısız olduktan sonra çalıştırmak için, bu eylemleri bir *kapsam*içinde gruplandırın. Bu yapı, eylemleri mantıksal grup olarak düzenlemek, grubun durumunu değerlendirmek ve kapsamın durumunu temel alan eylemler gerçekleştirmek istediğinizde yararlıdır. Bir kapsamdaki tüm eylemler çalışmayı bitirdikten sonra, kapsam de kendi durumunu alır. Örneğin, [özel durum ve hata işlemeyi](../logic-apps/logic-apps-exception-handling.md#scopes)uygulamak istediğinizde kapsamları kullanabilirsiniz. 

Bir kapsamın durumunu denetlemek için, bir Logic Apps 'in çalışma durumunu ("başarılı", "başarısız", "Iptal" vb.) belirlemede kullandığınız ölçütü kullanabilirsiniz. Varsayılan olarak, tüm kapsamın eylemleri başarılı olduğunda, kapsamın durumu "başarılı" olarak işaretlenir. Ancak kapsamdaki herhangi bir eylem başarısız olduğunda veya iptal edildiğinde, kapsamın durumu "başarısız" olarak işaretlenir. Kapsamlar için sınırlar [ve yapılandırma](../logic-apps/logic-apps-limits-and-config.md)konusuna bakın. 

Örneğin, aşağıda belirli eylemleri çalıştırmak için bir kapsam ve kapsamın durumunu denetlemek için bir koşul kullanan üst düzey bir mantıksal uygulama verilmiştir. Kapsamdaki herhangi bir eylem başarısız olursa veya beklenmedik şekilde sona bırakılırsa, kapsam sırasıyla "başarısız" veya "durduruldu" olarak işaretlenir ve mantıksal uygulama "kapsam başarısız oldu" iletisi gönderir. Tüm kapsamlı eylemler başarılı olursa, mantıksal uygulama "kapsam başarılı oldu" iletisi gönderir.

!["Zamanlama-yinelenme" tetikleyicisi ayarlama](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki örneği takip etmek için şu öğelere ihtiyacınız vardır:

* Azure aboneliği. Aboneliğiniz yoksa, [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* Logic Apps tarafından desteklenen herhangi bir e-posta sağlayıcısından bir e-posta hesabı. Bu örnek, Outlook.com kullanır. Farklı bir sağlayıcı kullanıyorsanız, genel akış aynı kalır, ancak UI farklı görünür.

* Bing Haritalar anahtarı. Bu anahtarı almak için bkz. <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Bing Haritalar anahtarı edinme</a>.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

## <a name="create-sample-logic-app"></a>Örnek mantıksal uygulama oluştur

İlk olarak, daha sonra bir kapsam ekleyebilmek için bu örnek mantıksal uygulamayı oluşturun:

![Örnek mantıksal uygulama oluştur](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Belirlediğiniz bir aralıkta Bing Haritalar hizmetini denetleyen bir **zamanlama-yinelenme** tetikleyicisi
* **Bing Haritalar-** iki konum arasındaki seyahat süresini denetleyen rota al eylemi
* Seyahat süresinin belirtilen seyahat süresini aşıp aşmadığını denetleyen koşullu bir ifade
* Geçerli seyahat süresi belirtilen zamandan fazla olan e-posta gönderen bir eylem

Mantıksal uygulamanızı dilediğiniz zaman kaydedebilirsiniz, bu nedenle işinizi sık sık kaydedin.

1. Henüz yapmadıysanız <a href="https://portal.azure.com" target="_blank">Azure Portal</a>oturum açın. Boş bir mantıksal uygulama oluşturma.

1. **Schedule-yinelenme** tetikleyicisini şu ayarlarla ekleyin: **Interval** = "1" ve **Sıklık** = "dakika"

   !["Zamanlama-yinelenme" tetikleyicisi ayarlama](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Görünümü görsel olarak basitleştirmek ve her eylemin tasarımcıdaki ayrıntılarını gizlemek için, bu adımlarda ilerleyecek şekilde her bir eylemin şeklini daraltın.

1. **Bing Haritalar-rota al** eylemini ekleyin. 

   1. Henüz bir Bing Haritalar bağlantınız yoksa bir bağlantı oluşturmanız istenir.

      | Ayar | Değer | Açıklama |
      | ------- | ----- | ----------- |
      | **Bağlantı Adı** | BingMapsConnection | Bağlantınıza bir ad verin. | 
      | **API Anahtarı** | <*your-Bing-Maps-key*> | Daha önce aldığınız Bing Haritalar anahtarını girin. | 
      ||||  

   1. Bu görüntünün altındaki tabloda gösterildiği gibi **rotayı al** eyleminizi ayarlayın:

      !["Bing Haritalar-rota al" eylemini ayarlama](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Bu parametreler hakkında daha fazla bilgi için bkz. [Rota hesaplama](https://msdn.microsoft.com/library/ff701717.aspx).

      | Ayar | Değer | Açıklama |
      | ------- | ----- | ----------- |
      | **Güzergah noktası 1** | < >*Başlat* | Yol başlangıcının kaynağını girin. | 
      | **Güzergah noktası 2** | <*bitiş* > | Yolun hedefini girin. | 
      | **Kaçının** | Hiçbiri | Yollarınızın önüne geçmek için, otoyollar, Tolls gibi öğeleri girin. Olası değerler için bkz. [Rota hesaplama](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **İyileştir** | timeWithTraffic | Rotayı iyileştirmek için bir parametre seçin (uzaklık, geçerli trafik bilgileriyle zaman vb.). Bu örnek şu değeri kullanır: "timeWithTraffic" | 
      | **Mesafe birimi** | <*your-preference*> | Rotayı hesaplamak için mesafe birimini girin. Bu örnek şu değeri kullanır: "mil" | 
      | **Seyahat modu** | Sürüş | Yönlendirmenize ait seyahat modunu girin. Bu örnekte bu değer "Itici" kullanılmaktadır | 
      | **Toplu Ulaşım Tarih-Saati** | Hiçbiri | Yalnızca aktarım modu için geçerlidir. | 
      | **Transit Tarih-tür türü** | Hiçbiri | Yalnızca aktarım modu için geçerlidir. | 
      ||||  

1. Geçerli seyahat zamanının trafik ile belirtilen bir süreyi aşıp aşmadığını denetleyen [bir koşul ekleyin](../logic-apps/logic-apps-control-flow-conditional-statement.md) . 
   Bu örnek için şu adımları izleyin:

   1. Koşulu şu açıklama ile yeniden adlandırın: **trafik süresi belirtilen süreden daha büyükse**

   1. En soldaki sütunda, **Değer Seç** kutusunun içine tıklayın, böylece dinamik içerik listesi görüntülenir. Bu listeden, saniye cinsinden **seyahat süresi trafiği** alanını seçin. 

      ![Koşul derleme](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Ortadaki kutuda şu işleci seçin: **büyüktür**

   1. En sağdaki sütunda, bu karşılaştırma değerini saniye cinsinden girin ve 10 dakikaya eşittir: **600**

      İşlem tamamlandığında koşulunuz şu örnekteki gibi görünür:

      ![Tamamlandı koşulu](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. **Eğer true ise** dalında, e-posta sağlayıcınız için bir "e-posta gönder" eylemi ekleyin. 
   Bu eylemi, bu görüntü altındaki adımları izleyerek ayarlayın:

   !["Eğer true" dalına "e-posta gönder" eylemi ekleyin](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. **To** alanına, sınama amacıyla e-posta adresinizi girin.

   1. **Konu** alanına şu metni girin:

      ```Time to leave: Traffic more than 10 minutes```

   1. **Gövde** alanına, sonundaki boşluk ile bu metni girin: 

      ```Travel time:```

      İmleç **gövde** alanında göründüğünde, bu noktada kullanılabilir olan herhangi bir parametreyi seçebilmeniz için dinamik içerik listesi açık kalır.

   1. Dinamik içerik listesinde **İfade**’yi seçin.

   1. **Div ()** işlevini bulun ve seçin. 
      İmlecinizi işlevin parantezleri içine koyun.

   1. İmleç işlevin parantezleri içindeyken dinamik içerik listesinin görünmesi için **dinamik içerik** ' i seçin. 
   
   1. **Yol al** bölümünde **trafik süresi trafiği** alanını seçin.

      !["Trafik süresi trafiği" ni seçin](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Alan JSON biçimine çözümlendikten sonra bir **virgül** (```,```) ve sonra sayı ```60``` ekleyin, böylece **trafik süresi trafiğinden** değeri saniyeler ile dakikaya dönüştürmeniz gerekir. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      İfadeniz Şu örneğe benzer şekilde görünür:

      ![Son ifade](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. İşiniz bittiğinde **Tamam**' ı seçin.

   <!-- markdownlint-disable MD038 -->
   1. İfade çözümlendikten sonra, bu metni başında boşluk olacak şekilde ekleyin: ``` minutes```
  
       **Body** alanınız Şu örneğe benzer şekilde görünür:

       ![Finished "Body" alanı ](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Mantıksal uygulamanızı kaydedin.

Ardından, belirli eylemleri gruplandırabilmeniz ve durumlarını değerlendirebilmeniz için bir kapsam ekleyin.

## <a name="add-a-scope"></a>Kapsam Ekle

1. Henüz yapmadıysanız, mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın. 

1. İş akışı konumuna istediğiniz kapsamı ekleyin. Örneğin, mantıksal uygulama iş akışındaki mevcut adımlar arasında bir kapsam eklemek için aşağıdaki adımları izleyin: 

   1. İşaretçinizi, kapsamı eklemek istediğiniz oka taşıyın. 
   **Eylem eklemek**> **artı işaretini** ( **+** ) seçin.

      ![Kapsam Ekle](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. Arama kutusuna filtreniz olarak "scope" yazın. 
   **Kapsam** eylemini seçin.

## <a name="add-steps-to-scope"></a>Kapsama adım ekleme

1. Şimdi adımları ekleyin veya kapsam içinde çalıştırmak istediğiniz mevcut adımları sürükleyin. Bu örnek için şu eylemleri kapsama sürükleyin:
      
   * **Rotayı al**
   * Trafik saati, hem **doğru** hem de **yanlış** dalları içeren **belirtilen süreden daha büyükse**

   Mantıksal uygulamanız Şu örneğe benzer şekilde görünür:

   ![Kapsam eklendi](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. Kapsam altında, kapsamın durumunu denetleyen bir koşul ekleyin. Koşulu şu açıklama ile yeniden adlandırın: **kapsam başarısız olursa**

   ![Kapsam durumunu denetlemek için koşul ekleyin](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. Koşulda, kapsamın durumunun "başarısız" veya "durduruldu" olarak eşit olup olmadığını denetleyen bu ifadeleri ekleyin. 

   1. Başka bir satır eklemek için **Ekle**' yi seçin. 

   1. Her satırda, sol taraftaki kutuyu tıklatarak dinamik içerik listesinin görünmesini sağlayın. 
   Dinamik içerik listesinden **ifade**' ı seçin. Düzenle kutusunda, bu ifadeyi girin ve ardından **Tamam**' ı seçin: 
   
      `result('Scope')[0]['status']`

      ![Kapsamın durumunu denetleyen bir ifade ekleyin](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Her iki satır için işleç olarak **eşittir** ' i seçin. 
   
   1. Karşılaştırma değerleri için, ilk satırda `Failed` girin. 
   İkinci satıra `Aborted` girin. 

      İşlem tamamlandığında koşulunuz şu örnekteki gibi görünür:

      ![Kapsamın durumunu denetleyen bir ifade ekleyin](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Şimdi koşulun kapsam durumunu denetlemesi ve sonraki adımlarda tanımladığınız eşleşen eylemi çalıştırması için koşulun `runAfter` özelliğini ayarlayın.

   1. **Kapsam başarısız olursa** , **üç nokta** (...) düğmesini seçin ve sonra **Çalıştır 'ı sonra Yapılandır**' ı seçin.

      ![' RunAfter ' özelliğini yapılandır](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Tüm bu kapsam durumlarını seçin: **başarılı**, **başarısız oldu**, **atlandı**ve **zaman aşımına uğradı**

      ![Kapsam durumlarını seçin](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. İşiniz bittiğinde **Bitti**'yi seçin. 
   Koşul şimdi bir "bilgi" simgesi gösterir.

1. **True ise** ve false dalları **varsa** , her bir kapsam durumuna göre gerçekleştirmek istediğiniz eylemleri ekleyin, örneğin, e-posta veya ileti gönderin.

   ![Kapsam durumuna göre gerçekleştirilecek eylemler ekleme](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Mantıksal uygulamanızı kaydedin.

Tamamlanmış mantıksal uygulamanız şu örnekteki gibi görünür:

![Kapsama sahip mantıksal uygulama tamamlandı](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Çalışmanızı test etme

Tasarımcı araç çubuğunda **Çalıştır**' ı seçin. Tüm kapsamlı eylemler başarılı olursa "kapsam başarılı" iletisini alırsınız. Kapsamlı eylemler başarılı olmazsa, "kapsam başarısız oldu" iletisini alırsınız. 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON tanımı

Kod görünümünde çalışıyorsanız, bunun yerine mantıksal uygulamanızın JSON tanımında bir kapsam yapısı tanımlayabilirsiniz. Örneğin, önceki mantıksal uygulamadaki tetikleyici ve eylemlerin JSON tanımı aşağıda verilmiştir:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Destek alın

* Sorularınız için [Azure Logic Apps forumunu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) ziyaret edin.
* Özellikleri ve önerileri göndermek veya Oylamak için [Azure Logic Apps kullanıcı geri bildirim sitesini](https://aka.ms/logicapps-wish)ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* [Bir koşula göre adımları çalıştırın (koşullu deyimler)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Farklı değerlere göre adımları Çalıştır (Switch deyimleri)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Çalıştır ve Yinele adımları (döngüler)](../logic-apps/logic-apps-control-flow-loops.md)
* [Paralel adımları çalıştırma veya birleştirme (dallar)](../logic-apps/logic-apps-control-flow-branches.md)
