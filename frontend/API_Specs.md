# Endpoints

## Conventii
Statusurile raspunsurilor vor incerca sa urmeze urmatorul standard:
- __200 OK__ Requestul s-a indeplinit cu succes
- __201 CREATED__ Datele din request au fost salvate in Body
- __400 BAD REQUEST__ Datele transmise in request sunt gresite
    - campul de email nu are formatul potrivit
    - exista un typo in campul `role`, `role` poate sa fie numai `student` sau `teacher`
    - diverse probleme de format care ar trebui sa fie explicate in bodyul responseului, daca nu intelegeti ceva contactati mentorul
- __401 UNAUTHORIZED__ Nu exista tokenul de authorization in header, userul nu este autentificat sau tokenul a expirat (autentificati-va din nou)
- __403 UNAUTHORIZED__ Userul curent nu are acces la acest endpoint (De exemplu un user cu rolul `student` nu are voie sa faca un `POST /tutoring-classes`)
- __404 NOT FOUND__ Un id dat nu a putut fi gasit in baza de date
- __418__ Probleme legate de tokenul de boboc, cititi mesajul pe care il primiti in body, si daca nu rezolvati apelati la mentor

## Inregistrare si Autentificare

- `POST https://proba2021.lsacbucuresti.ro/auth/register` - request pentru inregistrarea unui user nou
    - Bodyul requestului pentru inregistrarea unui profesor:
    ```json
    {
        "email": "teacher@onmicrosoft.upb.ro", // adresa de email unui profesor trebuie sa se termine in @onmicrosoft.upb.ro
        "firstname": "Alin",
        "lastname": "Mihalache",
        "password": "teacher1", // parola trebuie sa fie de minim 8 caractere
        "confirmation_password": "teacher1", // acest camp trebuie sa fie identic cu password
        "role": "teacher"
    }
    ```
    - Bodyul requestului pentru inregistrarea unui student:
    ```json
    {
        "email": "teacher@stud.upb.ro", // adresa de email unui profesor trebuie sa se termine in @stud.upb.ro
        "firstname": "Andrei",
        "lastname": "Alinache",
        "password": "student1", // parola trebuie sa fie de minim 8 caractere
        "confirmation_password": "student1", // acest camp trebuie sa fie identic cu password
        "role": "student"
    }
    ```
    - Daca requestul nu urmeaza structura data veti primi un response cu __Statusul 400__, iar in body va aparea un mesaj de eroare
    - O adresa de email poate sa fie folosita o singura data

- `POST https://proba2021.lsacbucuresti.ro/auth/login` - request pentru autentificarea unui user nou
    - Bodyul requestului pentru autentificare unui profesor:
    ```json
    {
        "email": "teacher@onmicrosoft.ro",
        "password": "teacher1"
    }
    ```
    - Daca requestul nu urmeaza structura data veti primi un response cu __Statusul 400__, iar in body va aparea un mesaj de eroare
    - Body-ul responseului va arata in modul urmator:
    ```json
    {
        "ttl": "36000s",
        "token": "eyJhbGciO[...]FVlUW8hjdFQgVBGIK8oGw", // tokenul care trebuie sa fie introdus in Authorization header ca Bearer Token
        "role": {
            "title": "student"
        }
    }
    ```
## Reviews
Endpointurile legate de cererea reviewurilor:

- `GET https://proba2021.lsacbucuresti.ro/reviews` - request pentru obtinerea revieweurilor
    - Bodyul responseului va arata in modul urmator:
    ```json
    [
        {
            "id": 1,
            "message": "O platforma foarte buna",
            "user": {
                "firstname": "Andrei",
                "lastname": "Studentescu",
                "role": {
                    "title": "student"
                }
            }
        },
        {
            "id": 2,
            "message": "Se fac bani pe aici",
            "user": {
                "firstname": "Mihai",
                "lastname": "Profesorescu",
                "role": {
                    "title": "teacher"
                }
            }
        }
    ]
    ```

## Formularul de contact

