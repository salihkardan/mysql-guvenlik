# MySQL Sıkılaştırma

Bu döküman, MySQL kullanmayı planlayan kişilere yöneliktir. Özellikle bilgi varlığı güvenliğinin kritik olduğu projelerde, MySQL'in sıkılaştırma ayarları kesinlikle yapılmalıdır. Yapılmadığı durumlarda, MySQL de tutulan bilgilerin çalınması, değiştirilmesi gibi durumlarla karşı karşıya kalınabilir. 

Bu linkte MySQL'de bulunan ve bilinen açıklıkların listesini içermektedir. https://www.cvedetails.com/vulnerability-list/vendor_id-93/product_id-21801/Oracle-Mysql.html 

Döküman hazırlanırken birçok kaynak incelenmiş ve ortaya çıkan çalışma taranan kaynakların birleşimi olarak düşünülebilir.

##### 1) MySQL Veritabanı Çalışma Ortamı Güvenliğinin Sağlanması	
   MySQL sunucu servisinin chroot ortamı altında çalışması sağlanarak, MySQL sunucu servisinin işletim sistemi     kaynaklarının tümüne erişimi kısıtlanmalıdır. Bu şekilde MySQL sunucu servisi sadece ihtiyaç duyacağı kaynaklara erişim sağlanmış olacaktır.
   
##### 2) MySQL Veritabanını Süper Kullanıcı Harici Bir Kullanıcı Hakları ile Çalıştırmak 	
 MySQL'i çalıştırırken süper kullanıcı kullanılmamalıdır. MySQL için sistemde ayrı bir kullanıcı oluşturulmalıdır. Oluşturulan kullanıcı bir gruba yerleştirilmelidir. 
 
 	'group' ile belirtilen değer yapılandırması gerçekleştirilmek istenen programın adı ya da group.
Yapılandırma seçenekleri [group] ile başlayıp bir sonraki [group] adına kadar geçerli
olmaktadır. Örneğin [mysqld] ile mysqld sunucu yazılımı için etkinleştirilmek istenen değerler
belirtilecektir.  
	
	“user = kullanıcı_adı” değeri ile MySQL sunucu servisinin hangi kullanıcı hakları ile
çalıştırılacağı belirlenmektedir. Burada root kullanıcı haklarına sahip olmayan MySQL
grubuna üye MySQL kullanıcısı belirtilmiştir. 
  

##### 3)  MySQL Veritabanı Dosya ve Dizin Erişim Güvenliği	
	MySQL sunucu servisi tarafından kullanılan dizin ve dosya sahipliği hakları root kullanıcısına,
grup sahipliği hakları ise MySQL sunucu servisini çalıştıran kullanıcının dahil olduğu gruba ait
olmalıdır. MySQL sunucu servisinin mysql/mysql kullanıcı, grup ikilisi hakları ile çalıştırıldığı
düşünüldüğünde örnek bir dosya/dizin sahipliği hakları aşağıdaki gibi olmalıdır. 

```
chown root:mysql [dosya/dizin]
```		
my.cnf” dosyasının sahipliği root kullanıcısı, grup sahipliği olarak ise MySQL sunucu
servisini çalıştıran kullanıcının ( Örneğin “mysql/mysql” kullanıcı/grup ikilisi ) dahil olduğu
grup olarak yapılandırmalı ve erişim izinleri için ise sayısal notasyon olarak 440 olarak
belirlenmelidir. 

```
chown root:mysql my.cnf 
chmod 440 my.cnf 
 ```   
##### 4) MySQL Veritabanı Servisinin Çalışacağı IP Adres Bilgisinin Belirlenmesi	
“bind-address= ip adres bilgisi” değeri ile MySQL sunucu servisinin bağlantıları dinleyeceği
ip adres bilgisi tanımlanmaktadır. Ön tanımlı olarak MySQL sunucu yazılımı bütün ağ
arayüzlerinden gelen bağlantıları kabul edecek şekilde yapılandırılmaktadır. Eğer MySQL
sunucu servisi ve web uygulaması aynı sunucu makine üzerinde çalışacak ise bu değer
“127.0.0.1” olarak belirtilmelidir. Aslında bu durumda “skip-networking” değeri
kullanılabilir. “skip-networking” değeri ile MySQL sunucu yazılımı hiçbir şekilde TCP/IP
bağlantılarını dinlemez. Bütün etkileşim (Linux/Unix) unix soketleri aracılığı ile
gerçekleştirilir. Ancak burada Apache ve MySQL sunucu servisleri için ayrı ayrı chroot
dizinleri oluşturulmuş ise, bu seçenek kullanılamamaktadır. Bu seçeneğin yerine “bindaddress=127.0.0.1”
değeri ile sadece belirtilen ip adreslerinden gelen bağlantıları kabul edecek
şekilde yapılandırılmalıdır. Eğer Apache ve MySQL sunucu servisleri aynı sunucu makine
üzerinde değil iseler, güvenlik duvarı ya da tcp_wrapper gibi uygulamalar aracılığı ile ip adres
kısıtlaması gerçekleştirilmelidir. Bu şekilde sadece belirtilen ip adreslerinden bağlantı kabul
edilecektir. Bunun yanında iletişimin açık bir şekilde değil, şifreli olarak ağ üzerinden
gerçekleştirilmesi sağlanmalıdır.

```
...
bind-address=127.0.0.1
...
```

##### 5) Yerel Erişim Güvenliğinin Artırılması
“LOAD DATA LOCAL INFILE” komutları engellenerek, yerel dosyaların yetkisiz erişimlerden
korunması sağlanmalıdır. Bu şekilde özellikle php ile geliştirilen web uygulamalarında
bulunabilecek olan, sql enjeksiyonu saldırılarından kaynaklanan yetkisiz yerel dosyaların
okunmasını engellemektedir. Bunun için MySQL sunucu servisi yapılandırma dosyasında
aşağıdaki değer bulunmalıdır. 
```
...
local-infile = 0 
...
```
##### 6) Yerel Erişim Güvenliğinin Artırılması
