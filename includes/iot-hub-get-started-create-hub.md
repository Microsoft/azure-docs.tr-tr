## <a name="create-an-iot-hub"></a>IoT hub oluşturma
Bağlanılacak sanal cihaz uygulamanız için bir IoT Hub oluşturun. Aşağıdaki adımlar, Azure portalını kullanarak bu görevi nasıl tamamlayacağınızı gösterir.

1. [Azure portalında][lnk-portal] oturum açın.
1. Atlama çubuğunda **Yeni** > **Nesnelerin İnterneti** > **IoT Hub**'a tıklayın.
   
    ![Azure portalı Atlama Çubuğu][1]
1. **IoT hub'ı** dikey penceresinde IoT hub'ınız için yapılandırmayı seçin.
   
    ![IOT hub'ı dikey penceresi][2]
   
   1. **Ad** kutusunda IoT hub'ınız için bir ad girin. **Ad** geçerli ve kullanılabilirse **Ad** kutusunun yanında yeşil bir onay işareti görünür.
   1. Bir [fiyatlandırma ve ölçek katmanı][lnk-pricing] seçin. Bu öğretici için belirli bir katman gerekmez. Bu öğretici için ücretsiz F1 katmanını kullanın.
   1. **Kaynak grubunda** bir kaynak grubu oluşturun veya var olan bir kaynak grubunu seçin. Daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma][lnk-resource-groups].
   1. **Konum**'da IoT hub'ınızı barındıracak konumu seçin. Bu öğretici için en yakın konumunuzu seçin.
1. IoT hub'ı yapılandırma seçeneklerinizi seçtiğinizde **Oluştur**'a tıklayın.  Azure'un IoT hub'ınızı oluşturması birkaç dakika sürebilir. Durumu denetlemek için Başlangıç Panosu veya Bildirimler panelinde ilerlemeyi izleyebilirsiniz.
   
    ![Yeni IOT hub'ı durumu][3]
1. IOT hub'ı sorunsuz oluşturulduğunda, yeni IOT hub'ı dikey penceresini açmak için Azure portalındaki IOT hub'ına yönelik yeni kutucuğa tıklayın. **Ana bilgisayar adını** not edin ve **Paylaşılan erişim ilkeleri**'ne tıklayın.
   
    ![Yeni IOT hub'ı dikey penceresi][4]
1. **Paylaşılan erişim ilkeleri** dikey penceresinde **iothubowner** ilkesine tıklayın, ardından **iothubowner** dikey penceresindeki IoT Hub bağlantı dizesini kopyalayın ve not edin. Daha fazla bilgi için "IoT Hub geliştirici kılavuzunun" [Erişim denetimi][lnk-access-control] bölümüne bakın.
   
    ![Paylaşılan erişim ilkeleri dikey penceresi][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
