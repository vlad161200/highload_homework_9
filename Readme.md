# Дослідження проблем паралельного доступа в базах даних

<div style="display: flex; justify-content: space-between;">

<div style="width: 48%;">

### Percona (MySQL) | Read Uncommitted | Dirty Read | Client_1"

| Команда                                                       | Послідовність операції |
|---------------------------------------------------------------|------------------------|
| **SET AUTOCOMMIT = 0;**                                       | `-`                    |
| **SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;** | `-`                    |
| **START TRANSACTION;**                                        | `1`                    |
| **SELECT * FROM users;**                                      | `4`                    |
| **SELECT * FROM users;**                                      | `6`                    |
| **COMMIT;**                                                   | `8`                    |

</div>

<div style="width: 48%;">

### Percona (MySQL) | Read Uncommitted | Dirty Read | Client_2"

| Команда                                                           | Послідовність операції |
|-------------------------------------------------------------------|------------------------|
| **SET AUTOCOMMIT = 0;**                                           | `-`                    |
| **SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;**     | `-`                    |
| **START TRANSACTION;**                                            | `2`                    |
| **SELECT * FROM users;**                                          | `3`                    |
| **UPDATE users SET birthDate = '1975-11-10' WHERE users.id = 2;** | `5`                    |
| **COMMIT;**                                                       | `7`                    |

</div>
</div>

<div style="display: flex; justify-content: space-between;">

<div style="width: 48%;">

### Percona (MySQL) | Read Committed | Lost Update | Client_1"

| Команда                                                           | Послідовність операції |
|-------------------------------------------------------------------|------------------------|
| **SET AUTOCOMMIT = 0;**                                           | `-`                    |
| **SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;**       | `-`                    |
| **START TRANSACTION;**                                            | `1`                    |
| **SELECT * FROM users;**                                          | `4`                    |
| **UPDATE users SET birthDate = '1975-11-10' WHERE users.id = 2;** | `5`                    |
| **COMMIT;**                                                       | `8`                    |

</div>

<div style="width: 48%;">

### Percona (MySQL) | Read Committed | Lost Update | Client_2"

| Команда                                                           | Послідовність операції |
|-------------------------------------------------------------------|------------------------|
| **SET AUTOCOMMIT = 0;**                                           | `-`                    |
| **SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;**     | `-`                    |
| **START TRANSACTION;**                                            | `2`                    |
| **SELECT * FROM users;**                                          | `3`                    |
| **UPDATE users SET birthDate = '1975-11-30' WHERE users.id = 2;** | `6`                    |
| **COMMIT;**                                                       | `7`                    |

</div>
</div>

<div style="display: flex; justify-content: space-between;">

<div style="width: 48%;">

### Percona (MySQL) | Read Committed | Non-Repeatable Read | Client_1"

| Команда                                                     | Послідовність операції |
|-------------------------------------------------------------|------------------------|
| **SET AUTOCOMMIT = 0;**                                     | `-`                    |
| **SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;** | `-`                    |
| **START TRANSACTION;**                                      | `1`                    |
| **SELECT * FROM users;**                                    | `3`                    |
| **SELECT * FROM users;**                                    | `7`                    |
| **COMMIT;**                                                 | `8`                    |

</div>

<div style="width: 48%;">

### Percona (MySQL) | Read Committed | Non-Repeatable Read | Client_2"

| Команда                                                           | Послідовність операції |
|-------------------------------------------------------------------|------------------------|
| **SET AUTOCOMMIT = 0;**                                           | `-`                    |
| **SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;**       | `-`                    |
| **START TRANSACTION;**                                            | `2`                    |
| **SELECT * FROM users;**                                          | `4`                    |
| **UPDATE users SET birthDate = '1975-11-30' WHERE users.id = 2;** | `5`                    |
| **COMMIT;**                                                       | `6`                    |

</div>
</div>

<div style="display: flex; justify-content: space-between;">

<div style="width: 48%;">

### Percona (MySQL) | Read Committed | Phantom Read | Client_1"

| Команда                                                     | Послідовність операції |
|-------------------------------------------------------------|------------------------|
| **SET AUTOCOMMIT = 0;**                                     | `-`                    |
| **SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;** | `-`                    |
| **START TRANSACTION;**                                      | `1`                    |
| **SELECT COUNT(*) FROM users;**                             | `3`                    |
| **SELECT COUNT(*) FROM users;**                             | `7`                    |
| **COMMIT;**                                                 | `8`                    |

