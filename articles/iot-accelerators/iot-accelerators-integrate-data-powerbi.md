---
title: Power BI-Azure kullanarak uzaktan Izleme verilerini görselleştirin | Microsoft Docs
description: Bu öğretici, bir uzaktan Izleme çözümünün verilerini özelleştirilmiş bir görselleştirmeye dönüştürmek için Power BI Desktop ve Cosmos DB kullanır. Böylece kullanıcılar kendi özel panoları oluşturabilir ve bunları çözümde bulunmayan kullanıcılara paylaşabilir.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 13b10aa4f40b7c4b62b35e8580c2826c10e6683f
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166290"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Power BI kullanarak uzaktan Izleme verilerini görselleştirin

Bu öğreticide, CosmosDB 'den Power BI 'e uzaktan Izleme çözümü verilerinizi nasıl taktığınız açıklanmaktadır. Bu bağlantı kurulduunda, kendi özel panolarınızı oluşturabilir ve bunları uzaktan Izleme çözümü panonuza geri ekleyebilirsiniz. Bu iş akışı, daha fazla özelleşmiş grafiklerin oluşturulmasına izin verir. Daha sonra bu öğreticiyi kullanarak diğer veri akışlarıyla tümleştirebilir veya uzaktan Izleme çözümünüzün dışında tüketilen özel panolar oluşturabilirsiniz. Power BI panoları oluşturmak, belirli parçalar seçerken her bir paneli birbirleriyle etkileşime giremeyeceğiniz anlamına gelir. Örneğin, yalnızca sanal stoklarınızla ilgili bilgileri gösteren bir filtreniz olabilir ve panonuzun her parçası yalnızca sanal kamyon bilgilerini gösterecek şekilde etkileşime girebilir. Power BI dışında bir araç kullanmak istiyorsanız, bu adımları tercih ettiğiniz Görselleştirme aracını kullanmak için genişletebilir ve Cosmos veritabanına veya bir tane ayarladıysanız özel veritabanına bağlayabilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar

- Şu anda çalışan bir uzaktan Izleme çözümünüz olması gerekir
- [Azure portalına](https://portal.azure.com) ve IoT Hub ve çözümünün çalıştığı aboneliğinize erişiminizin olması gerekir
- [Power BI Masaüstü](https://powerbi.microsoft.com) yüklü olmalıdır, herhangi bir sürüm


## <a name="information-needed-from-azure-portal"></a>Azure portalından gereken bilgiler

1. [Azure Portal](https://portal.azure.com) 'a gidin ve gerekirse oturum açın

2. Sol bölmedeki kaynak grupları ' na tıklayın.

    ![Yan panel gezintisi](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. IoT çözümünüzün üzerinde çalıştığı kaynak grubuna gidin ve bu kaynak grubunun genel bakış sayfasına ulaşmak için tıklayın. 

4. Bu genel bakış sayfasında, "Azure Cosmos DB hesabı" türüne sahip olan öğeye tıklayın, daha sonra söz konusu IoT çözümünün Cosmos DB akışının genel bakış sayfasına yönlendirilirsiniz.

    ![Kaynak Grubu](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. Soldaki bölmede, "anahtarlar" bölümüne tıklayın ve Power BI içinde kullanılacak aşağıdaki değerleri göz önünde edin:

   - URI
   - Birincil anahtar

     ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Akışı Power BI ayarlama
  
1. Power BI Masaüstü uygulamasını açın ve sol üst köşedeki "verileri al" a tıklayın. 

    ![Veri Al](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Veri girmeniz istendiğinde, "Azure Cosmos DB" için arama yapın ve bu bağlayıcıyı seçin. Bu bağlayıcı temel olarak Azure IoT çözümünüzün Cosmos veritabanından verileri doğrudan çeker
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Yukarıda kaydettiğiniz bilgileri girin:

    * URI
    * Birincil anahtar

4. Power BI aktarılacak tüm tabloları seçin. Bu eylem, verilerin yüklenmesini sağlayacaktır. Çözümünüz daha uzun süredir çalışıyor, verilerin yüklenmesi daha uzun sürebilir (birkaç saate kadar). 

    ![Tabloları içeri aktar](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Verilerin yüklenmesi tamamlandıktan sonra, Power BI üst satırındaki "sorguları Düzenle" seçeneğine tıklayın ve her tablo için sarı çubukta bulunan oklara tıklayarak tüm tabloları genişletin. Bu, temelde tüm sütunları gösterecek şekilde genişletilir. Nem, hız saati vb. gibi şeyler için verilerin doğru türde olmaması fark edersiniz.

    ![Yeni sütun](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Örneğin, Power BI gelen veriler, bağlayıcı aracılığıyla geldiği zaman UNIX saat olarak değiştirilmiştir. Bu dönüştürmeyi ayarlamak için, ileriye doğru yeni bir sütun oluşturabilir ve bu denklemi tarih saat biçiminde almak için kullanabilirsiniz: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Güncelleştirilmiş tablo](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Belge. Device. msg. alındı, yalnızca UNIX biçimli sütunlardan biridir ve dönüştürme gerektiren diğer kullanıcılarla değiştirilebilir. 
  
    Diğer veri noktaları tür dizesine dönüştürüldü, yukarıdaki adımlarla aynı adımları kullanarak uygun yerlerde Double veya INT olarak değiştirilebilir.

## <a name="creating-a-dashboard"></a>Pano oluşturma

Akış bağlandıktan sonra kişiselleştirilmiş panolarınızı oluşturmaya hazırlanın! Aşağıdaki Pano, sanal cihazlarımızın yayılmakta olan Telemetriyi almanın ve bu gibi farklı özetlerinin gösterildiği bir örnektir: 

* Haritada cihaz konumu (sağ)
* Durumları ve önem derecesine sahip cihazlar. (sol üst)
* Kurallar yerinde olan cihazlar ve bunlar için herhangi bir uyarı varsa (alt sol tarafta)

![PowerBI görselleştirme](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Panoyu yayımlama ve verileri yenileme

Panolarınızı başarıyla oluşturduktan sonra, [Power BI panolarınızı](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) başkalarıyla paylaşmak üzere yayımlamanızı öneririz.

Ayrıca, en son veri kümesine sahip olduğunuzdan emin olmak için yayımlanan panodaki [verileri yenilemek](https://docs.microsoft.com/power-bi/refresh-data) isteyeceksiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Power BI kullanarak uzaktan izleme verilerini görselleştirme hakkında bilgi edindiniz

Uzaktan Izleme çözümünü özelleştirme hakkında daha fazla bilgi için bkz.:

* [Uzaktan Izleme çözümü Kullanıcı arabirimini özelleştirme](iot-accelerators-remote-monitoring-customize.md)
* [Geliştirici Başvuru Kılavuzu](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Geliştirici Sorun Giderme Kılavuzu](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

