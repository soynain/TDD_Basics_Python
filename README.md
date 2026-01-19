# TDD_Basics_Python

En este repositorio aprenderemos a crear una api con FAST Api, aplicando PyTest para las pruebas unitarias,
por medio de la metodología *Test Driven Development*

De acuerdo a la IA, la metodología va así:

```markdown
🔁 ¿Qué es TDD (Test Driven Development)?

TDD es una metodología donde:

Primero escribes el test → luego el código mínimo → luego refactorizas

El ciclo clásico es:

🔴 Red → 🟢 Green → 🔵 Refactor

🔴 Red
Escribes un test que falla, porque aún no existe la funcionalidad.

🟢 Green
Escribes el mínimo código necesario para que el test pase.

🔵 Refactor
Limpias, mejoras el diseño, sin romper los tests.
```

Voy a ser sincero, como es con python, me da cierta pereza aprender la metodología aquí pero si es como dicta la metodología.
En si, tu diseñas tu código a partir de los casos de prueba que haces: ejemplo

Mi semaforo en rojo implementa estos dos métodos:

<img width="1339" height="1057" alt="image" src="https://github.com/user-attachments/assets/5c86708a-f2b9-4ceb-821a-7be48b27ad2f" />

Me enredé porque es un tema lo de los fixtures, que te ayudan a crear mocks, después los selfs que me daban problemas, pero todo salió bien
y pasan mis dos pruebas, de ahí escribo el funcionamiento de las clases:

```python
class UsersService:
    repository: UserRepository
    def __init__(self,user_repository:UserRepository):
        self.repository = user_repository

    def crear_usuario(self,Suser: User)->UsersInsertionResult:
        userResult = UsersInsertionResult()
        ##user_repository = UserRepository()
        try:
            self.repository.crear_usuario(user)
            userResult.user_object = user
            userResult.message = "User created correctly"

            
        except:
            userResult.user_object = user
            userResult.message = "User already exists"
          ##  return userResult
        return userResult
```
El repository:

```python
from sqlmodel import Field, SQLModel,Session,create_engine
from typing import Optional
from app.models.users_model import User





class UserRepository:
    engine = create_engine("mysql+pymysql://root:211772809@localhost:3306/microspractice")
    def crear_usuario(self,user: User)->None:
        with Session(self.engine) as inserter:
            inserter.add(user)
            inserter.commit()
        

```



<img width="874" height="651" alt="image" src="https://github.com/user-attachments/assets/2a65aedd-48df-4288-99ff-658b0c52da35" />


E igual aqui dejo la clase de las pruebas:
```python
import pytest
from app.repositorites import users_repositories
from app.services.users_service import UsersService
from app.models.users_model import User
from unittest.mock import Mock

@pytest.fixture
def repository():
    return Mock()

@pytest.fixture
def service_instance(repository):
    return UsersService(repository)


def test_crear_usuario(service_instance):
    usuario_mock = User(id=None,name="An user",age=15)
    result = service_instance.crear_usuario(usuario_mock)

    ##assert result.user_object == usuario_mock
    assert result.message == "User created correctly"

def test_evitar_usuario_duplicado_test(service_instance):
    usuario_mock = User(id=None,name="An user",age=15)
    
    service_instance.repository.crear_usuario.side_effect = Exception("duplicate")
    result = service_instance.crear_usuario(usuario_mock)
    

    assert result.message == "User already exists"
    ##assert result.user_object == usuario_mock
```

Si es una metodología interesante, pero si cuesta adaptarme a ella, y catarle la idea, ya que 
por lo general escribes tus pruebas unitarias después de desarrollar, o al menos eso hacia.

La idea por lo que entiendo: 
Escribe tus ideas, en tests, con los nombres de clases, crea los assertions y whens en java
por ejemplo, escribes el código minimo, acomodas tus pruebas y en base a eso refinas, pasas
a luz verde, ahora refactoriza tu código con patrones para que quede bien....

Igual, no me convence, pero si te da cierta disciplina si tocas temas de excepciones o de los helpers
para beans en java por ejemplo.

No es una metodología que me acomode pero en un trabajo probablemente termine adoptandolo de poco
a poco.
Esta es una prueba unitaria de ejemplo, aquí ya estoy pensando en un wrapper con el objeto y un message.

Ahora hay que escribir el codigo minimo para que funcione
