# MySQL Sıkılaştırma

Bu döküman, MySQL kullanmayı planlayan kişilere yöneliktir. Özellikle bilgi varlığı güvenliğinin kritik olduğu projelerde, MySQL'in sıkılaştırma ayarları kesinlikle yapılmalıdır. Yapılmadığı durumlarda, MySQL de tutulan bilgilerin çalınması, değiştirilmesi gibi durumlarla karşı karşıya kalınabilir. 

Bu linkte MySQL'de bulunan ve bilinen açıklıkların listesini içermektedir. https://www.cvedetails.com/vulnerability-list/vendor_id-93/product_id-21801/Oracle-Mysql.html 

Döküman hazırlanırken birçok kaynak incelenmiş ve ortaya çıkan çalışma taranan kaynakların birleşimi olarak düşünülebilir.

1) MySQL Veritabanı Çalışma Ortamı Güvenliğinin Sağlanması	
   MySQL sunucu servisinin chroot ortamı altında çalışması sağlanarak, MySQL sunucu servisinin işletim sistemi     kaynaklarının tümüne erişimi kısıtlanmalıdır. Bu şekilde MySQL sunucu servisi sadece ihtiyaç duyacağı kaynaklara erişim sağlanmış olacaktır.
   
 2) MySQL Veritabanını Süper Kullanıcı Harici Bir Kullanıcı Hakları ile Çalıştırmak 	
 'group' ile belirtilen değer yapılandırması gerçekleştirilmek istenen programın adı ya da group.
Yapılandırma seçenekleri [group] ile başlayıp bir sonraki [group] adına kadar geçerli
olmaktadır. Örneğin [mysqld] ile mysqld sunucu yazılımı için etkinleştirilmek istenen değerler
belirtilecektir.  
