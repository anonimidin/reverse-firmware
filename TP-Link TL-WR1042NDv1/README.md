
<h1 align="center">Прошивка роутера TP-Link TL-WR1042NDv1 (IoT 2014)</h1>

<h2>Введение</h2>

<p>Прошивки маршрутизатора и доступ к внутренней файловой системе + обзор системы. Скачать саму прошивку можно по этой ссылке: https://www.tp-link.com/resources/software/TL-WR1042ND_V1_130923.zip</p>

<h2>Анализ файла</h2>

<p>Разархивируем zip и получем бинарный файл <b>file wr1042nv1_en_3_15_7_up_boot\(130923\).bin </b></p>

```
unzip TL-WR1042ND_V1_130923.zip
```
![1](https://github.com/anonimidin/reverse-firmware/assets/109206637/9477d457-1e10-42db-841f-f8c7f620a1b4)

<p>File инструмент использует заголовки (если они присутствуют) внутри двоичного файла, чтобы определить, какой тип файла это. Видим файл для прошивки TP-LINK.</p>

```
file wr1042nv1_en_3_15_7_up_boot\(130923\).bin
```
![2](https://github.com/anonimidin/reverse-firmware/assets/109206637/92b6c632-123a-4cb0-a8ab-f5eb973f3ba4)

<p>Для дальнейшего анализа использовал инструмент <b>binwalk</b>. <a href="https://github.com/ReFirmLabs/binwalk">Binwalk</a> - это быстрый, простой в использовании инструмент для анализа, обратной инженерии и извлечения изображений прошивки.</p>

```
binwalk -e wr1042nv1_en_3_15_7_up_boot\(130923\).bin && ls
```
![3](https://github.com/anonimidin/reverse-firmware/assets/109206637/b6c6e46a-2962-47fa-a7ed-6a92db00e701)

<p>Заходим в директорию извлеченых файлов и видим прошивку для TP-LINK-а, <a href="https://en.wikipedia.org/wiki/Lempel%E2%80%93Ziv%E2%80%93Markov_chain_algorithm">LZMA</a> алгоримт для сжатия и <a href="https://ru.wikipedia.org/wiki/Squashfs">SquashFS</a> файловую систему, по стандарту должна извлекаться вся файловая система но у меня возникли с этим проблемы, пришлось делать все вручную. Внутри этой директории содежрутся файлы LZMA, 120200.squashfs и пустая директория squash-root-0. Заинтересовал меня больше всего 120200.squashfc файл. Прочел про саму файловую систему и узнал подробности о нём. Перед тем, как сбросить файловую систему, это хорошая практика, чтобы увидеть, что внутри нее. Для этого я использовал инструмент <b>unsquashfs</b>.</p>

```
sudo apt install squashfs-tools -y && sudo unsquashfs 120200.squashfs
```
![10](https://github.com/anonimidin/reverse-firmware/assets/109206637/84027d21-391a-45f3-b2d1-878973e3391d)

<h3>Bingo! Получил всю настроеную систему /squashfs-root .</h3>

![5](https://github.com/anonimidin/reverse-firmware/assets/109206637/5c4a017d-4eda-44b4-a923-fe497fd8b662)

<h2>Обзор системы</h2>

<p>Простая busybox система основана на linux. Файл для автозапуска тасков - linuxrc.
<h5>Утилиты системы:</h5>

```
ls -lm /bin
```

![6](https://github.com/anonimidin/reverse-firmware/assets/109206637/6b9eccb9-61fa-4cdb-ba18-52aea283f620)

<h5>Директория /etc и пароли системы:</h5>

![7](https://github.com/anonimidin/reverse-firmware/assets/109206637/e9f2b3cc-fd19-4927-857a-ad23bf5dd205)

<h5>Файл wscd.conf:</h5> 
WSCD.Conf связан с функциональностью <a href="https://en.wikipedia.org/wiki/Wi-Fi_Protected_Setup">WPS (Wi-Fi Protected Setup)</a> устройства TP-Link беспроводной точки доступа. WPS является стандартом для простого и безопасного установления беспроводного сетевого соединения. SSID_PREFIX: этот параметр определяет префикс SSID для беспроводной сети. По умолчанию он установлен на TP-link_ap_. Дальше гугл поможет. <br>

![8](https://github.com/anonimidin/reverse-firmware/assets/109206637/c2c1e73b-9a12-45d5-9d53-66ed1a5cceca)

<h5>Директория web и исходные файлы веб шлюза:</h5>

![9](https://github.com/anonimidin/reverse-firmware/assets/109206637/cefcbab7-c1f0-4ba4-922b-d91ac75c8f19)
<br>Исходный код веб-интерфейса позволяет проверить, есть ли в нем зашитые значения, такие как пароли или ключи API и другие подобные данные.
</p>
<hr>
<h2>c2VlIHlvdSBzb29uIQ==</h2>
