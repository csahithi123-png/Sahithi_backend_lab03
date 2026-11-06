# BDF Lab 3 - REST API, Query Parameters, Validation & Debugging

**Student Name:** C. SAHITHI  
**Registration Number:** 252U1R1062  
**Section:** CSE-2A  

**Live Deployed URL (Render):** [https://sahithi-backend-lab03.onrender.com](https://sahithi-backend-lab03.onrender.com)  
**GitHub Repository:** [https://github.com/csahithi123-png/Sahithi_backend_lab03](https://github.com/csahithi123-png/Sahithi_backend_lab03)  

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/csahithi123-png/Sahithi_backend_lab03)

A complete Node.js + Express + SQLite implementation of Lab 3, continued from the Lab 2 `book` table approach.

---

## Live Deployment Links (Render)

| Task / Feature | Method | Live Deployed URL |
|---|---|---|
| **API Health & Endpoints** | `GET` | [https://sahithi-backend-lab03.onrender.com/](https://sahithi-backend-lab03.onrender.com/) |
| **Task 1: List all books** | `GET` | [https://sahithi-backend-lab03.onrender.com/books](https://sahithi-backend-lab03.onrender.com/books) |
| **Task 1: Get single book** | `GET` | [https://sahithi-backend-lab03.onrender.com/books/1](https://sahithi-backend-lab03.onrender.com/books/1) |
| **Task 1: Create book** | `POST` | `https://sahithi-backend-lab03.onrender.com/books` |
| **Task 1: Update book** | `PUT` | `https://sahithi-backend-lab03.onrender.com/books/1` |
| **Task 1: Delete book** | `DELETE` | `https://sahithi-backend-lab03.onrender.com/books/1` |
| **Task 2: Title search (`search_q`)** | `GET` | [https://sahithi-backend-lab03.onrender.com/books?search_q=the](https://sahithi-backend-lab03.onrender.com/books?search_q=the) |
| **Task 2: Sort by price DESC** | `GET` | [https://sahithi-backend-lab03.onrender.com/books?order_by=price&order=DESC](https://sahithi-backend-lab03.onrender.com/books?order_by=price&order=DESC) |
| **Task 2: Pagination (limit & offset)** | `GET` | [https://sahithi-backend-lab03.onrender.com/books?limit=5&offset=0](https://sahithi-backend-lab03.onrender.com/books?limit=5&offset=0) |
| **Task 2: Pagination (page 2)** | `GET` | [https://sahithi-backend-lab03.onrender.com/books?limit=5&offset=5](https://sahithi-backend-lab03.onrender.com/books?limit=5&offset=5) |
| **Task 3: Price range filter** | `GET` | [https://sahithi-backend-lab03.onrender.com/books?min_price=200&max_price=500](https://sahithi-backend-lab03.onrender.com/books?min_price=200&max_price=500) |
| **Task 3: Combined Search & Filter** | `GET` | [https://sahithi-backend-lab03.onrender.com/books?search_q=the&order_by=price&order=DESC&min_price=200&max_price=600&limit=5&offset=0](https://sahithi-backend-lab03.onrender.com/books?search_q=the&order_by=price&order=DESC&min_price=200&max_price=600&limit=5&offset=0) |
| **Task 5: Missing book (404 error)** | `GET` | [https://sahithi-backend-lab03.onrender.com/books/99999](https://sahithi-backend-lab03.onrender.com/books/99999) |
| **Task 6: Invalid sort (400 error)** | `GET` | [https://sahithi-backend-lab03.onrender.com/books?order_by=invalid](https://sahithi-backend-lab03.onrender.com/books?order_by=invalid) |
| **Task 6: Invalid price range (400)** | `GET` | [https://sahithi-backend-lab03.onrender.com/books?min_price=500&max_price=100](https://sahithi-backend-lab03.onrender.com/books?min_price=500&max_price=100) |

## Requirements covered

- Resource-based noun endpoint: `/books`; HTTP methods represent actions.
- `GET /books` supports `search_q`, `order_by`, `order`, `limit`, and `offset`.
- Defaults: `search_q=""`, `order_by=id`, `order=ASC`, `limit=10`, `offset=0`.
- Partial title search uses SQL `LIKE`.
- Price range supports `min_price` and `max_price` (inclusive conditions equivalent to `BETWEEN`).
- POST and PUT validate title, author and numeric price.
- Status codes: 201 create; 200 read/update/delete; 400 invalid input; 404 missing book.
- Consistent JSON error format: `{ "message": "..." }`.
- SQL values use parameterized queries. Sort column and direction are validated against allowlists.
- Database initialization and database reads/writes are awaited.
- The `data` directory is created automatically at startup to prevent SQLite `SQLITE_CANTOPEN` errors.

## Setup (Windows CMD / VS Code terminal)

1. Extract the ZIP.
2. Open the extracted `lab-03-rest-api` folder in VS Code.
3. Install dependencies:
   ```bash
   npm install
   ```
4. Start the server:
   ```bash
   npm start
   ```
   For development with auto-restart:
   ```bash
   npm run dev
   ```
5. Open `http://localhost:3000/`. The SQLite database `data/books.db` is created automatically and seeded with 10 sample books on the first run.

If port 3000 is busy, run:
```cmd
set PORT=3001 && npm start
```
Then use `http://localhost:3001`.

## API endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/books` | List, search, sort, filter and paginate |
| GET | `/books/:id` | Get one book |
| POST | `/books` | Create a book |
| PUT | `/books/:id` | Update a book |
| DELETE | `/books/:id` | Delete a book |

### GET query parameters

| Parameter | Default | Example |
|---|---|---|
| `search_q` | empty | `?search_q=code` |
| `order_by` | `id` | `?order_by=price` |
| `order` | `ASC` | `?order=DESC` |
| `limit` | `10` | `?limit=5` |
| `offset` | `0` | `?offset=5` |
| `min_price` | none | `?min_price=200` |
| `max_price` | none | `?max_price=500` |

Combined example:
`GET http://localhost:3000/books?search_q=the&order_by=price&order=DESC&min_price=200&max_price=600&limit=5&offset=0`

Allowed `order_by`: `id`, `title`, `author`, `price`. Allowed `order`: `ASC`, `DESC`. `limit` must be 1–100; `offset` must be a non-negative integer.

## Postman testing

Import `postman/Lab-03-Books-API.postman_collection.json` into Postman. Set `baseUrl` to `http://localhost:3000` if needed. Run requests in the collection. The collection includes successful and failure cases for list/search/filter, CRUD, validation, invalid sort/pagination, and missing IDs.

### Task Screenshots & Live URLs

#### 1. Name the endpoints as nouns (`/books`) and use the HTTP method to show the action:
- **Live Root Endpoint:** [https://sahithi-backend-lab03.onrender.com/](https://sahithi-backend-lab03.onrender.com/)
- **Live List Books (`GET /books`):** [https://sahithi-backend-lab03.onrender.com/books](https://sahithi-backend-lab03.onrender.com/books)
- **Live Single Book (`GET /books/:id`):** [https://sahithi-backend-lab03.onrender.com/books/1](https://sahithi-backend-lab03.onrender.com/books/1)
- **Live Create (`POST /books`):** `https://sahithi-backend-lab03.onrender.com/books`
- **Live Update (`PUT /books/:id`):** `https://sahithi-backend-lab03.onrender.com/books/1`
- **Live Delete (`DELETE /books/:id`):** `https://sahithi-backend-lab03.onrender.com/books/1`

![alt text](image.png)
![alt text](image-1.png)

#### 2. Add query parameters to `GET /books/`: `search_q` (partial title match with `LIKE`), `order_by`, `order` (ASC/DESC), `limit` and `offset`, each with a default value:
- **Live Partial Title Search (`?search_q=the`):** [https://sahithi-backend-lab03.onrender.com/books?search_q=the](https://sahithi-backend-lab03.onrender.com/books?search_q=the)
- **Live Sort by Price (`?order_by=price&order=DESC`):** [https://sahithi-backend-lab03.onrender.com/books?order_by=price&order=DESC](https://sahithi-backend-lab03.onrender.com/books?order_by=price&order=DESC)
- **Live Pagination (`?limit=5&offset=0`):** [https://sahithi-backend-lab03.onrender.com/books?limit=5&offset=0](https://sahithi-backend-lab03.onrender.com/books?limit=5&offset=0)
- **Live Pagination Page 2 (`?limit=5&offset=5`):** [https://sahithi-backend-lab03.onrender.com/books?limit=5&offset=5](https://sahithi-backend-lab03.onrender.com/books?limit=5&offset=5)

![alt text](image-2.png)

#### 3. Add a price-range filter, for example `min_price` and `max_price` using `BETWEEN` or two conditions:
- **Live Price Range ($200 to $500):** [https://sahithi-backend-lab03.onrender.com/books?min_price=200&max_price=500](https://sahithi-backend-lab03.onrender.com/books?min_price=200&max_price=500)
- **Live Combined Query:** [https://sahithi-backend-lab03.onrender.com/books?search_q=the&order_by=price&order=DESC&min_price=200&max_price=600&limit=5&offset=0](https://sahithi-backend-lab03.onrender.com/books?search_q=the&order_by=price&order=DESC&min_price=200&max_price=600&limit=5&offset=0)

![alt text](image-3.png)
![alt text](image-4.png)

#### 4. Validate the POST body: reject missing title, author or a non-numeric price with 400 and a clear JSON message:
- **Target URL:** `POST https://sahithi-backend-lab03.onrender.com/books`
- **Missing Title (400 Bad Request):** `{ "author": "C. SAHITHI", "price": 399 }`
- **Invalid Price (400 Bad Request):** `{ "title": "Node.js Guide", "author": "C. SAHITHI", "price": "invalid" }`

![alt text](image-5.png)
![alt text](image-6.png)

#### 5. Return 201 after create, 200 after update and delete, and 404 when the id does not exist:
- **Single Book (200 OK):** [https://sahithi-backend-lab03.onrender.com/books/1](https://sahithi-backend-lab03.onrender.com/books/1)
- **Missing Book ID (404 Not Found):** [https://sahithi-backend-lab03.onrender.com/books/99999](https://sahithi-backend-lab03.onrender.com/books/99999)
- **Create Book (201 Created):** `POST https://sahithi-backend-lab03.onrender.com/books`
- **Update Book (200 OK):** `PUT https://sahithi-backend-lab03.onrender.com/books/1`
- **Delete Book (200 OK):** `DELETE https://sahithi-backend-lab03.onrender.com/books/10`

![alt text](image-7.png)
![alt text](image-8.png)
![alt text](image-9.png)

#### 6. Make the response format consistent, for example `{ "message": "..." }` for errors:
- **Live Invalid Sort Column (400 Bad Request):** [https://sahithi-backend-lab03.onrender.com/books?order_by=unknown](https://sahithi-backend-lab03.onrender.com/books?order_by=unknown)
- **Live Inverted Range Error (400 Bad Request):** [https://sahithi-backend-lab03.onrender.com/books?min_price=500&max_price=100](https://sahithi-backend-lab03.onrender.com/books?min_price=500&max_price=100)

![alt text](image-10.png)

#### 7. Debugging exercise: the faculty hands out a broken version of the project. Students fix a port conflict (EADDRINUSE), a wrong import path, a table-name typo (no such table) and a missing await:
 
For create method:
![alt text](image-11.png)

For update and delete:
![alt text](image-12.png)

For missing ID:
![alt text](image-13.png)

### Sample request body
```json
{
  "title": "Learning Node.js",
  "author": "C. SAHITHI",
  "price": 399
}
```

### Example responses

Successful create (201):
```json
{
  "message": "Book created successfully",
  "data": {
    "id": 11,
    "title": "Learning Node.js",
    "author": "C. SAHITHI",
    "price": 399
  }
}
```

Validation failure (400):
```json
{ "message": "title is required and must be a non-empty string" }
```

Missing record (404):
```json
{ "message": "Book not found" }
```

## Debugging exercise - issue and resolution

1. **EADDRINUSE (port already in use):** Another process is using the selected port. Stop the old Node process (Ctrl+C in its terminal) or start with another port using `set PORT=3001 && npm start`.
2. **Wrong import path:** Check the relative path and filename. The route imports `../database` from `src/routes/books.js`, which correctly resolves to `src/database.js`.
3. **No such table:** Ensure the table name is exactly `book` (singular) in both table creation and SQL queries. The database setup runs before the server starts.
4. **Missing await:** Await asynchronous database operations such as `db.get`, `db.all`, `db.run`, and `openDatabase()` before using their results.

## Viva preparation

- **Query parameter vs path parameter:** Query parameters follow `?` and are commonly used for filtering/sorting/pagination (for example, `/books?limit=5`). A path parameter is part of the URL path and identifies a resource (for example, `/books/3`).
- **Stateless:** Each HTTP request contains the information needed to process it; the server does not depend on stored client session state from a previous request.
- **Bad input vs missing record:** `400 Bad Request` for invalid input; `404 Not Found` when the requested resource does not exist.

## Submission notes

- Do not commit `.env` or `node_modules`.
- `node_modules` is intentionally excluded from this ZIP; run `npm install`.
- `data/books.db` is generated automatically at first launch.
- Capture your own Postman screenshots after running the API if your faculty requires visual testing evidence.