</div>

<div style="width: 48%;">

### Percona (MySQL) | Read Committed | Phantom Read | Client_2"

| Команда                                                                 | Послідовність операції |
|-------------------------------------------------------------------------|------------------------|
| **SET AUTOCOMMIT = 0;**                                                 | `-`                    |
| **SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;**             | `-`                    |
| **START TRANSACTION;**                                                  | `2`                    |
| **SELECT * FROM users;**                                                | `4`                    |
| **INSERT INTO users (name, birthDate) VALUES ('Irina', '2001-08-20');** | `5`                    |
| **COMMIT;**                                                             | `6`                    |

</div>
</div>

### PostgreSQL | Read Uncommitted | Dirty Read | Client_1"

### Не вдалося повторити через те, що PostgreSQL не підтримує dirty read в Read Uncommitted

<div style="display: flex; justify-content: space-between;">

<div style="width: 48%;">

### PostgreSQL | Read Committed | Lost Update | Client_1"

| Команда                                                           | Послідовність операції |
|-------------------------------------------------------------------|------------------------|
| **SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;**             | `-`                    |
| **BEGIN;**                                                        | `1`                    |
| **SELECT * FROM users;**                                          | `4`                    |
| **UPDATE users SET birthDate = '1975-11-10' WHERE users.id = 2;** | `5`                    |
| **COMMIT;**                                                       | `8`                    |

</div>

<div style="width: 48%;">

### PostgreSQL | Read Committed | Lost Update | Client_2"

| Команда                                                           | Послідовність операції |
|-------------------------------------------------------------------|------------------------|
| **SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;**             | `-`                    |
| **BEGIN;**                                                        | `2`                    |
| **SELECT * FROM users;**                                          | `3`                    |
| **UPDATE users SET birthDate = '1975-11-30' WHERE users.id = 2;** | `6`                    |
| **COMMIT;**                                                       | `7`                    |

</div>
</div>

<div style="display: flex; justify-content: space-between;">

<div style="width: 48%;">

### PostgreSQL | Read Committed | Non-Repeatable Read | Client_1"

| Команда                                             | Послідовність операції |
|-----------------------------------------------------|------------------------|
| **SET TRANSACTION ISOLATION LEVEL READ COMMITTED;** | `-`                    |
| **BEGIN;**                                          | `1`                    |
| **SELECT * FROM users;**                            | `3`                    |
| **SELECT * FROM users;**                            | `7`                    |
| **COMMIT;**                                         | `8`                    |

</div>

<div style="width: 48%;">

### PostgreSQL | Read Committed | Non-Repeatable Read | Client_2"

| Команда                                                           | Послідовність операції |
|-------------------------------------------------------------------|------------------------|
| **SET TRANSACTION ISOLATION LEVEL READ COMMITTED;**               | `-`                    |
| **BEGIN;**                                                        | `2`                    |
| **SELECT * FROM users;**                                          | `4`                    |
| **UPDATE users SET birthDate = '1975-11-30' WHERE users.id = 2;** | `5`                    |
| **COMMIT;**                                                       | `6`                    |

</div>
</div>

<div style="display: flex; justify-content: space-between;">

<div style="width: 48%;">

### PostgreSQL | Read Committed | Phantom Read | Client_1"

| Команда                                             | Послідовність операції |
|-----------------------------------------------------|------------------------|
| **SET TRANSACTION ISOLATION LEVEL READ COMMITTED;** | `-`                    |
| **BEGIN;**                                          | `1`                    |
| **SELECT COUNT(*) FROM users;**                     | `3`                    |
| **SELECT COUNT(*) FROM users;**                     | `7`                    |
| **COMMIT;**                                         | `8`                    |

</div>

<div style="width: 48%;">

### PostgreSQL | Read Committed | Phantom Read | Client_2"

| Команда                                                                 | Послідовність операції |
|-------------------------------------------------------------------------|------------------------|
| **SET TRANSACTION ISOLATION LEVEL READ COMMITTED;**                     | `-`                    |
| **BEGIN;**                                                              | `2`                    |
| **SELECT * FROM users;**                                                | `4`                    |
| **INSERT INTO users (name, birthDate) VALUES ('Irina', '2001-08-20');** | `5`                    |
| **COMMIT;**                                                             | `6`                    |

</div>
</div>