# Tutoring Class
## Descriere
Entitatea `Tutoring Class` reprezinta o meditatie. Aceasta este legate de un profesor (entitate `User` care are rolul `teacher`)

## Campuri
- id
    - id-ul entitatii, acesta ar trebui sa fie generat de baza de date
- description
    - Descrierea meditatiei
    - Un sir de caracter care nu depaseste 500 de caractere
- subject
    - Materia la care este meditatia
    - Un sir de caracter care nu depaseste 80 de caractere

- legatura de la entitatea `Tutoring Class` la entitatea `User`


## Endpoints

- `GET /tutoring-classes` - obtinerea tuturor entitatilor
    - Raspunsul contine un array cu toate entitatile `Tutoring Class` stocate in baza de date
    - Ordinea campurilor sau a entitatilor nu conteaza
    - Exemplu de raspuns:
    ```json
    [
        {
            "id": 7,
            "description": "Curs interesant",
            "teacher_id": 1, // teacher_id reprezinta id-ul unui user
            "subject": "Matematica"
        },
        {
            "id": 8,
            "description": "Curs foarte important",
            "teacher_id": 4, // teacher_id reprezinta id-ul unui user
            "subject": "Fizica"
        }
    ]
    ```

- `GET /tutoring-classes/{id}` - obtinerea unei entitati specifice
    - Raspunsul contine doar entitatea care are id-ul egal cu parametrul `id`
    - Exemplu de raspuns:
    ```json
    {
        "id": 8,
        "description": "Curs foarte important",
        "teacher_id": 4, // teacher_id reprezinta id-ul unui user
        "subject": "Fizica"
    }
    ```
    - In caz ca nu exista entitatea cu id-ul dat se va returna un raspuns cu __Statusul 404__

- `POST /tutoring-classes` - crearea unei entitati
    - Requestul va genera o noua entitate `Tutoring Class` in baza de date
    - Numai userii care au rol `teacher` pot sa foloseasca acest endpoint
    - In cazul in care un user cu alt rol acceseaza acest endpoint va primi un raspuns cu __Statusul 403__
    - Body-ul requestului pe care il veti primi va avea urmatoarea structura
    ```json
    {
        "description": "Te voi rezolva",
        "subject": "Franceza"
    }
    ```
    - Campul `teacher_id` va fi completat de voi cu id-ul userului care a facut acest request
    - In cazul in care Body-ul requestului nu este conform structurii asteptate se va intoarce un raspuns cu __Statusul 400__

- `PATCH /tutoring-classes/{id}` - updatarea unei entitati
    - Requestul va updata entitatea `Tutoring Class` care are id-ul egal cu parametrul `id` dat
    - Pentru aceasta entitate dorim sa modificam doar campul `description`, asa ca body-ul requestului ar trebui sa aiba urmatoarea structura:
    ```json
    {
        "description": "O lectie si mai interesanta"
    }
    ```
    - In caz ca nu exista entitatea cu id-ul dat se va returna un raspuns cu __Statusul 404__
    - In caz ca bodyul requestului nu respecta formatul impus se va returna un raspuns cu __Statusul 400__

- `DELETE /tutoring-classes/{id}` - stergerea unei entitati
    - Requestul va sterge entitatea `Tutoring Class` care are id-ul egal cu parametrul `id` dat
    - In caz ca nu exista entitatea cu id-ul dat se va returna un raspuns cu __Statusul 404__

## BONUS

- Sa se implementeze o validare care verifica ca requesturile de tip `PATCH` si `DELETE` vor putea modifica numai meditatii care sunt legate de userul care face requestul
    - Exemplu: `meditatia 10` e detinuta de `userul 1`, `meditatia 20` e detinuta de `userul 2`, `userul 1` va putea face requesturi `PATCH` si `DELETE` la `meditatia 10`, dar nu le va putea face la `meditatia 20`

- Se va implementa un sistem de filtrare. Se va folosi query parameterul `subject` care va avea ca valoare numele unei materii
    - Exemplu de request: `GET /tutoring-classes?subject=matematica` va returna toate entitatile `Tutoring Class` care au campul `subject` egal cu `matematica`
