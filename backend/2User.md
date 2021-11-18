# Users
## Descriere
Userii sunt persoanele care isi fac cont pe siteurile noastre. In cazul acestui proiect userii pot sa fie Studenti sau Profesori, fiecare tip de useri avand anumite permisiuni.

## Campuri
- id
    - id-ul entitatii, acesta ar trebui sa fie generat de baza de date
- lastname
    - Numele de familie a userului
    - Un sir de caracter care nu depaseste 50 de caractere
- firstname
    - Prenumele userului
    - Un sir de caracter care nu depaseste 50 de caractere
- email
    - Emailul persoanei care face cererea
    - Un sir de caractere care nu depaseste 50 de caractere
    - Trebuie sa se verifice ca sirul de caractere reprezinta o adresa de email (se va verifica ca este de format xxx@xxx.xxx, numarul de x-uri este inamplator)
    - Trebuie sa se verifice ca mailul unui student trebuie sa se termina in `@stud.upb.ro`, iar mailul unui profesor se termina in `@onmicrosof.upb.ro`
        - Hint: regex sau validator libraries
    - Adresa de email ar trebui sa fie unica in cadrul entitatilor `User`
- password
    - Parola userului
    - Un sir de caracter care nu depaseste 50 de caractere
- role
    - Rolul userului, rolul poate fi `teacher` sau `student`

## Endpoints

- `POST /auth/register` - crearea unei user
    - Requestul va genera un nou user in baza de date in functie de campul `title` primit in body-ul requestului
        - `title` poate avea doar valorile `student` sau `teacher`
    - Body-ul requestului pe care il veti primi va avea urmatoarea structura
    ```json
    {
        "email": "teacher@onmicrosoft.upb.ro",
        "firstname": "Mihai",
        "lastname": "Popescu",
        "password": "teacher1",
        "confirmation_password": "teacher1",
        "role": "teacher"
    }
    ```
    - Campul `confirmation_password` nu va fi stocat in baza de date, el este acolo doar pentru a verifica ca userul si-a introdus parola corect
    - In cazul in care Body-ul requestului nu este conform structurii asteptate se va intoarce un raspuns cu __Statusul 400__

- `POST /auth/login` - autentificarea unui user
    - Requestul va returna un token sau un cookie cu care userul se poate autentifica
    - Body-ul requestului pe care il veti primi va avea urmatoarea structura
    ```json
    {
        "email": "teacher@onmicrosoft.upb.ro",
        "password": "teacher1"
    }
    ```
    - In cazul in care Body-ul requestului nu este conform structurii asteptate se va intoarce un raspuns cu __Statusul 400__
    - In cazul in care credentialele (combinatia de email si parola) oferite nu se potrivesc cu credentialele niciunui user se va return un raspuns __Statusul 401__

- `GET /users` - obtinerea tuturor entitatilor
    - Raspunsul contine un array cu toate entitatile `User` stocate in baza de date
    - Ordinea campurilor sau a entitatilor nu conteaza
    - Exemplu de raspuns:
    ```json
    [
        {
            "id": 1,
            "email": "teacher@onmicrosoft.upb.ro",
            "firstname": "Mihai",
            "lastname": "Popescu",
            "role": ... // rolurile pot fi implementate in mai multe moduri (care ar modifica structura responseului), din acest motiv nu am scris nicio valoare
        },
        {
            "id": 2,
            "email": "student@stud.upb.ro",
            "firstname": "Andrei",
            "lastname": "Mira",
            "role": ...
        }
    ]
    ```
    - Atentie la faptul ca parola nu este returnata in raspuns
    - Campul de role nu apare pentru ca este decizia voastra cum vreti sa il implementati

- `GET /users/{id}` - obtinerea unei entitati specifice
    - Raspunsul contine doar entitatea care are id-ul egal cu parametrul `id`
    - Exemplu de raspuns:
    ```json
    {
        "id": 2,
        "email": "student@stud.upb.ro",
        "firstname": "Andrei",
        "lastname": "Mira",
        "role": ...
    }
    ```
    - In caz ca nu exista entitatea cu id-ul dat se va returna un raspuns cu __Statusul 404__

- `PATCH /users/{id}` - updatarea unei entitati
    - Requestul va updata entitatea `User` care are id-ul egal cu parametrul `id` dat
    - Pentru updatare vrem sa introducem in body doar campurile care vrem sa le modificam, asta inseamna ca toate campurile din body sunt optionale
        - Exceptie: daca exista campul `password` atunci campul `confirmation_password` trebuie sa existe si el si sa fie identic cu campul `password`
    - Exemple de bodyuri valide:
    ```json
    {
        "email": "altul@stud.upb.ro",
        "password": "teacher1",
        "confirmation_password": "teacher1"
    }
    ```
    ```json
    {
        "firstname": "AltNume",
        "lastname": "Salut",
    }
    ```
    - In caz ca nu exista entitatea cu id-ul dat se va returna un raspuns cu __Statusul 404__
    - In caz ca bodyul nu urmareste structura definita se va returna un raspuns cu __Statusul 400__

- `DELETE /users/{id}` - stergerea unei entitati
    - Requestul va sterge entitatea `Contact Request` care are id-ul egal cu parametrul `id` dat
    - In caz ca nu exista entitatea cu id-ul dat se va returna un raspuns cu __Statusul 404__

## De Imbunatatit

- Parola ar trebui sa aiba minim 8 caractere
- Parola nu va fi stocata in plain-text in baza de date
    - Hint: Hash si Salt / Bcrypt
- Sa se implementeze o validare care verifica ca requesturile de tip `PATCH` si `DELETE` vor putea modifica numai date legate de userul care face requestul
    - Exemplu: userul cu id-ul 7 poate sa faca requesturi doar la `PATCH /users/7` sau `DELETE /users/7`, dar un request `PATCH /users/8` ar returna un raspuns cu __Statusul 403__, iar updatarea/stergerea nu va fi executata
