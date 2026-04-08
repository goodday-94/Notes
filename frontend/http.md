
1. HTTP Methods (what they mean)

👉 Think: what action are you doing to data

| Method     | Meaning        | Example                | Use in your project |
| ---------- | -------------- | ---------------------- | ------------------- |
| **GET**    | Read data      | get dataset            | load table          |
| **POST**   | Create new     | create dataset / clone | clone dataset ✅     |
| **PUT**    | Replace/update | update dataset         | edit dataset        |
| **PATCH**  | Partial update | update one field       | change status       |
| **DELETE** | Remove         | delete dataset         | delete button       |

✅ 4. Status codes (must know)

👉 These are what your tests check

| Code                 | Meaning              | When                   |
| -------------------- | -------------------- | ---------------------- |
| **200 OK**           | success              | normal success         |
| **201 Created**      | new resource created | POST (better than 200) |
| **204 NoContent**    | success, no data     | delete                 |
| **400 BadRequest**   | invalid input        | wrong data             |
| **401 Unauthorized** | not logged in        | no token               |
| **403 Forbidden**    | no permission        | not admin              |
| **404 NotFound**     | resource missing     | id not exist           |
| **500**              | server error         | bug                    |