- `POST https://proba2021.lsacbucuresti.ro/contact-requests` - request pentru stocarea unui formular de contact
    - Bodyul requestului va arata in modul urmator:
    ```json
    {
        "name": "Andrei",
        "email": "dragos@ceva.ro",
        "message": "Am o mare problema"
    }
    ```
    - Daca requestul nu urmeaza structura data veti primi un response cu __Statusul 400__, iar in body va aparea un mesaj de eroare

## Materii
- `GET https://proba2021.lsacbucuresti.ro/subjects` - request pentru obtinerea materiilor
    - Bodyul responseului va arata in modul urmator:
    ```json
    [
        {
            "id": 1,
            "title": "matematica"
        },
        {
            "id": 2,
            "title": "informatica"
        }
    ]
    ```

## Meditatii

- `GET https://proba2021.lsacbucuresti.ro/tutoring-classes` - request pentru cererea anunturilor de meditatii existente
    - Bodyul responseului va arata in modul urmator:
    ```json
    [
        {
            "id": 1,
            "description": "Vom face un curs important",
            "teacher": {
                "email": "mihai@microsoft.com",
                "firstname": "Mihai",
                "lastname": "Profesorescu"
            },
            "subject": {
                "title": "Matematici Speciale"
            }
        }
    ]
    ```

- `POST https://proba2021.lsacbucuresti.ro/tutoring-classes` - request pentru stocarea unei meditatii noi
    - Userul care trimite requestul trebuie sa aiba rolul `profesor`
    - Daca userul nu este profesor veti primi un response cu __Statusul 403__, iar in body va aparea un mesaj de eroare
    - Bodyul requestului va arata in modul urmator:
    ```json
    {
        "description": "Sunt un profesor bun, va rog veniti la mine",
        "subject_id": 2 // este id-ul unei materii alese de user (profesorul care face requestul), materiile sunt obtinute din requetul la GET /subjects
    }
    ```
    - Daca requestul nu urmeaza structura data veti primi un response cu __Statusul 400__, iar in body va aparea un mesaj de eroare

- `POST https://proba2021.lsacbucuresti.ro/tutoring-classes/{id-ul meditatiei la care vrea userul sa se inroleze}/enrol` - request pentru inscrierea unui student la o meditatie
    - Userul care trimite requestul trebuie sa aiba rolul `student`
    - Acest request nu are nevoie de body
    - Daca userul este deja inrolat veti primi un response cu __Statusul 409__ si textul `User is already enrolled`


## My (Endpointuri care se refera la userul care este acum logat)

- `GET https://proba2021.lsacbucuresti.ro/my/tutoring-classes` - request pentru cererea anunturilor de meditatii create de userul care face requestul (sectiunea "Meditatiile tale")
    - Userul care trimite requestul trebuie sa aiba rolul `profesor`
    - Bodyul responseului va arata in modul urmator:
    ```json
    [
        {
            "id": 1,
            "description": "Vei invata multe aici",
            "teacher": {
                "email": "mihai@onmicrosoft.ro",
                "firstname": "Mihai",
                "lastname": "Profesorescu"
            },
            "subject": {
                "title": "Matematici Speciale"
            }
        }
    ]
    ```

- `GET https://proba2021.lsacbucuresti.ro/my/enrolments` - request pentru cererea anunturilor de meditatii la care userul s-a inscris
    - Userul care trimite requestul trebuie sa aiba rolul `student`
    - Bodyul responseului va arata in modul urmator:
    ```json
    [
        {
            "id": 2,
            "description": "Vei invata si mai multe aici",
            "teacher": {
                "firstname": "Mihai",
                "lastname": "Profesorescu"
            },
            "subject": {
                "title": "Matematici Speciale"
            }
        }
    ]
    ```

- `DELETE https://proba2021.lsacbucuresti.ro/my/enrolments/{id-ul meditatiei la care vrea userul sa renunte}` - request pentru stergerea unei inscrieri la o meditatie
    - Userul care trimite requestul trebuie sa aiba rolul `student`
    - Requestul nu are nevoie de Body