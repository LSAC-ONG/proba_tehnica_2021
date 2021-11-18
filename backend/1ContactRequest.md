# Contact Request
## Descriere
Contact Request reprezinta o entitate care stocheaza date despre cererile de contactare pe care le primim. (Acestea ar putea sa fie trimise prin cadrul formularelor de contact de pe siteurile noastre).

## Campuri
- id
    - id-ul entitatii, acesta ar trebui sa fie generat de baza de date
- name
    - Numele persoanei care va face cerearea
    - Un sir de caracter care nu depaseste 50 de caractere
- email
    - Emailul persoanei care face cererea
    - Un sir de caractere care nu depaseste 50 de caractere
    - Trebuie sa se verifice ca sirul de caractere reprezinta o adresa de email (se va verifica ca este de format xxx@xxx.xxx, numarul de x-uri este inamplator)
        - Hint: regex sau validator libraries
- message
    - Mesajul transmis din cadrul cererii
    - Un sir de caracter care nu depaseste 5000 de caractere
- is_resolved
    - Statusul cererii de contact, daca problema a fost remediata sau nu
    - Boolean
    - Valoarea default a acetui camp este __false__ si acest camp nu ar trebuie sa fie fie prezent in requestul `POST`


## Endpoints

- `GET /contact-requests` - obtinerea tuturor entitatilor
    - Raspunsul contine un array cu toate entitatile Contact Request stocate in baza de date
    - Ordinea campurilor sau a entitatilor nu conteaza
    - Exemplu de raspuns:
    ```json
    [
        {
            "id": 7,
            "message": "Am o mica problema",
            "name": "Mihai",
            "email": "dragos@dragos.com",
            "is_resolved": false
        },
        {
            "id": 8,
            "message": "Am o mare problema",
            "name": "Andrei",
            "email": "andrei@gmail.ro",
            "is_resolved": false
        }
    ]
    ```

- `GET /contact-requests/{id}` - obtinerea unei entitati specifice
    - Raspunsul contine doar entitatea care are id-ul egal cu parametrul `id`
    - Exemplu de request: `GET /contact-request/7` va returna entitatea Contact Request cu id-ul 7
    - Exemplu de raspuns:
    ```json
    {
        "id": 7,
        "message": "Am o mica problema",
        "name": "Mihai",
        "email": "dragos@dragos.com",
        "is_resolved": false
    }
    ```
    - In caz ca nu exista entitatea cu id-ul dat se va returna un raspuns cu __Statusul 404__

- `POST /contact-requests` - crearea unei entitati
    - Requestul va genera o noua entitate Contact Request in baza de date
    - Body-ul requestului pe care il veti primi va avea urmatoarea structura
    ```json
    {
        "name": "Mihai",
        "message": "Am o problema",
        "email": "mihai@gmail.com"
    }
    ```
    - In cazul in care Body-ul requestului nu este conform structurii asteptate se va intoarce un raspuns cu __Statusul 400__
    - Exemplu de body care ar returna raspuns cu __Statusul 400__
    ```json
    {
        "name": "Mihai",
        "message": 1,
        "email": "mihai@gmail.com"
    }
    ```

- `PATCH /contact-requests/{id}` - updatarea unei entitati
    - Requestul va updata entitatea `Contact Request` care are id-ul egal cu parametrul `id` dat
    - Pentru aceasta entitate dorim sa modificam doar campul `is_resolved`, asa ca body-ul requestului ar trebui sa aiba urmatoarea structura:
    ```json
    {
        "is_resolved": true
    }
    ```
    - In caz ca nu exista entitatea cu id-ul dat se va returna un raspuns cu __Statusul 404__
    - In caz ca bodyul requestului are campuri aditionale (de exemplu: `message` sau `name`) acestea ar trebui sa fie ignorate
    - In caz ca campul `is_resolved` nu este o valoare `boolean` se va returna un raspuns cu __Statusul 400__

- `DELETE /contact-requests/{id}` - stergerea unei entitati
    - Requestul va sterge entitatea `Contact Request` care are id-ul egal cu parametrul `id` dat
    - In caz ca nu exista entitatea cu id-ul dat se va returna un raspuns cu __Statusul 404__

## BONUS

- Cand se primeste un request de tipul `POST /contact-requests` se va trimite un email cu datele primite (Numele si adresa de email a persoanei care face cereare si mesajul acesteia) la o adresa de email aleasa de voi
- Se va implementa un sistem de sortare. Requestul `GET /contact-requests` poate sa primeasca query parameterii: `sortBy`, care va avea valoarea unui camp al entitatii, si `order` care va putea avea valoarea `ASC` pentru ascendent sau `DESC` pentru descendent
    - Excemplu de request: `GET /contact-requests?sortBy=email&order=ASC` va returna toate entitatile `Contact Request` ordonate crescator dupa `email`.
- Se va implementa un sistem de filtrare. Se va folosi query parameterul `filterBy` care va avea ca valoare text in format json care are structura: `"camp_entitate1":"valoare1"`
    - Exemplu de request: `GET /contact-requests?filterBy={"email":"dragos@yahoo.com","is_verified":false}` va returna toate entitatile `Contact Request` care au campul `email` egal cu `dragos@yahoo.com` si campul `is_verified` egal cu `false`