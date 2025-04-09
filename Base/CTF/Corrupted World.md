#forensic #writeup #minecraft

> Я складывал все свои важные предметы в сундук, когда произошел сбой Minecraft. Теперь, когда я загружаю свой мир, сундук пуст. Помогите мне восстановить его содержимое.

Примечания:

- Версия игры: 1.20.2
- Вам не нужно владеть игрой, чтобы решить эту задачу

Подсказка:

> X: 104, Y: 63, Z: 248

Файл, с которого мы начинаем, - это файл Minecraft Anvil, специально для региона 0,0. Регионы содержат фрагменты, а фрагменты содержат блоки.

Для начала я нахожу, в каком фрагменте на самом деле находятся мои координаты, используя [этот инструмент](https://minecraft.tools/en/coordinate-calculator.php).

Я нахожу, что мой фрагмент - это `6,-15` (x, z).

Затем, прочитав [формат файла region](https://minecraft.wiki/w/Region_file_format), я обнаружил, что мы можем извлечь фрагмент данных, а затем распаковать его из zlib, а затем прочитать данные NBT.

Я пишу скрипт на Python для автоматического анализа и вывода фрагментов данных NBT (разбор после удаления данных заголовка вручную).

```python
import io, sys, math, zlib, nbtlib, os

print('Loading file...')
file=open("r.0.0.mca","rb")
data=file.read()
file.close()

count=0
print('Parsing...')
while len(data) > 0:
    length=(math.ceil(int.from_bytes(io.BytesIO(data).read(4), 'big')/4096)*4096)-5
    file_format=int.from_bytes(io.BytesIO(data[:5][4:]).read(1),'big')
    data=data[5:]
    writedata=data[:length]
    data=data[length:]
    with open(f'out/{count}.zlib', 'wb') as f:
            f.write(writedata)
            f.close()
    try:
        writedata=zlib.decompress(writedata)
        with open(f'out/{count}.nbt', 'wb') as f:
            f.write(writedata)
            f.close()
        nbt_file = nbtlib.load(f'out/{count}.nbt')
        print(int(nbt_file['']['xPos']),int(nbt_file['']['yPos']),int(nbt_file['']['zPos']))
        os.rename(f'out/{count}.nbt',f"out/{int(nbt_file['']['xPos'])},{int(nbt_file['']['yPos'])},{int(nbt_file['']['zPos'])},{count}.nbt")
    except:
        print(f'Error on chunk! {io.BytesIO(data[:4]).read(4)}, {length}, {file_format}')
    count+=1
```

При первом запуске мы получаем некоторые ошибки:

```
Error on chunk! b'\x86\x8b\xfb\xf8', 4091, 2
Error on chunk! b'', 2257321979, 140
```

Переходя к первому фрагменту, который перепутался в шестнадцатеричном редакторе (010 Editor), мы видим, что этот фрагмент на самом деле равен 8 КБ, а не 4, как указано в длине, поэтому мы редактируем байты длины из '4094' (b'\ x00 \ x00 \ x0f \ xfe') в '8192' (b '\ x00 \ x00 \ x20 \ x00'), что позволяет завершить работу скрипта без ошибок.

![[Pasted image 20240823101956.png]]

![[Pasted image 20240823102015.png]]
Теперь давайте поищем наши чанковые файлы, у нас есть два `-6,4,15` файла.

```
6,-4,15,246.nbt
6,-4,15,270.nbt
```

Просматривая оба варианта в шестнадцатеричном редакторе, мы видим `My Important Items` сундук в обоих, но только в `246` нем есть предметы.

Анализируя элементы в шестнадцатеричном редакторе, мы можем увидеть небольшую текстовую строку из 2 символов для каждого элемента.

![[Pasted image 20240823102502.png]]

	После совместного создания мы получаем флаг. `irisctf{block_game_as_a_file_system}`