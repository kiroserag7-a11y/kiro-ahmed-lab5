# Lab 5 - Complex Queries in SQL (Problem 2: Library)

## Team
| Name | ID | Did what |
|------|----|----------|
| Kirollose Serag Mouner ellyas | 2304102. | `problem2_DDL.sql`, `problem2_DML.sql`, SQL queries (`problem2_Queries.sql`) |
| Ahmed Ramdan Mohamed kahrout | 2304109. | website pages F2.1-F2.6, README |

## Contents
| File | What it is |
|------|------------|
| `problem2_DDL.sql` | Database `library`: the 6 tables of Lab 4 (same foreign keys and `ON DELETE CASCADE` rules) + the new table `review` (`CHECK` rating 1-5, `UNIQUE (book_id, member_id)`, `ON DELETE CASCADE` from book) + the bonus view `book_details` |
| `problem2_DML.sql` | Seed data: the rows of Lab 4 (5 authors, 3 publishers, 3 categories, 10 books, 5 members, 12 loans) + 10 reviews |
| `problem2_Queries.sql` | The SQL of the new pages F2.1-F2.6, each query has a `-- F2.x` comment |
| `website/` | The website of Lab 4 (login + to-do + search pages) with the new Lab 5 pages. The new pages read their SQL from `problem2_Queries.sql` |

## Technology
Python 3 + Flask, PyMySQL (plain SQL, no ORM), bcrypt, HTML/Jinja2, CSS, JavaScript, MySQL **8.0.31 or later** (needed for `INTERSECT` and `EXCEPT`; check with `mysql -u root -p -e "SELECT VERSION();"`).

## How to run (from an empty machine)
1. Install Python 3.10+ and MySQL 8.0.31+.
2. Build the databases (run from this folder):
   ```bash
   mysql -u root -p < website/database/schema.sql   # login database: registration
   mysql -u root -p < problem2_DDL.sql              # library database + 7 tables + view book_details
   mysql -u root -p < problem2_DML.sql              # seed data
   mysql -u root -p library < problem2_Queries.sql  # (optional) run all page queries by hand
   ```
3. Set up the website:
   ```bash
   cd website
   pip install -r requirements.txt
   cp .env.example .env      # on Windows: copy .env.example .env
   ```
   Open `.env` and write your MySQL password and any long random `SECRET_KEY`.
4. Start the server and open <http://localhost:5000>:
   ```bash
   python app.py
   ```
5. Register or log in. The top bar links to all the pages.

## New pages (Lab 5)
| Page | Address | SQL features |
|------|---------|--------------|
| F2.1 Above the average | `/above-average` | scalar subquery, view `book_details` |
| F2.2 Never borrowed | `/never-borrowed` | `NOT EXISTS` (+ the same result with the `LEFT JOIN` of Lab 4 as a check) |
| F2.3 Best of the category | `/best-of-category` | correlated subquery, view `book_details` |
| F2.4 Active members | `/active-members` | derived table in `FROM`, `EXISTS` |
| F2.5 Reviews | `/reviews` | `LEFT JOIN`, `CASE`, `COALESCE`, `HAVING` with a subquery |
| F2.6 Countries and people | `/countries-people` | `UNION`, `INTERSECT`, `EXCEPT` |

The pages of Lab 4 (`/search`, `/catalogue`, `/reports`, `/loans`, `/top`, `/authors-books`) are still there. All pages need a logged-in user.

## Bonus
**View `book_details`** (created in `problem2_DDL.sql`): the join of BOOK, AUTHOR, PUBLISHER and CATEGORY from the catalogue page of Lab 4. It appears in every part of the project:

| File | Where the bonus is |
|------|--------------------|
| `problem2_DDL.sql` | `CREATE VIEW book_details` |
| `problem2_DML.sql` | comment: the view shows the inserted books with their author, publisher and category |
| `problem2_Queries.sql` | **F2.1** and **F2.3 (a), (b)** select from `book_details` instead of repeating the joins |
| `website/app.py` | the pages F2.1 and F2.3 (through `problem2_Queries.sql`) and the Lab 4 catalogue page `/catalogue` read the view |
| `README.md` | this section |

The Lab 4 bonus is still in place: the search form (`/search`) uses placeholders (`%s`) so the typed values are never glued into the SQL text (see `search()` in `website/app.py`).

## What the seed data contains
| Case required by the lab | Rows |
|--------------------------|------|
| Book above the average price (13.75) | books 1, 3, 4, 6, 10 |
| Book without review | books 4, 7, 10 |
| Book with at least 2 reviews of different ratings | books 1 (5, 4), 5 (5, 4), 8 (4, 3) |
| Book whose average rating is above the average of all ratings (3.70) | books 1, 5, 3 |
| Country with an author and no publisher | Japan |
| Category with books of different prices | Fiction (9.99 to 16.75) |
| Member with more loans than the average (2.4) | members 1 and 2 |
