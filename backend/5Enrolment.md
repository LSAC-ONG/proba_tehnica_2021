# Enrolment
## Descriere
Entitatea `Enrolment` reprezinta inscrierea unui student (user care are rolul de student) la o meditatie.

Entitatea descrie o relatie Many-to-Many (M:M) intre entitatea `Tutoring Class` si `User` (Un user poate sa se inscrie la mai multe meditatii, iar o meditatie poate sa aiba mai multi studenti)

In MySQL relatia M:M este realizata printr-un Join/Junction/Association Table care contine un `tutoring_class_id` si un `user_id`, astfel, fiecare entitate `Enrolment` definind o inscriere a unui user la o meditatie.

In MongoDB ati putea avea cate un array pe fiecare parte a relatiei
De ex:
    - entitatea `Tutoring Class` are un array `users` care contine id-urile userilor care s-au inscris la aceasta meditatie
    - entitatea `User` are un array `tutorin_classes` care contine id-urile meditatiilor la care s-a inscris userul

## Endpoints

- `POST /tutoring-class/{id}/enroll` - inscrierea la o meditatie
    - Userul care face acest request trebuie sa aiba rolul `student`
    - Daca userul nu este autentificat se va returna __Statusul 401__
    - Daca userul nu are rolul `student` se va returna __Statusul 403__
    - Body-ul requestului va fi gol
    - In caz ca nu exista entitatea `Tutoring Class` cu id-ul dat ca parametru se va intoarce un raspuns cu __Statusul 400__
