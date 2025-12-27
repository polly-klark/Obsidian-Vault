#fastapi #react #python #js

Для реализации разграничения доступа к разделам сайта для пользователей с разными уровнями привилегий, используя React на фронтенде и FastAPI на бэкенде, можно следовать следующему подходу: 
# 1. Настройка FastAPI 
## Установка необходимых библиотек 

Убедитесь, что у вас установлены необходимые библиотеки: 

```
pip install fastapi[all] python-jose[cryptography] passlib[bcrypt]
```
## Создание модели пользователя и ролей 

В файле `models.py` создайте модели для пользователя и ролей: 

```
from pydantic import BaseModel 
from passlib.context import CryptContext 

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto") 

class User(BaseModel): 
	username: str 
	password: str 
	role: str # Роль пользователя 
	
class UserInDB(User): 
	hashed_password: str 
	
# Пример базы данных пользователей 
fake_users_db = { 
	"admin": UserInDB(username="admin", hashed_password=pwd_context.hash("adminpass"), role="admin"), 
	"user": UserInDB(username="user", hashed_password=pwd_context.hash("userpass"), role="user"), 
	}
```
## Настройка аутентификации и авторизации 

В файле `main.py` настройте маршруты для аутентификации и проверки ролей: 

```
from fastapi import FastAPI, Depends, HTTPException, status 
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm 
from jose import JWTError, jwt 
from datetime import datetime, timedelta 
from models import User, UserInDB, pwd_context, fake_users_db 

app = FastAPI() 

SECRET_KEY = "your_secret_key" 
ALGORITHM = "HS256" 
ACCESS_TOKEN_EXPIRE_MINUTES = 30 

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token") 

def create_access_token(data: dict, expires_delta: timedelta = None): 
	to_encode = data.copy() 
	if expires_delta: 
		expire = datetime.utcnow() + expires_delta 
	else: 
		expire = datetime.utcnow() + timedelta(minutes=15) 
	to_encode.update({"exp": expire}) 
	return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM) 
	
def get_current_user(token: str = Depends(oauth2_scheme)): 
	credentials_exception = HTTPException( 
		status_code=status.HTTP_401_UNAUTHORIZED, 
		detail="Could not validate credentials", 
		headers={"WWW-Authenticate": "Bearer"}, 
	) 
	
	try: 
		payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM]) 
		username: str = payload.get("sub") 
		if username is None: 
			raise credentials_exception 
	except JWTError: 
		raise credentials_exception 
		
	user = fake_users_db.get(username) 
	if user is None: 
		raise credentials_exception 
		
	return user 
	
def role_checker(required_role: str): 
	def role_checker_inner(user: UserInDB = Depends(get_current_user)): 
		if user.role != required_role: 
			raise HTTPException(status_code=status.HTTP_403_FORBIDDEN,
								detail="Operation not permitted") 
		return user 
	return role_checker_inner 
	
@app.post("/token") 
async def login(form_data: OAuth2PasswordRequestForm = Depends()): 
	user = fake_users_db.get(form_data.username) 
	
	if not user or not pwd_context.verify(form_data.password, user.hashed_password): 
	raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED,
						detail="Incorrect username or password", 
						headers={"WWW-Authenticate": "Bearer"})
	access_token_expires = timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
	access_token = create_access_token(data={"sub": user.username}, expires_delta=access_token_expires) 
	
	return {"access_token": access_token, "token_type": "bearer"}

@app.get("/admin") 
async def read_admin_data(current_user: UserInDB = Depends(role_checker("admin"))): 
	return {"message": "This is admin data."} 

@app.get("/user") 
async def read_user_data(current_user: UserInDB = 
Depends(role_checker("user"))): 
	return {"message": "This is user data."} 

```
# 2. Настройка React 
## Установка необходимых библиотек 

Создайте новый проект React и установите Axios для работы с HTTP-запросами: 

```
npx create-react-app my-app 
cd my-app 
npm install axios react-router-dom 
```
## Создание компонентов для аутентификации и доступа к данным 

Создайте компоненты для входа и отображения данных в зависимости от роли пользователя. 
## Компонент входа ( `Login.js` ): 

```
import React, { useState } from 'react'; 
import axios from 'axios'; 

const Login = ({ setToken }) => { 
	const [username, setUsername] = useState(''); 
	const [password, setPassword] = useState(''); 
	
	const handleSubmit = async (e) => { 
		e.preventDefault(); 
	
		const response = await axios.post('http://localhost:8000/token', new URLSearchParams({ 
		username, 
		password, 
		})); 
	
		setToken(response.data.access_token); 
	}; 
	
	return (
		<form onSumbit={hendleSumbit}>
			input type="text" value={username} onChange={(e) => setUsername(e.target.value)} placeholder="Username" />
			input type="password" value={password} onChange={(e) => setPassword(e.target.value)} placeholder="Password" />
			<button type="sumbit">Login</button>
		</form>
	); 
}; 

export default Login; 
```

## Компонент для отображения данных ( `Dashboard.js` ):

```
import React from 'react'; 
import axios from 'axios';
 
const Dashboard = ({ token }) => { 
	const [data, setData] = React.useState(null);
	
	const fetchData = async (role) => { 
			const response = await axios.get(`http://localhost:8000/${role}`, {
				headers: { Authorization: `Bearer ${token}` }, 
			});
			
			setData(response.data); 
		}; 
		
	return (
		<div>
			<button onClick={() => fetchData('admin')}>Get Admin Data</button>
			<button onClick={() => fetchData('user')}>Get User Data</button>
			{data && <div>{JSON.stringify(data)}</div>
		</div>
	); 
}; 

export default Dashboard;
```

## Основной компонент приложения (`App.js`):

```
import React, { useState } from 'react'; 
import Login from './Login'; 
import Dashboard from './Dashboard'; 

const App = () => { 
	const [token, setToken] = useState(''); 
	
	return (
		<div>
			{!token ? ( 
				<Login setToken={setToken} />
			) : ( 
				<Dashboard token={token} />
			)}
		</div>
	); 
}; 

export default App; 
```

## Запуск приложения

1. Запустите сервер FastAPI: 

```
uvicorn main:app --reload
```
 
2. Запустите приложение React: 

```
npm start
```

Теперь вы можете войти в систему через компонент `Login`, а затем получать данные в зависимости от роли пользователя через компонент `Dashboard`. Этот пример демонстрирует базовую реализацию разграничения доступа с использованием FastAPI и React.

Citations: 
[1] https://github.com/amisadmin/fastapi-user-auth 
[2] https://www.youtube.com/watch?v=_k2M-LpxId8 
[3] https://developer.auth0.com/resources/code-samples/api/fastapi/basic-role-basedaccess-control 
[4] https://stackademic.com/blog/fastapi-role-base-access-control-with-jwt-9fa2922a088c 
[5] https://dev.to/mochafreddo/building-a-modern-user-permission-management-system-withfastapi-sqlalchemy-and-mariadb-5fp1 
[6] https://dev.to/moadennagi/role-based-access-control-using-fastapi-h59 
[7] https://www.permit.io/blog/implement-authorization-in-fastapi 
[8] https://www.freecodecamp.org/news/how-to-add-jwt-authentication-in-fastapi/