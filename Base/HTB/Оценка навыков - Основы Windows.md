#windows #htb 

Недавно в Inlanefreight произошёл инцидент, когда недовольный сотрудник отдела маркетинга получил доступ к внутренней общей папке HR и удалил несколько конфиденциальных файлов и папок. К счастью, у ИТ-отдела были хорошие резервные копии, и они восстановили все удалённые данные. Теперь есть опасения, что этот недовольный сотрудник изначально получил доступ к общей папке HR. После оценки безопасности вы обнаружили, что ИТ-отдел, возможно, не до конца понимает, как работают разрешения в Windows. Вы проводите обучение и демонстрацию, чтобы показать сотрудникам, как правильно обмениваться файлами в среде Windows, а также просматривать службы из командной строки для выявления возможных нарушений.

Примечание: важно, чтобы каждый шаг выполнялся в том порядке, в котором они представлены. Начиная с шага 1 и заканчивая шагом 8, включая все связанные с ним спецификации. Пожалуйста, имейте в виду, что каждый шаг разработан таким образом, чтобы дать вам возможность применить навыки и концепции, изученные в этом модуле. Не торопитесь, получайте удовольствие и не стесняйтесь обращаться за помощью, если вы застряли.

В этой демонстрации вы:

## 1. Создание общей папки под названием "Данные компании".

## 2. Создание вложенной папки под названием HR внутри папки с данными компании

## 3. Создание пользователя по имени Джим

- `Uncheck: User must change password at logon`

## 4. Создание группы безопасности под названием HR

## 5. Добавление Джима в группу безопасности отдела кадров

## 6. Добавление группы безопасности HR в общую папку с данными компании и список разрешений NTFS

- `Remove the default group that is present`
- `Share Permissions: Allow Change & Read`
- `Disable Inheritance before issuing specific NTFS permissions`
- `NTFS permissions: Modify, Read & Execute, List folder contents, Read, Write`

## 7. Добавление группы безопасности HR в список разрешений NTFS для подпапки HR

- `Remove the default group that is present`
- `Disable Inheritance before issuing specific NTFS permissions`
- `NTFS permissions: Modify, Read & Execute, List folder contents, Read, and Write`

## 8. Использование PowerShell для составления списка сведений о службе

## Практика

Чтобы получить SID группы с именем «HR» в PowerShell, можно использовать следующий код, аналогичный вашему примеру для пользователя «Jim», но с указанием имени группы:

powershell

`$objGroup = New-Object System.Security.Principal.NTAccount("HR") $strSID = $objGroup.Translate([System.Security.Principal.SecurityIdentifier]) $strSID.Value`

Этот скрипт создаёт объект NTAccount для группы «HR», затем преобразует его в объект SecurityIdentifier и выводит значение SID группы[1](https://winitpro.ru/index.php/2016/05/27/kak-uznat-sid-polzovatelya-po-imeni-i-naoborot/)[4](https://iteron.ru/%D0%BA%D0%B0%D0%BA-%D1%83%D0%B7%D0%BD%D0%B0%D1%82%D1%8C-sid-%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D1%8F-%D0%BF%D0%BE-%D0%B8%D0%BC%D0%B5%D0%BD%D0%B8-%D0%B8-%D0%BD%D0%B0/).

Если у вас установлен модуль Active Directory, вы можете получить SID группы «HR» следующим образом:

powershell

`Import-Module ActiveDirectory Get-ADGroup -Identity "HR" | Select-Object -ExpandProperty SID`

Этот способ удобен для доменных групп и требует наличия модуля Active Directory[1](https://winitpro.ru/index.php/2016/05/27/kak-uznat-sid-polzovatelya-po-imeni-i-naoborot/)[2](https://fixmypc.ru/post/poisk-sid-v-powershell-polzovatelei-i-grupp/).

Таким образом, для поиска SID группы «HR» используйте либо классы .NET в PowerShell, либо командлет Get-ADGroup из модуля Active Directory.

### Citations:

1. [https://winitpro.ru/index.php/2016/05/27/kak-uznat-sid-polzovatelya-po-imeni-i-naoborot/](https://winitpro.ru/index.php/2016/05/27/kak-uznat-sid-polzovatelya-po-imeni-i-naoborot/)
2. [https://fixmypc.ru/post/poisk-sid-v-powershell-polzovatelei-i-grupp/](https://fixmypc.ru/post/poisk-sid-v-powershell-polzovatelei-i-grupp/)
3. [https://winitpro.ru/index.php/2015/05/21/powershell-get-aduser-poluchenie-dannih-o-polzovatelyax-active-directory/](https://winitpro.ru/index.php/2015/05/21/powershell-get-aduser-poluchenie-dannih-o-polzovatelyax-active-directory/)
4. [https://iteron.ru/%D0%BA%D0%B0%D0%BA-%D1%83%D0%B7%D0%BD%D0%B0%D1%82%D1%8C-sid-%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D1%8F-%D0%BF%D0%BE-%D0%B8%D0%BC%D0%B5%D0%BD%D0%B8-%D0%B8-%D0%BD%D0%B0/](https://iteron.ru/%D0%BA%D0%B0%D0%BA-%D1%83%D0%B7%D0%BD%D0%B0%D1%82%D1%8C-sid-%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D1%8F-%D0%BF%D0%BE-%D0%B8%D0%BC%D0%B5%D0%BD%D0%B8-%D0%B8-%D0%BD%D0%B0/)
5. [https://administra.top/powershell-opredelit-imya-uchetkigruppy-po-sid/](https://administra.top/powershell-opredelit-imya-uchetkigruppy-po-sid/)
6. [https://www.osp.ru/winitpro/2010/12/13007865](https://www.osp.ru/winitpro/2010/12/13007865)
7. [http://onreader.mdl.ru/MasteringActiveDirectory.2ed/content/Ch08.html](http://onreader.mdl.ru/MasteringActiveDirectory.2ed/content/Ch08.html)
8. [https://habr.com/ru/companies/servermall/articles/354166/](https://habr.com/ru/companies/servermall/articles/354166/)

---

Answer from Perplexity: [https://www.perplexity.ai/search/objuser-new-object-system-secu-__eONA44QLWBC3.L8iYXUg?utm_source=copy_output](https://www.perplexity.ai/search/objuser-new-object-system-secu-__eONA44QLWBC3.L8iYXUg?utm_source=copy_output)