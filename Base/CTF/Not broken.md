#forensic #writeup 

File -> export object -> HTTP

![image](https://github.com/user-attachments/assets/48f099bc-54bd-4ef4-aaf6-5dcd30c35410)

Save largest packet and open it

![image](https://github.com/user-attachments/assets/efd0be5b-9507-4357-ba2e-1f198689b7e1)

We can see that is request to image

![image](https://github.com/user-attachments/assets/ae510eff-c398-41c9-b681-03047ea8723a)

Delete everything up to %PNG and save as a png file

Есть pcap файл с каким то трафиком TCP Открываем в wireshark и нажимаем экспортировать (скрин 1)выбираем самый большой пакет и нажимаем сохранить (скрин 2) Открываем и видим что это обычный post запрос на загрузку картинки и после хедеров идет сама картинка. Открываем HxD и удаляем все из начала до сигнатуры png %PNG и сохраняем как .png, открываем и получаем картинку с флагом. (3 скрин)Сложный не такой уж и сложный.

https://imgur.com/JavOYhz https://imgur.com/3Y0Dmvc https://imgur.com/xzaZAyt