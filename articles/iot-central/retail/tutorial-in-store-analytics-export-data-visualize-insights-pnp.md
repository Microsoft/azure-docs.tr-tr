---
title: 'Öğretici: Azure IoT Central verileri dışarı aktarın ve öngörüleri görselleştirin'
description: Bu öğreticide, IoT Central verileri dışarı aktarmayı ve Power BI panosunda öngörüleri görselleştirmeyi öğrenin.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: ce775f207eaa5df05900ea2e5b6d9fbeab4dc878
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112883"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Öğretici: Azure IoT Central verileri dışarı aktarın ve öngörüleri Power BI görselleştirin

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Önceki iki öğreticilerde, **Mağaza Analizi-kullanıma alma** uygulama şablonunu kullanarak bir IoT Central uygulaması oluşturdunuz ve özelleştirdiniz. Bu öğreticide, cihazdan toplanan Telemetriyi dışarı aktarmak için IoT Central uygulamanızı yapılandırırsınız. Daha sonra Power BI kullanarak, telemetriden türetilen öngörüleri görselleştirmek üzere mağaza yöneticisi için özel bir pano oluşturun.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Bir IoT Central uygulamasını bir olay hub 'ına telemetri dışarı aktarmak için yapılandırın.
> * Bir olay hub 'ından Power BI akış veri kümesine veri göndermek için Logic Apps kullanın.
> * Akış veri kümesindeki verileri görselleştirmek için Power BI panosu oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Önceki iki öğreticiden herhangi bir işlem yapmak için [azure IoT Central 'te bir mağaza analizi uygulaması oluşturun](./tutorial-in-store-analytics-create-app-pnp.md) ve [İşletmen panosunu özelleştirin ve Azure IoT Central cihazları yönetin](./tutorial-in-store-analytics-customize-dashboard-pnp.md).
* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
* Power BI hesabı. Power BI hesabınız yoksa, başlamadan önce [ücretsiz bir Power BI Pro denemeye](https://app.powerbi.com/signupredirect?pbi_source=web) kaydolun.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Olay Hub 'ınızı ve mantıksal uygulamanızı oluşturmadan önce, bunları yönetmek için bir kaynak grubu oluşturmanız gerekir. Kaynak grubu, **Mağaza Analytics-checkout** IoT Central uygulaması ile aynı konumda olmalıdır. Kaynak grubu oluşturmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **kaynak grupları**' nı seçin. Ardından **Ekle**'yi seçin.
1. **Abonelik**için, IoT Central uygulamanızı oluşturmak Için kullandığınız Azure aboneliğinin adını seçin.
1. **Kaynak grubu** adı için _Retail-Store-Analysis_* yazın.
1. **Bölge**için IoT Central uygulaması için seçtiğiniz bölgeyi seçin.
1. **Gözden geçir + oluştur**' u seçin.
1. **Gözden geçir + oluştur** sayfasında **Oluştur**' u seçin.

Artık aboneliğinizde **Retail-Store-Analysis** adlı bir kaynak grubunuz var.

## <a name="create-an-event-hub"></a>Olay hub’ı oluşturma

Perakende izleme uygulamasını telemetri dışa aktarmaya yapılandırmadan önce, dışarı aktarılmış verileri almak için bir olay hub 'ı oluşturmanız gerekir. Aşağıdaki adımlarda, Olay Hub 'ınızı nasıl oluşturacağınız gösterilmektedir:

1. Azure portal, ekranın sol üst kısmında bulunan **kaynak oluştur** ' u seçin.
1. **Market 'Te ara**' te _Event Hubs_girin ve ardından **ENTER**tuşuna basın.
1. Üzerinde **Event Hubs** sayfasında **Oluştur**.
1. **Ad alanı oluştur** sayfasında, aşağıdaki adımları uygulayın:
    * Ad alanı için adınız _-Retail-Store-Analysis_gibi benzersiz bir ad girin. Sistem, bu adın kullanılabilir olup olmadığını denetler.
    * **Temel** fiyatlandırma katmanını seçin.
    * IoT Central uygulamanızı oluşturmak için kullandığınız **aboneliği** seçin.
    * **Retail-Store-Analysis** kaynak grubunu seçin.
    * IoT Central uygulamanız için kullandığınız konumu seçin.
    * **Oluştur**'u seçin. Sistemin kaynakları sağlaması için birkaç dakika beklemeniz gerekebilir.
1. Portalda **Perakende-mağaza-analiz** kaynak grubuna gidin. Dağıtımın bitmesini bekleyin. Dağıtım durumunu güncelleştirmek için **Yenile** ' yi seçmeniz gerekebilir. Ayrıca, **bildirimlerde**Olay Hub 'ı ad alanı oluşturma durumunu da denetleyebilirsiniz.
1. **Perakende-mağaza-analiz** kaynak grubunda **Event Hubs ad alanını**seçin. Portalda **Event Hubs ad alanınız** için giriş sayfasını görürsünüz.

Artık bir **Event Hubs ad alanına**sahipsiniz, IoT Central uygulamanızla birlikte kullanmak üzere bir **Olay Hub** 'ı oluşturabilirsiniz:

1. Portalda **Event Hubs ad alanınız** için giriş sayfasında **+ Olay Hub**' ı seçin.
1. **Olay Hub 'ı oluştur** sayfasında, ad olarak _Mağaza telemetrisi_ girin ve ardından **Oluştur**' u seçin.

Artık IoT Central uygulamanızdan veri dışarı aktarma yapılandırdığınızda kullanabileceğiniz bir olay hub 'ına sahipsiniz:

![Olay hub'ı](./media/tutorial-in-store-analytics-visualize-insights-pnp/event-hub.png)

## <a name="configure-data-export"></a>Veri vermeyi yapılandırma

Artık bir olay hub 'ınız olduğundan, bağlı cihazlardan telemetri dışarı aktarmak için **Mağaza içi Analytics-Checkout** uygulamanızı yapılandırabilirsiniz. Aşağıdaki adımlarda, dışarı aktarmanın nasıl yapılandırılacağı gösterilmektedir:

1. **Mağaza içi analiz-kullanıma alma** IoT Central uygulamanızda oturum açın.
1. Sol bölmedeki **veri dışa aktarma** ' yı seçin.
1. **Azure Event Hubs > yeni**' yi seçin.
1. **Görünen ad**olarak _telemetri dışarı aktarmayı_ girin.
1. **Event Hubs ad**alanınızı seçin.
1. **Mağaza-telemetri** Olay Hub 'ını seçin.
1. **Verilerin dışarı aktarılması Için** **cihazların** ve **cihaz şablonlarının** kapalı olması bölümüne geçin.
1. **Kaydet**’i seçin.

Veri dışa aktarma işlemi, Olay Hub 'ınıza telemetri göndermeye başlamak birkaç dakika sürebilir. Dışarı aktarma durumunu **veri dışarı aktarmaları** sayfasında görebilirsiniz:

![Sürekli veri dışa aktarma yapılandırması](./media/tutorial-in-store-analytics-visualize-insights-pnp/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Power BI veri kümelerini oluşturma

Power BI panonuz, perakende izleme uygulamanızdan verileri görüntüler. Bu çözümde, Power BI panosu için veri kaynağı olarak Power BI akış veri kümeleri kullanırsınız. Bu bölümde, mantıksal uygulamanın verileri olay hub 'ından iletebilmesi için akış veri kümelerinin şemasını tanımlarsınız. Aşağıdaki adımlarda, ortam sensörleri için iki akış veri kümesi ve doluluk algılayıcısı için bir akış veri kümesi oluşturma gösterilmektedir:

1. **Power BI** hesabınızda oturum açın.
1. **Çalışma alanları**' nı ve ardından **çalışma alanı oluştur**' u seçin.
1. **Çalışma alanı oluştur** sayfasında, **çalışma alanı adı**olarak _Mağaza içi Analytics-Checkout_ girin.
1. **Mağaza içi analiz-kullanıma alma çalışma alanına hoş geldiniz** sayfasının en altına gidin ve **Atla**' yı seçin.
1. Çalışma alanı sayfasında **> akış veri kümesi oluştur**' u seçin.
1. **Yeni akış veri kümesi** sayfasında **API**' yi seçin ve ardından **İleri**' yi seçin.
1. **Veri kümesi adı**olarak _bölge 1 algılayıcı_ girin.
1. Akıştan aşağıdaki tablodaki üç **değeri** girin:

    | Değer adı  | Değer türü |
    | ----------- | ---------- |
    | Zaman damgası   | DateTime   |
    | Nem oranı    | Sayı     |
    | Sıcaklık | Sayı     |

1. **Tarihi geçmiş veri analizini** üzerine geçirin.
1. **Oluştur** ' u ve ardından **bitti**' yi seçin.
1. **Bölge 1 algılayıcı** akış veri kümesiyle aynı şema ve ayarlarla **bölge 2 algılayıcı** adlı başka bir akış veri kümesi oluşturun.

Artık iki akış veri kümeniz vardır. Mantıksal uygulama, **Mağaza içi analiz-kullanıma alma** uygulamanıza bağlı iki çevresel sensörden Telemetriyi bu iki veri kümesine yönlendirir:

![Bölge veri kümeleri](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-1.png)

Bu çözüm, Power BI akış verilerine filtre uygulamak mümkün olmadığından her bir algılayıcı için bir akış veri kümesi kullanır.

Ayrıca, doluluk telemetrisi için bir akış veri kümesi gerekir:

1. Çalışma alanı sayfasında **> akış veri kümesi oluştur**' u seçin.
1. **Yeni akış veri kümesi** sayfasında **API**' yi seçin ve ardından **İleri**' yi seçin.
1. **Veri kümesi adı**olarak _sahiplik algılayıcısı_ girin.
1. Aşağıdaki tabloda **akıştan beş değer** girin:

    | Değer adı     | Değer türü |
    | -------------- | ---------- |
    | Zaman damgası      | DateTime   |
    | Sıra uzunluğu 1 | Sayı     |
    | Sıra uzunluğu 2 | Sayı     |
    | İyi zaman 1   | Sayı     |
    | Diyi zaman 2   | Sayı     |

1. **Tarihi geçmiş veri analizini** üzerine geçirin.
1. **Oluştur** ' u ve ardından **bitti**' yi seçin.

Artık, sanal doluluk sensörindeki değerleri depolayan bir üçüncü akış veri kümeniz vardır. Bu algılayıcı, depodaki iki kullanıma alma işleminin sıra uzunluğunu ve müşterilerin bu kuyruklarda beklediği süreyi bildirir:

![Doluluk veri kümesi](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-2.png)

## <a name="create-a-logic-app"></a>Mantıksal uygulama oluşturma

Bu çözümde, Logic App Olay Hub 'ından Telemetriyi okur, verileri ayrıştırır ve sonra oluşturduğunuz Power BI akış veri kümelerine gönderir.

Mantıksal uygulamayı oluşturmadan önce [Azure 'da bir yerleşik analiz uygulaması oluşturma IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) öğreticisindeki IoT Central uygulamanıza bağladığınız Iki RuuviTag sensörlerinin cihaz kimliklerine sahip olmanız gerekir:

1. **Mağaza içi analiz-kullanıma alma** IoT Central uygulamanızda oturum açın.
1. Sol bölmedeki **cihazlar** ' ı seçin. Sonra **Ruuvitag**öğesini seçin.
1. **Cihaz kimliklerini**bir yere göz önünde yapın. Aşağıdaki ekran görüntüsünde, kimlikler **f5dcf4ac32e8** ve **e29ffc8d5326**' dir:

    ![Cihaz kimlikleri](./media/tutorial-in-store-analytics-visualize-insights-pnp/device-ids.png)

Aşağıdaki adımlarda Azure portal mantıksal uygulamayı nasıl oluşturacağınız gösterilmektedir:

1. [Azure Portal](https://portal.azure.com) oturum açın ve ekranın sol üst kısmında **kaynak oluştur** ' u seçin.
1. **Market 'Te arama**yapın bölümünde _Logic App_yazın ve **ENTER**tuşuna basın.
1. **Mantıksal uygulama** sayfasında **Oluştur**' u seçin.
1. **Mantıksal uygulama** Oluştur sayfasında:
    * Mantıksal uygulamanız için _ad-perakende-mağaza-analiz_gibi benzersiz bir ad girin.
    * IoT Central uygulamanızı oluşturmak için kullandığınız **aboneliği** seçin.
    * **Retail-Store-Analysis** kaynak grubunu seçin.
    * IoT Central uygulamanız için kullandığınız konumu seçin.
    * **Oluştur**'u seçin. Sistemin kaynakları sağlaması için birkaç dakika beklemeniz gerekebilir.
1. Azure portal yeni mantıksal uygulamanıza gidin.
1. **Logic Apps tasarımcı** sayfasında, aşağı kaydırarak **boş mantıksal uygulama**' yı seçin.
1. **Ara bağlayıcılar ve Tetikleyiciler**' de _Event Hubs_girin.
1. **Tetikleyiciler**' de **olayları Olay Hub 'ında ne zaman kullanılabilir olduğunu**seçin.
1. _Mağaza telemetrisini_ **bağlantı adı**olarak girin ve **Event Hubs ad**alanınızı seçin.
1. **RootManageSharedAccess** ilkesini seçin ve **Oluştur**' u seçin.
1. **Olay Hub 'ında olaylar kullanılabilir olduğunda** :
    * **Olay Hub 'ı adı**' nda **Mağaza-telemetri**' i seçin.
    * **İçerik türü**' nde **uygulama/JSON**' ı seçin.
    * **Aralığı** üç ve **Sıklık** olarak saniye olarak ayarlayın
1. Mantıksal uygulamanızı kaydetmek için **Kaydet** ' i seçin.

Mantıksal uygulama tasarımınıza mantığı eklemek için **kod görünümü**' nü seçin:

1. `"actions": {},` aşağıdaki JSON ile değiştirin. İki yer tutucuyu `[YOUR RUUVITAG DEVICE ID 1]` ve `[YOUR RUUVITAG DEVICE ID 2]` iki RuuviTag cihazlarınızdan not ettiğiniz kimliklerle değiştirin:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. **Kaydet** ' i seçin ve ardından, eklediğiniz mantığın görsel sürümünü görmek için **Tasarımcı** ' yı seçin:

    ![Mantıksal uygulama tasarımı](./media/tutorial-in-store-analytics-visualize-insights-pnp/logic-app.png)

1. Eylemi genişletmek için **DeviceID 'e göre Değiştir** ' i seçin. Ardından **bölge 1 ortamı**' nı seçin ve **Eylem Ekle**' yi seçin.
1. **Ara bağlayıcılar ve eylemler**' de **Power BI**girin ve ardından **ENTER**tuşuna basın.
1. **Veri kümesine satır ekle (Önizleme)** eylemini seçin.
1. **Oturum aç '** ı seçin ve Power BI hesabınızda oturum açmak için istemleri izleyin.
1. Oturum açma işlemi tamamlandıktan sonra, **bir veri kümesine satır ekleme** eylemine:
    * Çalışma alanı olarak **Mağaza içi analiz-kullanıma al** seçeneğini belirleyin.
    * Veri kümesi olarak **bölge 1 algılayıcı** seçin.
    * Tablo olarak **RealTimeData** öğesini seçin.
    * **Yeni parametre Ekle** ' yi seçin ve ardından **zaman damgası**, **nem**ve **sıcaklık** alanlarını seçin.
    * **Zaman damgası** alanını seçin ve ardından **dinamik içerik** listesinden **x-opt-enqueuedtime** ' ı seçin.
    * **Nem** alanını seçin ve ardından **telemetri ayrıştırmak**için **daha fazla göster** ' i seçin. Sonra **nem**' ı seçin.
    * **Sıcaklık** alanını seçin ve ardından **Telemetriyi ayrıştırmak**için **daha fazla göster** ' i seçin. Sonra **sıcaklık**' ı seçin.
    * Seçin **Kaydet** yaptığınız değişiklikleri kaydedin. **Bölge 1 ortamı** eylemi aşağıdaki ekran görüntüsüne benzer şekilde görünür: ![Bölge 1 ortamı](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-1-action.png)
1. **Bölge 2 ortamı** eylemini seçin ve **Eylem Ekle**' yi seçin.
1. **Ara bağlayıcılar ve eylemler**' de **Power BI**girin ve ardından **ENTER**tuşuna basın.
1. **Veri kümesine satır ekle (Önizleme)** eylemini seçin.
1. **Veri kümesine satır ekleme 2** eylemi:
    * Çalışma alanı olarak **Mağaza içi analiz-kullanıma al** seçeneğini belirleyin.
    * Veri kümesi olarak **bölge 2 algılayıcı** seçin.
    * Tablo olarak **RealTimeData** öğesini seçin.
    * **Yeni parametre Ekle** ' yi seçin ve ardından **zaman damgası**, **nem**ve **sıcaklık** alanlarını seçin.
    * **Zaman damgası** alanını seçin ve ardından **dinamik içerik** listesinden **x-opt-enqueuedtime** ' ı seçin.
    * **Nem** alanını seçin ve ardından **telemetri ayrıştırmak**için **daha fazla göster** ' i seçin. Sonra **nem**' ı seçin.
    * **Sıcaklık** alanını seçin ve ardından **Telemetriyi ayrıştırmak**için **daha fazla göster** ' i seçin. Sonra **sıcaklık**' ı seçin.
    Seçin **Kaydet** yaptığınız değişiklikleri kaydedin.  **Bölge 2 ortamı** eylemi aşağıdaki ekran görüntüsüne benzer şekilde görünür: ![Bölge 2 ortamı](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-2-action.png)
1. **Sahiplik** eylemini seçin ve ardından **arabirim kimliğiyle anahtar** eylemini seçin.
1. **Iyi zaman arabirimi** eylemini seçin ve **Eylem Ekle**' yi seçin.
1. **Ara bağlayıcılar ve eylemler**' de **Power BI**girin ve ardından **ENTER**tuşuna basın.
1. **Veri kümesine satır ekle (Önizleme)** eylemini seçin.
1. **Veri kümesine satır ekleme** eylemi:
    * Çalışma alanı olarak **Mağaza içi analiz-kullanıma al** seçeneğini belirleyin.
    * Veri kümesi olarak **sahiplik algılayıcısı** ' nı seçin.
    * Tablo olarak **RealTimeData** öğesini seçin.
    * **Yeni parametre Ekle** ' yi seçin ve ardından **zaman damgası**, **diyi zaman 1**ve **diyi zaman 2** alanlarını seçin.
    * **Zaman damgası** alanını seçin ve ardından **dinamik içerik** listesinden **x-opt-enqueuedtime** ' ı seçin.
    * **Iyi saat 1** alanını seçin ve ardından **telemetri ayrıştırmak**için **daha fazla göster** ' i seçin. Ardından **DwellTime1**öğesini seçin.
    * **Diyi zaman 2** alanını seçin ve ardından **Telemetriyi ayrıştırmak**için **daha fazla bilgi** 'yi seçin. Ardından **DwellTime2**öğesini seçin.
    * Seçin **Kaydet** yaptığınız değişiklikleri kaydedin. **Iyi zaman arabirimi** eylemi şu ekran görüntüsüne benzer şekilde görünür: ![sahiplik eylemi](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-1.png)
1. **Kişi sayısı arabirim** eylemini seçin ve **Eylem Ekle**' yi seçin.
1. **Ara bağlayıcılar ve eylemler**' de **Power BI**girin ve ardından **ENTER**tuşuna basın.
1. **Veri kümesine satır ekle (Önizleme)** eylemini seçin.
1. **Veri kümesine satır ekleme** eylemi:
    * Çalışma alanı olarak **Mağaza içi analiz-kullanıma al** seçeneğini belirleyin.
    * Veri kümesi olarak **sahiplik algılayıcısı** ' nı seçin.
    * Tablo olarak **RealTimeData** öğesini seçin.
    * **Yeni parametre Ekle** ' yi seçin ve ardından **zaman damgası**, **sıra uzunluğu 1**ve **sıra uzunluğu 2** alanlarını seçin.
    * **Zaman damgası** alanını seçin ve ardından **dinamik içerik** listesinden **x-opt-enqueuedtime** ' ı seçin.
    * **Sıra uzunluğu 1** alanını seçin ve ardından **telemetri ayrıştırmak**için **daha fazla göster** ' i seçin. Ardından **count1**öğesini seçin.
    * **Sıra uzunluğu 2** alanını seçin ve ardından **telemetri ayrıştırmak**için **daha fazla göster** ' i seçin. Ardından **count2**öğesini seçin.
    * Seçin **Kaydet** yaptığınız değişiklikleri kaydedin. **Kişi sayısı arabirim** eylemi şu ekran görüntüsüne benzer şekilde görünür: ![sahiplik eylemi](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-2.png)

Mantıksal uygulama otomatik olarak çalışır. Her çalıştırmanın durumunu görmek için Azure portal mantıksal uygulamanın **genel bakış** sayfasına gidin:

## <a name="create-a-power-bi-dashboard"></a>Power BI panosu oluşturma

Artık, Olay Hub 'ınız aracılığıyla IoT Central uygulamanızdan telemetri akışı oluşturdunuz. Daha sonra mantıksal uygulamanız, Olay Hub 'ı iletilerini ayrıştırır ve bunları bir Power BI akış veri kümesine ekler. Şimdi Telemetriyi görselleştirmek için bir Power BI panosu oluşturabilirsiniz:

1. **Power BI** hesabınızda oturum açın.
1. **Mağaza > çalışma alanlarını**seçin.
1. **> Panosu Oluştur ' u**seçin.
1. **Depo analizlerini** Pano adı olarak girin ve **Oluştur**' u seçin.

### <a name="add-line-charts"></a>Çizgi grafikleri ekleme

İki çevresel sensörden sıcaklık ve nem oranı göstermek için dört satırlık grafik kutucuğu ekleyin. Kutucukları oluşturmak için aşağıdaki tablodaki bilgileri kullanın. Her kutucuğu eklemek için, şunu seçerek başlayın **... (Daha fazla seçenek) > kutucuk ekleyin**. **Özel akış verileri**' ni seçin ve ardından **İleri**' yi seçin:

| Ayar | Grafik #1 | Grafik #2 | Grafik #3 | Grafik #4 |
| ------- | -------- | -------- | -------- | -------- |
| Veri kümesi | Bölge 1 algılayıcısı | Bölge 1 algılayıcısı | Bölge 2 algılayıcısı | Bölge 2 algılayıcısı |
| Görselleştirme türü | Çizgi grafik | Çizgi grafik | Çizgi grafik | Çizgi grafik |
| Kesmesini | Zaman damgası | Zaman damgası | Zaman damgası | Zaman damgası |
| Değerler | Sıcaklık | Nem oranı | Sıcaklık | Nem oranı |
| Zaman penceresi | 60 dakika | 60 dakika | 60 dakika | 60 dakika |
| Başlık | Sıcaklık (1 saat) | Nem (1 saat) | Sıcaklık (1 saat) | Nem (1 saat) |
| Konu | Bölge 1 | Bölge 1 | Bölge 2 | Bölge 2 |

Aşağıdaki ekran görüntüsünde, ilk grafiğin ayarları gösterilmektedir:

![Çizgi grafik ayarları](./media/tutorial-in-store-analytics-visualize-insights-pnp/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Çevresel verileri göstermek için kartlar ekleyin

İki çevresel sensörden en son sıcaklık ve nem değerlerini göstermek için dört kart kutucuğu ekleyin. Kutucukları oluşturmak için aşağıdaki tablodaki bilgileri kullanın. Her kutucuğu eklemek için, şunu seçerek başlayın **... (Daha fazla seçenek) > kutucuk ekleyin**. **Özel akış verileri**' ni seçin ve ardından **İleri**' yi seçin:

| Ayar | Kart #1 | Kart #2 | Kart #3 | Kart #4 |
| ------- | ------- | ------- | ------- | ------- |
| Veri kümesi | Bölge 1 algılayıcısı | Bölge 1 algılayıcısı | Bölge 2 algılayıcısı | Bölge 2 algılayıcısı |
| Görselleştirme türü | Kartından | Kartından | Kartından | Kartından |
| Alanlar | Sıcaklık | Nem oranı | Sıcaklık | Nem oranı |
| Başlık | Sıcaklık (F) | Nem (%) | Sıcaklık (F) | Nem (%) |
| Konu | Bölge 1 | Bölge 1 | Bölge 2 | Bölge 2 |

Aşağıdaki ekran görüntüsünde, ilk kartın ayarları gösterilmektedir:

![Kart ayarları](./media/tutorial-in-store-analytics-visualize-insights-pnp/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Kullanıma alınan doluluk verilerini göstermek için kutucuk ekleyin

Depodaki iki kullanıma alma için sıra uzunluğu ve diyi zamanı göstermek üzere dört kart kutucuğu ekleyin. Kutucukları oluşturmak için aşağıdaki tablodaki bilgileri kullanın. Her kutucuğu eklemek için, şunu seçerek başlayın **... (Daha fazla seçenek) > kutucuk ekleyin**. **Özel akış verileri**' ni seçin ve ardından **İleri**' yi seçin:

| Ayar | Kart #1 | Kart #2 | Kart #3 | Kart #4 |
| ------- | ------- | ------- | ------- | ------- |
| Veri kümesi | Doluluk algılayıcısı | Doluluk algılayıcısı | Doluluk algılayıcısı | Doluluk algılayıcısı |
| Görselleştirme türü | Kümelenmiş sütun grafik | Kümelenmiş sütun grafik | Ölçer | Ölçer |
| Kesmesini    | Zaman damgası | Zaman damgası | Yok | Yok |
| Değer | İyi zaman 1 | Diyi zaman 2 | Sıra uzunluğu 1 | Sıra uzunluğu 2 |
| Zaman penceresi | 60 dakika | 60 dakika |  Yok | Yok |
| Başlık | İyi zaman | İyi zaman | Sıra uzunluğu | Sıra uzunluğu |
| Konu | Kullanıma al 1 | Kullanıma al 2 | Kullanıma al 1 | Kullanıma al 2 |

Panodaki kutucukları yeniden boyutlandırabilir ve yeniden düzenleyin aşağıdaki ekran görüntüsüne benzer şekilde görünür:

![Power BI Panosu](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard.png)

Panoyu daha fazla özelleştirmek için bazı ek grafik kaynakları ekleyebilirsiniz:

![Power BI Panosu](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

IoT Central uygulamanız ile işiniz bittiğinde, uygulamada oturum açıp **Yönetim** bölümündeki **uygulama ayarları** sayfasına giderek bunu silebilirsiniz.

Uygulamayı korumak, ancak bununla ilişkili maliyetleri azaltmak istiyorsanız, Olay Hub 'ınıza telemetri gönderen veri dışa aktarmayı devre dışı bırakın.

**Retail-Store-Analysis**adlı kaynak grubunu silerek Azure Portal Olay Hub 'ını ve mantıksal uygulamayı silebilirsiniz.

Çalışma alanı için Power BI ayarları sayfasından çalışma alanını silerek Power BI veri kümelerinizi ve panonuzu silebilirsiniz.

## <a name="next-steps"></a>Sonraki Adımlar

Bu üç öğreticide, **Mağaza içi analiz-kullanıma alma** IoT Central uygulama şablonunu kullanan bir uçtan uca çözüm gösterilmektedir. Cihazları uygulamaya bağladınız, cihazları izlemek için IoT Central kullandınız ve cihaz telemetrisinden öngörüleri görüntülemek için bir pano oluşturmak üzere Power BI kullandınız. Önerilen bir sonraki adım, diğer IoT Central uygulama şablonlarından birini keşfetmeye yönelik olur:

> [!div class="nextstepaction"]
> * [IoT Central ile enerji çözümleri oluşturun](../energy/overview-iot-central-energy.md)
> * [IoT Central kamu çözümleri oluşturun](../government/overview-iot-central-government.md)
> * [IoT Central ile sağlık çözümleri oluşturun](../healthcare/overview-iot-central-healthcare.md)
