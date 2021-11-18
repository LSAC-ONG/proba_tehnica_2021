# Reviews
## Descriere
Un review este scris de un user.

Entitatea `Review` este prima entitate care contine o relatie la o alta entitate existenta `User`. Aceasta relatie reprezinta o relatie One to Many (un user poate sa aiba mai multe reviewuri).

Aceste tipuri de relatii sunt implementate diferit in functie de tipul de baze de date folosit (MongoDB sau MySQL)

## Campuri
- id
    - id-ul entitatii, acesta ar trebui sa fie generat de baza de date
- message
    - Mesajul transmis prin reviewe
    - Un sir de caracter care nu depaseste 500 de caractere

- legatura intre useri si reviewuri se va face in functie de baza de date folosita
    - in MySQL se foloseste un `foreign key` in entitatea `Review` care face referinta la o entitate `User`
    - in MongoDB s-ar crea un array `reviews` in entitatea `User` care contine toate reviewurile scrise de userul respectiv

## Endpoints

- `GET /reviews` - obtinerea tuturor entitatilor
    - Raspunsul contine un array cu toate entitatile `Review` stocate in baza de date
    - Ordinea campurilor sau a entitatilor nu conteaza
    - Exemplu de raspuns:
    ```json
    [
        {
            "id": 7,
            "message": "Foarte buna idee",
            "user_id": 4
        },
        {
            "id": 8,
            "message": "Cam naspa pe aici",
            "user_id": 5
        }
    ]
    ```

- `GET /reviews/{id}` - obtinerea unei entitati specifice
    - Raspunsul contine doar entitatea care are id-ul egal cu parametrul `id`
    - Exemplu de raspuns:
    ```json
    {
        "id": 8,
        "message": "Cam naspa pe aici",
        "user_id": 5
    }
    ```
    - In caz ca nu exista entitatea cu id-ul dat se va returna un raspuns cu __Statusul 404__

- `POST /reviews` - crearea unei entitati
    - Requestul va genera o noua entitate `Review` in baza de date
    - Body-ul requestului pe care il veti primi va avea urmatoarea structura
    ```json
    {
        "message": "Am o problema",
        "user_id": 32
    }
    ```
    - In cazul in care Body-ul requestului nu este conform structurii asteptate se va intoarce un raspuns cu __Statusul 400__

- `PATCH /reviews/{id}` - updatarea unei entitati
    - Requestul va updata entitatea `Review` care are id-ul egal cu parametrul `id` dat
    - Pentru aceasta entitate dorim sa modificam doar campul `message`, asa ca body-ul requestului ar trebui sa aiba urmatoarea structura:
    ```json
    {
        "message": "Am alta parere acum"
    }
    ```
    - In caz ca nu exista entitatea cu id-ul dat se va returna un raspuns cu __Statusul 404__
    - In caz ca bodyul requestului nu respecta formatul impus se va returna un raspuns cu __Statusul 400__

- `DELETE /reviews/{id}` - stergerea unei entitati
    - Requestul va sterge entitatea `Review` care are id-ul egal cu parametrul `id` dat
    - In caz ca nu exista entitatea cu id-ul dat se va returna un raspuns cu __Statusul 404__

## De Imbunatatit

- Reviewurile pot fi create numai de useri care sunt autentificati, iar campul `user_id` va fi completat de voi cu id-ul userului care a facut requestul
- Sa se implementeze o validare care verifica ca requesturile de tip `PATCH` si `DELETE` vor putea modifica numai reviewuri care sunt legate de userul care face requestul
    - Exemplu: `reviewul 10` e detinut de `userul 1`, `reviewul 20` e detinut de `userul 2`, `userul 1` va putea face requesturi `PATCH` si `DELETE` la `reviewul 10`, dar nu le va putea face la `reviewul 20`