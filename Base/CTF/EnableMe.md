#forensic #writeup #macro

Вы получили конфиденциальный документ! Следуйте инструкциям, чтобы разблокировать его.

> Примечание: Это не вредоносная программа

## Решение

Мы получили файл с именем `invoice.docm`, **файлы.docm** представляют собой документы Microsoft Word с **поддержкой макросов**. Я запустил файл с помощью онлайн -песочницы под названием [any.run](https://app.any.run/tasks/7da2e855-e4d8-4e9b-b449-1fad3cc72ccd) и вот что я получил: <img src="https://github.com/HashemSalhi/CTF-Writeups/blob/main/UofTCTF%202024/Forensics/EnableMe/Screenshots/Screenshot.png">

Итак, давайте проверим макросы файлов с помощью `olevba` . <img src="https://github.com/HashemSalhi/CTF-Writeups/blob/main/UofTCTF%202024/Forensics/EnableMe/Screenshots/Screenshot1.png">

```
Sub AutoOpen()
    Dim v6 As Variant, v7 As Variant
    v6 = Array(98, 120, 113, 99, 116, 99, 113, 108, 115, 39, 116, 111, 72, 113, 38, 123, 36, 34, 72, 116, 35, 121, 72, 101, 98, 121, 72, 116, 39, 115, 114, 72, 99, 39, 39, 39, 106)
    v7 = Array(44, 32, 51, 84, 43, 53, 48, 62, 68, 114, 38, 61, 17, 70, 121, 45, 112, 126, 26, 39, 21, 78, 21, 7, 6, 26, 127, 8, 89, 0, 1, 54, 26, 87, 16, 10, 84)
    
    Dim v8 As Integer: v8 = 23

    Dim v9 As String, v10 As String, v4 As String, i As Integer
    v9 = ""
    For i = 0 To UBound(v6)
        v9 = v9 & Chr(v6(i) Xor Asc(Mid(Chr(v8), (i Mod Len(Chr(v8))) + 1, 1)))
    Next i

    v10 = ""
    For i = 0 To UBound(v7)
        v10 = v10 & Chr(v7(i) Xor Asc(Mid(v9, (i Mod Len(v9)) + 1, 1)))
    Next i

    MsgBox v10
End Sub
```

Код берет байты из `v6` и **преобразует** их с помощью ключа (**23**) Просто примените это к байтам из `v6`. <изображение src="https://github.com/HashemSalhi/CTF-Writeups/blob/main/UofTCTF%202024/Forensics/EnableMe/Screenshots/Screenshot2.png"> **uoftctf{d0cx_f1l35_c4n_run_c0de_t000}**

---

Нам предоставлен `.docm` файл, который известен (среди других офисных файлов) своими макросами и обманом сотрудников.

Я использую инструмент под названием [olevba](https://github.com/decalage2/oletools/wiki/olevba) для извлечения любых макросов из файла и поиска `AutoOpen.vba`. `AutoOpen` Имя файла позволяет автоматически запускать макрос при открытии документа.

```vb
Sub AutoOpen()
    Dim v6 As Variant, v7 As Variant
    v6 = Array(98, 120, 113, 99, 116, 99, 113, 108, 115, 39, 116, 111, 72, 113, 38, 123, 36, 34, 72, 116, 35, 121, 72, 101, 98, 121, 72, 116, 39, 115, 114, 72, 99, 39, 39, 39, 106)
    v7 = Array(44, 32, 51, 84, 43, 53, 48, 62, 68, 114, 38, 61, 17, 70, 121, 45, 112, 126, 26, 39, 21, 78, 21, 7, 6, 26, 127, 8, 89, 0, 1, 54, 26, 87, 16, 10, 84)

    Dim v8 As Integer: v8 = 23

    Dim v9 As String, v10 As String, v4 As String, i As Integer
    v9 = ""
    For i = 0 To UBound(v6)
        v9 = v9 & Chr(v6(i) Xor Asc(Mid(Chr(v8), (i Mod Len(Chr(v8))) + 1, 1)))
    Next i

    v10 = ""
    For i = 0 To UBound(v7)
        v10 = v10 & Chr(v7(i) Xor Asc(Mid(v9, (i Mod Len(v9)) + 1, 1)))
    Next i

    MsgBox v10
End Sub
```

Анализируя содержимое скрипта, кажется, что у него есть список символов ASCII, который выполняется через некоторые `Chr`, `Xor` и другие различные аргументы перед отображением результата `v10` в окне сообщения.

Запуск этого скрипта выдает нам окно сообщения со следующим: `YOU HAVE BEEN HACKED! Just kidding :)`

Если мы изменим `MsgBox` значение с `v10` на `v9`, мы получим флаг.

Отметить: `uoftctf{d0cx_f1l35_c4n_run_c0de_t000}`