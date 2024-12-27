* * * * *

### **1\. Zarządzanie bazą danych**

-   **Architektura SQL Server**: szczegóły dotyczące silnika bazy danych (relacyjnego i pamięciowego).
-   **Indeksy**:
    -   Rodzaje indeksów: clustered, non-clustered, full-text, columnstore, filtered.
    -   Strategie indeksowania (np. covering indexes).
    -   Analiza fragmentacji i reorganizacja/rebuild indeksów.
-   **Filegroups i zarządzanie plikami bazy danych**:
    -   Ustawienia plików (PRIMARY, log).
    -   Migracja danych między filegroups.
-   **Wydajność**:
    -   Plany wykonania zapytań (Query Execution Plans).
    -   Optymalizacja kosztów zapytań.
    -   Parametr sniffing i jak go unikać.
    -   TempDB i jej wpływ na wydajność.

* * * * *

### **2\. Zaawansowane zapytania SQL**

-   **Podzapytania (Subqueries)**:
    -   Korzystanie z podzapytań skorelowanych i nieskorelowanych.
-   **CTE (Common Table Expressions)**:
    -   Rekurencyjne CTE (np. hierarchie danych).
-   **Pivot i Unpivot**:
    -   Przekształcanie danych kolumn na wiersze i odwrotnie.
-   **Dynamic SQL**:
    -   Generowanie dynamicznych instrukcji SQL.
    -   Bezpieczeństwo przy użyciu dynamic SQL (np. unikanie SQL Injection).
-   **Operacje na dużych zbiorach danych**:
    -   BULK INSERT, OPENROWSET.
    -   Funkcje okienkowe (window functions): ROW_NUMBER, RANK, LEAD, LAG.
-   **Hierarchiczne zapytania**:
    -   Obsługa hierarchii danych za pomocą rekursji lub funkcji hierarchicznych.

* * * * *

### **3\. Programowanie w SQL**

-   **Funkcje użytkownika**:
    -   Scalar vs. table-valued functions.
    -   Deterministic i non-deterministic functions.
-   **Procedury składowane (Stored Procedures)**:
    -   Parametry wejściowe i wyjściowe.
    -   Obsługa błędów (TRY...CATCH).
    -   Automatyzacja zadań.
-   **Transakcje**:
    -   ACID w SQL Server.
    -   Zarządzanie transakcjami: COMMIT, ROLLBACK, SAVEPOINT.
-   **Kursory**:
    -   Rodzaje kursorów i ich wpływ na wydajność.
    -   Alternatywy dla kursorów (np. operacje set-based).
-   **Tabele tymczasowe**:
    -   Tymczasowe (`#`) i globalne tymczasowe tabele (`##`).
    -   Różnice między tabelami tymczasowymi a zmiennymi tabelarycznymi (`@`).

* * * * *

### **4\. Mechanizmy bezpieczeństwa**

-   **Autoryzacja i role**:
    -   Tworzenie ról użytkownika i przypisywanie uprawnień.
    -   Transparent Data Encryption (TDE).
-   **Dynamic Data Masking**:
    -   Ukrywanie danych dla wybranych użytkowników.
-   **Row-Level Security (RLS)**:
    -   Kontrola dostępu na poziomie wiersza.
-   **Audyt i monitorowanie**:
    -   Database Audit Specification.
    -   Extended Events i profilowanie SQL Server.

* * * * *

### **5\. Replikacja i HA (High Availability)**

-   **Replikacja**:
    -   Snapshot, transactional, merge replication.
-   **Always On Availability Groups**:
    -   Konfiguracja i zarządzanie.
    -   Failover.
-   **Backup i Restore**:
    -   Typy kopii zapasowych: full, differential, transaction log.
    -   Recovery Models (Simple, Full, Bulk-logged).
-   **Log Shipping i Mirroring**:
    -   Porównanie metod HA.

* * * * *

### **6\. Analiza danych i raportowanie**

-   **SQL Server Analysis Services (SSAS)**:
    -   Modele wielowymiarowe (OLAP) i tabelaryczne.
-   **SQL Server Reporting Services (SSRS)**:
    -   Tworzenie i publikowanie raportów.
    -   Parametryzowane raporty.
-   **Integration Services (SSIS)**:
    -   Tworzenie przepływów ETL.
    -   Obsługa błędów w pakietach SSIS.



### **Architektura SQL Server**

-   **Relacyjny silnik bazy danych (Database Engine)**:
    -   Obsługuje przechowywanie danych i wykonywanie zapytań SQL.
    -   Kluczowe komponenty: parser, optymalizator zapytań, bufor stron (Buffer Pool).
-   **Silnik pamięciowy (In-Memory OLTP)**:
    -   Technologia zwiększająca wydajność dla aplikacji OLTP.
    -   Funkcje: tabele w pamięci (`MEMORY_OPTIMIZED`), indeksy zoptymalizowane dla pamięci, transakcje zoptymalizowane pod kątem wielowątkowości.

* * * * *

### **Indeksy**

1.  **Rodzaje indeksów**:

    -   **Clustered** (klastrowany): dane w tabeli są fizycznie posortowane zgodnie z kluczem indeksu.
        -   Każda tabela może mieć tylko jeden indeks klastrowany.
    -   **Non-clustered** (nieklastrowany): dane są przechowywane oddzielnie od indeksu.
        -   Można mieć wiele indeksów nieklastrowanych na tabeli.
    -   **Columnstore**: zoptymalizowany dla zapytań analitycznych (OLAP), przechowuje dane w kolumnach zamiast wierszy.
        -   Dobry wybór dla hurtowni danych.
    -   **Full-text**: indeks umożliwiający wyszukiwanie tekstu w kolumnach.
    -   **Filtered Index**: indeks na podzbiorze danych (np. dla kolumn z wartością `NULL`).
2.  **Strategie indeksowania**:

    -   Używanie indeksów obejmujących (covering indexes), aby zmniejszyć konieczność dostępu do danych bazowych.
    -   Tworzenie indeksów dla zapytań często używanych w aplikacjach.
    -   Utrzymanie równowagi między liczbą indeksów a ich wpływem na operacje `INSERT`, `UPDATE`, `DELETE`.
3.  **Analiza fragmentacji indeksów**:

    -   **Fragmentacja logiczna**: różnica między kolejnością logiczną a fizyczną danych.
    -   **Fragmentacja fizyczna**: rozrzucenie stron danych na dysku.
    -   Analiza fragmentacji:

        sql

        Skopiuj kod

        `SELECT
            index_id,
            avg_fragmentation_in_percent
        FROM sys.dm_db_index_physical_stats (DB_ID(), OBJECT_ID('TableName'), NULL, NULL, 'DETAILED');`

    -   Reorganizacja (`ALTER INDEX ... REORGANIZE`) lub odbudowa (`ALTER INDEX ... REBUILD`) indeksów w celu redukcji fragmentacji.

* * * * *

### **Filegroups i zarządzanie plikami bazy danych**

1.  **Filegroups**:

    -   Logiczna grupa plików bazy danych. Umożliwia lepsze zarządzanie danymi.
    -   Stosowane w przypadku dużych baz danych, np. do przechowywania danych historycznych w osobnych grupach plików.

    Przykład tworzenia filegroup:

    sql

    Skopiuj kod

    `ALTER DATABASE [MyDatabase]
    ADD FILEGROUP MyFileGroup;`

2.  **Migracja danych między filegroups**:

    -   Przeniesienie tabel do innego filegroup wymaga utworzenia tabeli na docelowym filegroup i skopiowania danych:

        sql

        Skopiuj kod

        `CREATE TABLE NewTable (Id INT) ON MyFileGroup;
        INSERT INTO NewTable SELECT * FROM OldTable;
        DROP TABLE OldTable;
        EXEC sp_rename 'NewTable', 'OldTable';`

* * * * *

### **Wydajność**

1.  **Plany wykonania zapytań (Query Execution Plans)**:

    -   **Wizualizacja**: przedstawia kroki, jakie SQL Server podejmuje w celu wykonania zapytania.
    -   Jak uzyskać plan:

        sql

        Skopiuj kod

        `SET STATISTICS TIME ON;
        SET STATISTICS IO ON;
        SELECT * FROM TableName;`

    -   **Typy planów**:
        -   **Estimated Execution Plan**: przewidywany plan na podstawie statystyk.
        -   **Actual Execution Plan**: rzeczywisty plan po wykonaniu zapytania.
    -   Identyfikacja kosztownych operacji: np. skanowanie tabel (Table Scan) zamiast wyszukiwania przez indeks (Index Seek).
2.  **Optymalizacja zapytań**:

    -   Użycie indeksów pokrywających.
    -   Redukcja liczby operacji sortowania (`ORDER BY`).
    -   Stosowanie filtrów ograniczających liczbę przetwarzanych wierszy.
3.  **Parametr sniffing**:

    -   SQL Server czasami "zapamiętuje" plan dla określonego parametru zapytania.
    -   Rozwiązania:
        -   Opcja `OPTION (RECOMPILE)` w zapytaniach.
        -   Użycie lokalnych zmiennych zamiast parametrów:

            sql

            Skopiuj kod

            `DECLARE @LocalParam INT = @Param;
            SELECT * FROM TableName WHERE Column = @LocalParam;`

4.  **TempDB**:

    -   TempDB jest używana do przechowywania tymczasowych tabel, zmiennych tabelarycznych i innych obiektów tymczasowych.
    -   Optymalizacja TempDB:
        -   Rozdzielenie plików TempDB na różne dyski.
        -   Unikanie nadmiernego używania tabel tymczasowych.

### **2\. Zaawansowane zapytania SQL**

* * * * *

### **1\. Podzapytania (Subqueries)**

-   **Rodzaje podzapytań**:

    1.  **Nieskorelowane (Uncorrelated)**:

        -   Niezależne od zapytania głównego.
        -   Przykład:

            sql

            Skopiuj kod

            `SELECT Name
            FROM Employees
            WHERE DepartmentID = (SELECT DepartmentID FROM Departments WHERE Name = 'HR');`

    2.  **Skorelowane (Correlated)**:

        -   Wykonywane dla każdego wiersza w zapytaniu głównym.
        -   Przykład:

            sql

            Skopiuj kod

            `SELECT e.Name
            FROM Employees e
            WHERE e.Salary > (SELECT AVG(Salary) FROM Employees WHERE DepartmentID = e.DepartmentID);`

-   **Użycie w klauzulach**:

    -   W klauzuli `WHERE`, `HAVING`, `SELECT`, czy `FROM` jako widok in-line.

* * * * *

### **2\. Common Table Expressions (CTE)**

-   **CTE** to tymczasowa nazwa zapytania używana w dalszej części instrukcji SQL.

-   **Zastosowania**:

    1.  **Podzapytań rekurencyjnych** (np. hierarchie).
    2.  Lepsza czytelność i zarządzanie zapytaniami.
-   **Przykład podstawowego CTE**:

    sql

    Skopiuj kod

    `WITH EmployeeCTE AS (
        SELECT Name, ManagerID, 1 AS Level
        FROM Employees
        WHERE ManagerID IS NULL
        UNION ALL
        SELECT e.Name, e.ManagerID, Level + 1
        FROM Employees e
        INNER JOIN EmployeeCTE cte ON e.ManagerID = cte.EmployeeID
    )
    SELECT * FROM EmployeeCTE;`

-   **Rekurencyjne CTE**: szczególnie przydatne w nawigacji po strukturach hierarchicznych, np. drzewach organizacyjnych.

* * * * *

### **3\. Pivot i Unpivot**

1.  **Pivot**: Zmienia wiersze na kolumny.

    -   Przykład:

        sql

        Skopiuj kod

        `SELECT *
        FROM (
            SELECT Year, Sales, Region
            FROM SalesData
        ) AS SourceTable
        PIVOT (
            SUM(Sales)
            FOR Region IN ([North], [South], [East], [West])
        ) AS PivotTable;`

2.  **Unpivot**: Zmienia kolumny na wiersze.

    -   Przykład:

        sql

        Skopiuj kod

        `SELECT Year, Region, Sales
        FROM PivotTable
        UNPIVOT (
            Sales FOR Region IN ([North], [South], [East], [West])
        ) AS UnpivotTable;`

* * * * *

### **4\. Dynamic SQL**

Dynamiczny SQL pozwala generować i wykonywać zapytania SQL w czasie rzeczywistym.

-   **Użycie**:

    -   Tworzenie elastycznych zapytań, gdzie parametry są nieznane w czasie kompilacji.
-   **Przykład**:

    sql

    Skopiuj kod

    `DECLARE @sql NVARCHAR(MAX) = 'SELECT * FROM ' + QUOTENAME(@TableName) + ' WHERE ' + @Column + ' = @Value';
    EXEC sp_executesql @sql, N'@Value INT', @Value = 100;`

-   **Zabezpieczenia**:

    -   Użycie `sp_executesql` zamiast zwykłego `EXEC` w celu ochrony przed SQL Injection.

* * * * *

### **5\. Operacje na dużych zbiorach danych**

-   **BULK INSERT**: Wczytywanie danych do tabeli z pliku.

    sql

    Skopiuj kod

    `BULK INSERT SalesData
    FROM 'C:\Data\sales.csv'
    WITH (FIELDTERMINATOR = ',', ROWTERMINATOR = '\n', FIRSTROW = 2);`

-   **OPENROWSET**: Wczytywanie danych z zewnętrznych źródeł.

    sql

    Skopiuj kod

    `SELECT *
    FROM OPENROWSET('SQLNCLI', 'Server=.;Trusted_Connection=yes;', 'SELECT * FROM Database.dbo.Table');`

-   **Funkcje okienkowe (Window Functions)**:\
    Operują na zestawach danych w ramach zapytania bez agregacji całego zestawu.

    1.  **ROW_NUMBER()**:

        sql

        Skopiuj kod

        `SELECT Name, ROW_NUMBER() OVER (PARTITION BY DepartmentID ORDER BY HireDate) AS RowNum
        FROM Employees;`

    2.  **LEAD(), LAG()**:\
        Pozwalają odczytać wartości z poprzednich lub następnych wierszy.

        sql

        Skopiuj kod

        `SELECT Name, Salary,
               LAG(Salary) OVER (ORDER BY HireDate) AS PrevSalary,
               LEAD(Salary) OVER (ORDER BY HireDate) AS NextSalary
        FROM Employees;`

* * * * *

### **6\. Hierarchiczne zapytania**

Hierarchie są często przechowywane w modelu z odwołaniem do klucza nadrzędnego.

-   **Przykład rekursji**:

    sql

    Skopiuj kod

    `WITH OrgHierarchy AS (
        SELECT EmployeeID, ManagerID, Name, 1 AS Level
        FROM Employees
        WHERE ManagerID IS NULL
        UNION ALL
        SELECT e.EmployeeID, e.ManagerID, e.Name, Level + 1
        FROM Employees e
        INNER JOIN OrgHierarchy h ON e.ManagerID = h.EmployeeID
    )
    SELECT * FROM OrgHierarchy;`

-   **Zastosowania**:

    -   Struktury organizacyjne.
    -   Zarządzanie drzewami kategorii w e-commerce.

### **3\. Programowanie w SQL**

* * * * *

### **1\. Funkcje użytkownika (User-Defined Functions - UDF)**

UDF to funkcje tworzone przez użytkowników, które pozwalają wykonywać operacje, przetwarzać dane i zwracać wyniki.

#### **Rodzaje funkcji:**

1.  **Funkcje skalarnie (Scalar Functions):**

    -   Zwracają pojedynczą wartość (np. liczba, ciąg znaków).
    -   Przykład:

        sql

        Skopiuj kod

        `CREATE FUNCTION GetFullName (@FirstName NVARCHAR(50), @LastName NVARCHAR(50))
        RETURNS NVARCHAR(100)
        AS
        BEGIN
            RETURN @FirstName + ' ' + @LastName;
        END;`

        Użycie:

        sql

        Skopiuj kod

        `SELECT dbo.GetFullName('John', 'Doe');`

2.  **Funkcje tabelaryczne (Table-Valued Functions):**

    -   Zwracają zestaw danych w formie tabeli.

    -   **Inline TVF** (prosta definicja):

        sql

        Skopiuj kod

        `CREATE FUNCTION GetEmployeesByDepartment (@DepartmentID INT)
        RETURNS TABLE
        AS
        RETURN (
            SELECT EmployeeID, Name
            FROM Employees
            WHERE DepartmentID = @DepartmentID
        );`

        Użycie:

        sql

        Skopiuj kod

        `SELECT * FROM dbo.GetEmployeesByDepartment(1);`

    -   **Wielokrotna instrukcja TVF (Multi-statement TVF):**

        sql

        Skopiuj kod

        `CREATE FUNCTION GetTopEmployees (@TopCount INT)
        RETURNS @EmployeeTable TABLE (EmployeeID INT, Name NVARCHAR(50))
        AS
        BEGIN
            INSERT INTO @EmployeeTable
            SELECT TOP (@TopCount) EmployeeID, Name
            FROM Employees
            ORDER BY Salary DESC;
            RETURN;
        END;`

#### **Deterministyczne i niedeterministyczne funkcje:**

-   **Deterministyczne:** Zawsze zwracają ten sam wynik dla tych samych wejść (np. proste obliczenia).
-   **Niedeterministyczne:** Wyniki mogą się różnić (np. funkcje zależne od czasu: `GETDATE()` nie może być używane w UDF).

* * * * *

### **2\. Procedury składowane (Stored Procedures)**

Procedury składowane to zestawy instrukcji SQL, które są przechowywane w bazie i mogą być wywoływane wielokrotnie.

#### **Podstawowa struktura procedury:**

sql

Skopiuj kod

`CREATE PROCEDURE GetEmployeeDetails
    @EmployeeID INT
AS
BEGIN
    SELECT *
    FROM Employees
    WHERE EmployeeID = @EmployeeID;
END;`

Wywołanie:

sql

Skopiuj kod

`EXEC GetEmployeeDetails @EmployeeID = 1;`

#### **Parametry wyjściowe:**

sql

Skopiuj kod

`CREATE PROCEDURE GetEmployeeName
    @EmployeeID INT,
    @EmployeeName NVARCHAR(50) OUTPUT
AS
BEGIN
    SELECT @EmployeeName = Name
    FROM Employees
    WHERE EmployeeID = @EmployeeID;
END;`

Wywołanie:

sql

Skopiuj kod

`DECLARE @Name NVARCHAR(50);
EXEC GetEmployeeName @EmployeeID = 1, @EmployeeName = @Name OUTPUT;
PRINT @Name;`

#### **Obsługa błędów w procedurach:**

-   **TRY...CATCH**:

    sql

    Skopiuj kod

    `CREATE PROCEDURE InsertEmployee
        @Name NVARCHAR(50), @DepartmentID INT
    AS
    BEGIN
        BEGIN TRY
            INSERT INTO Employees (Name, DepartmentID) VALUES (@Name, @DepartmentID);
        END TRY
        BEGIN CATCH
            PRINT 'Error: ' + ERROR_MESSAGE();
        END CATCH;
    END;`

#### **Automatyzacja zadań z procedurami:**

-   Procedury składowane mogą być używane w zadaniach automatycznych (np. SQL Server Agent).

* * * * *

### **3\. Transakcje i zarządzanie transakcjami**

Transakcje to grupy instrukcji SQL, które są wykonywane jako jedna operacja.

#### **Cechy ACID:**

-   **Atomicity:** Wszystko albo nic.
-   **Consistency:** Dane pozostają spójne.
-   **Isolation:** Transakcje są odizolowane od siebie.
-   **Durability:** Wyniki transakcji są trwałe.

#### **Transakcje w SQL:**

-   **BEGIN TRANSACTION**: Rozpoczyna transakcję.
-   **COMMIT**: Zatwierdza transakcję.
-   **ROLLBACK**: Cofanie transakcji w przypadku błędu.

Przykład:

sql

Skopiuj kod

`BEGIN TRANSACTION;
BEGIN TRY
    INSERT INTO Accounts (AccountID, Balance) VALUES (1, 1000);
    UPDATE Accounts SET Balance = Balance - 500 WHERE AccountID = 1;
    INSERT INTO Transactions (AccountID, Amount) VALUES (1, -`

### **4\. Zarządzanie dostępem i bezpieczeństwo w MS SQL Server**

* * * * *

### **1\. Autoryzacja i role użytkowników**

#### **Loginy i użytkownicy:**

-   **Loginy**: Umożliwiają dostęp do instancji SQL Server.

    -   Typy loginów:

        -   **Windows Authentication**: Integracja z Active Directory.
        -   **SQL Server Authentication**: Użytkownik i hasło przechowywane w SQL Server.

        Tworzenie loginu:

        sql

        Skopiuj kod

        `CREATE LOGIN JohnDoe WITH PASSWORD = 'StrongPassword123!';`

-   **Użytkownicy**: Związani z bazą danych, reprezentują login w tej bazie. Tworzenie użytkownika w bazie:

    sql

    Skopiuj kod

    `CREATE USER JohnDoeUser FOR LOGIN JohnDoe;`

#### **Role użytkowników:**

-   **Role serwera**: Przyznają uprawnienia do zarządzania instancją.

    -   Przykładowe role:
        -   `sysadmin`: Pełne uprawnienia administracyjne.
        -   `dbcreator`: Tworzenie baz danych.
        -   `securityadmin`: Zarządzanie loginami.
-   **Role bazy danych**: Uprawnienia ograniczone do konkretnej bazy.

    -   Przykładowe role:
        -   `db_owner`: Pełna kontrola nad bazą.
        -   `db_datareader`: Dostęp do odczytu.
        -   `db_datawriter`: Dostęp do zapisu.
    -   Dodawanie użytkownika do roli:

        sql

        Skopiuj kod

        `EXEC sp_addrolemember 'db_datareader', 'JohnDoeUser';`

#### **Role niestandardowe**:

-   Tworzenie niestandardowej roli:

    sql

    Skopiuj kod

    `CREATE ROLE CustomRole;
    GRANT SELECT ON TableName TO CustomRole;
    EXEC sp_addrolemember 'CustomRole', 'JohnDoeUser';`

* * * * *

### **2\. Uprawnienia**

#### **Przyznawanie uprawnień:**

-   **GRANT**: Nadanie uprawnienia.
-   **DENY**: Odbieranie uprawnienia, nawet jeśli użytkownik ma je z innego źródła.
-   **REVOKE**: Cofnięcie uprawnienia bez blokowania dostępu.

Przykłady:

sql

Skopiuj kod

`GRANT SELECT, INSERT ON Employees TO JohnDoeUser;
DENY DELETE ON Employees TO JohnDoeUser;
REVOKE INSERT ON Employees FROM JohnDoeUser;`

#### **Kontrola szczegółowa:**

-   Uprawnienia można przyznawać na różnych poziomach:
    -   Poziom bazy danych (np. `GRANT CONNECT`).
    -   Poziom schematu (np. `GRANT ALTER ON SCHEMA::Sales`).
    -   Poziom obiektów (np. `GRANT SELECT ON TableName`).

* * * * *

### **3\. Bezpieczeństwo na poziomie wiersza (Row-Level Security - RLS)**

RLS pozwala ograniczać dostęp do danych w wierszach na podstawie zasad.

#### **Implementacja RLS:**

1.  Tworzenie funkcji zwracającej `BIT`:

    sql

    Skopiuj kod

    `CREATE FUNCTION SecurityFilter (@UserName NVARCHAR(50))
    RETURNS TABLE
    AS
    RETURN SELECT 1 AS AllowAccess
    WHERE @UserName = SYSTEM_USER;`

2.  Tworzenie zasad (Policy):

    sql

    Skopiuj kod

    `CREATE SECURITY POLICY FilterPolicy
    ADD FILTER PREDICATE dbo.SecurityFilter(SYSTEM_USER) ON Employees;`

Efekt: Użytkownicy widzą tylko dane, które spełniają warunki funkcji.

* * * * *

### **4\. Transparent Data Encryption (TDE)**

TDE szyfruje pliki bazy danych, dziennika transakcji i kopie zapasowe, chroniąc dane w stanie spoczynku.

#### **Kroki konfiguracji TDE:**

1.  Tworzenie klucza głównego:

    sql

    Skopiuj kod

    `CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'StrongPassword123!';`

2.  Tworzenie certyfikatu:

    sql

    Skopiuj kod

    `CREATE CERTIFICATE MyCertificate WITH SUBJECT = 'Database Encryption';`

3.  Tworzenie klucza szyfrowania:

    sql

    Skopiuj kod

    `USE MyDatabase;
    CREATE DATABASE ENCRYPT`

### **5. Optymalizacja zapytań i wydajność w MS SQL Server**

------

### **1. Indeksy**

Indeksy są kluczowym elementem poprawy wydajności zapytań poprzez szybki dostęp do danych.

#### **Rodzaje indeksów**:

1. **Indeks klastrowany (Clustered):**

   - Fizyka danych w tabeli jest uporządkowana zgodnie z tym indeksem.

   - Każda tabela może mieć tylko jeden indeks klastrowany.

   - Tworzenie:

     ```
     sql
     
     
     Skopiuj kod
     CREATE CLUSTERED INDEX IX_EmployeeID ON Employees(EmployeeID);
     ```

2. **Indeks nieklastrowany (Non-Clustered):**

   - Tworzy oddzielną strukturę z odnośnikami do danych.

   - Tabela może mieć wiele indeksów nieklastrowanych.

   - Tworzenie:

     ```
     sql
     
     
     Skopiuj kod
     CREATE NONCLUSTERED INDEX IX_Name ON Employees(Name);
     ```

3. **Indeks złożony (Composite):**

   - Obejmuje wiele kolumn.

   - Przydatny w przypadku zapytań z warunkami filtrowania na więcej niż jednej kolumnie.

   - Tworzenie:

     ```
     sql
     
     
     Skopiuj kod
     CREATE NONCLUSTERED INDEX IX_Name_Department ON Employees(Name, DepartmentID);
     ```

4. **Indeks unikalny (Unique):**

   - Gwarantuje brak duplikatów.

   - Tworzenie:

     ```
     sql
     
     
     Skopiuj kod
     CREATE UNIQUE INDEX IX_Email ON Employees(Email);
     ```

5. **Indeks filtrowany (Filtered):**

   - Ogranicza indeks tylko do określonego podzbioru danych.

   - Tworzenie:

     ```
     sql
     
     
     Skopiuj kod
     CREATE NONCLUSTERED INDEX IX_ActiveEmployees ON Employees(Status) WHERE Status = 'Active';
     ```

#### **Analiza użycia indeksów:**

- Widok dynamiczny 

  ```
  sys.dm_db_index_usage_stats
  ```

   pozwala sprawdzić, jak indeksy są używane:

  ```
  sql
  
  
  Skopiuj kod
  SELECT * FROM sys.dm_db_index_usage_stats WHERE database_id = DB_ID('YourDatabase');
  ```

------

### **2. Plan wykonania zapytania (Execution Plan)**

#### **Uzyskiwanie planu wykonania:**

- W Management Studio: **"Include Actual Execution Plan"** (Ctrl+M).

- Z poziomu zapytania:

  ```
  sqlSkopiuj kodSET SHOWPLAN_TEXT ON; -- Wersja tekstowa
  SET SHOWPLAN_XML ON;  -- Wersja XML
  ```

#### **Kluczowe elementy planu:**

1. **Scan (pełne skanowanie):** Oznacza brak indeksu odpowiadającego zapytaniu.
2. **Seek (wyszukiwanie):** Korzystanie z indeksu.
3. **Sort, Hash Match:** Kosztowne operacje, warto ich unikać.
4. **Key Lookup:** Powoduje odczyt danych z tabeli po znalezieniu w indeksie — może być zoptymalizowany.

------

### **3. Statystyki**

#### **Rola statystyk:**

- SQL Server używa statystyk do wyboru najlepszego planu wykonania zapytania.
- Automatyczna aktualizacja statystyk może nie nadążać przy dużych zbiorach danych.

#### **Ręczna aktualizacja statystyk:**

```
sql


Skopiuj kod
UPDATE STATISTICS TableName WITH FULLSCAN;
```

#### **Sprawdzanie stanu statystyk:**

```
sql


Skopiuj kod
DBCC SHOW_STATISTICS ('TableName', 'IndexName');
```

------

### **4. Narzędzia do monitorowania wydajności**

#### **Dynamic Management Views (DMV):**

1. **Wyszukiwanie najwolniejszych zapytań:**

## **1. Zaawansowane techniki zarządzania danymi**

### **1. Widoki (Views)**

#### **Czym są widoki?**
- Widoki to zapisane zapytania SELECT, które zachowują się jak wirtualne tabele.
- Służą do uproszczenia zapytań, poprawy bezpieczeństwa i organizacji danych.

#### **Tworzenie widoku:**
```sql
CREATE VIEW ActiveEmployees
AS
SELECT EmployeeID, Name, Department
FROM Employees
WHERE Status = 'Active';
```

#### **Modyfikowalne widoki:**
- Widok można modyfikować, jeśli spełnia pewne warunki (np. odnosi się tylko do jednej tabeli).

Przykład:
```sql
UPDATE ActiveEmployees
SET Department = 'HR'
WHERE EmployeeID = 1;
```

#### **Widoki z schematem zabezpieczeń:**
- Widoki mogą ukrywać wrażliwe dane, np.:
```sql
CREATE VIEW PublicEmployeeInfo
AS
SELECT Name, Position
FROM Employees;
```

### **2. Złożone tabele (CTE)**

#### **Czym są CTE?**
- Common Table Expressions to tymczasowe wyniki zapytań, które można ponownie wykorzystać w jednym zapytaniu.

#### **Tworzenie CTE:**
```sql
WITH EmployeeHierarchy AS (
    SELECT EmployeeID, ManagerID, Name
    FROM Employees
    WHERE ManagerID IS NULL
    UNION ALL
    SELECT e.EmployeeID, e.ManagerID, e.Name
    FROM Employees e
    INNER JOIN EmployeeHierarchy eh
    ON e.ManagerID = eh.EmployeeID
)
SELECT * FROM EmployeeHierarchy;
```

### **3. Temporal Tables**

#### **Czym są Temporal Tables?**
- Funkcjonalność pozwalająca na przechowywanie historii zmian danych.

#### **Tworzenie tabeli temporalnej:**
```sql
CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    Name NVARCHAR(50),
    Position NVARCHAR(50),
    ValidFrom DATETIME2 GENERATED ALWAYS AS ROW START,
    ValidTo DATETIME2 GENERATED ALWAYS AS ROW END,
    PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
)
WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.EmployeesHistory));
```

#### **Zapytania na danych historycznych:**
```sql
SELECT * FROM Employees
FOR SYSTEM_TIME AS OF '2024-01-01';
```

---

## **2. Replikacja i HA (High Availability)**

### **1. Replikacja**

#### **Typy replikacji:**
- **Snapshot Replication:** Kopiuje cały zestaw danych w określonych interwałach czasowych.
- **Transactional Replication:** Synchronizuje zmiany w czasie rzeczywistym.
- **Merge Replication:** Obsługuje zmiany wprowadzone zarówno na serwerze głównym, jak i podrzędnym.

#### **Konfiguracja replikacji transakcyjnej:**
1. **Utworzenie publikacji:**
    ```sql
    EXEC sp_addpublication @publication = 'MyPublication', @status = 'active';
    EXEC sp_addarticle @publication = 'MyPublication', @article = 'Employees';
    ```
2. **Subskrypcja:**
    ```sql
    EXEC sp_addsubscription @publication = 'MyPublication', @subscriber = 'SubscriberServer', @destination_db = 'SubscriberDB';
    ```

### **2. Always On Availability Groups**

#### **Czym są?**
- Mechanizm zapewniający wysoką dostępność i odzyskiwanie danych po awarii.
- Wymaga SQL Server Enterprise Edition.

#### **Konfiguracja Always On:**
1. Włączenie funkcji Always On:
   - W SQL Server Configuration Manager.
2. Tworzenie grupy dostępności:
   ```sql
   CREATE AVAILABILITY GROUP MyAG
   FOR DATABASE MyDatabase
   REPLICA ON
       'PrimaryReplica' WITH (ENDPOINT_URL = 'TCP://Primary:5022', AVAILABILITY_MODE = SYNCHRONOUS_COMMIT),
       'SecondaryReplica' WITH (ENDPOINT_URL = 'TCP://Secondary:5022', AVAILABILITY_MODE = SYNCHRONOUS_COMMIT);
   ```

---

## **3. Programowanie w SQL**

### **1. Funkcje użytkownika (User-Defined Functions - UDF)**

#### **Rodzaje funkcji:**
1. **Funkcje skalarnie (Scalar Functions):**
   - Zwracają pojedynczą wartość.
   ```sql
   CREATE FUNCTION GetFullName (@FirstName NVARCHAR(50), @LastName NVARCHAR(50))
   RETURNS NVARCHAR(100)
   AS
   BEGIN
       RETURN @FirstName + ' ' + @LastName;
   END;
   ```

2. **Funkcje tabelaryczne (Table-Valued Functions):**
   - Zwracają zestaw danych w formie tabeli.
   ```sql
   CREATE FUNCTION GetEmployeesByDepartment (@DepartmentID INT)
   RETURNS TABLE
   AS
   RETURN (
       SELECT EmployeeID, Name 
       FROM Employees 
       WHERE DepartmentID = @DepartmentID
   );
   ```

### **2. Procedury składowane (Stored Procedures)**

#### **Struktura procedury:**
```sql
CREATE PROCEDURE GetEmployeeDetails
    @EmployeeID INT
AS
BEGIN
    SELECT * 
    FROM Employees 
    WHERE EmployeeID = @EmployeeID;
END;
```

#### **Obsługa błędów:**
- **TRY...CATCH:**
  ```sql
  CREATE PROCEDURE InsertEmployee
      @Name NVARCHAR(50), @DepartmentID INT
  AS
  BEGIN
      BEGIN TRY
          INSERT INTO Employees (Name, DepartmentID) VALUES (@Name, @DepartmentID);
      END TRY
      BEGIN CATCH
          PRINT 'Error: ' + ERROR_MESSAGE();
      END CATCH;
  END;
  ```

### **3. Transakcje i zarządzanie transakcjami**

#### **Transakcje w SQL:**
```sql
BEGIN TRANSACTION;
BEGIN TRY
    INSERT INTO Accounts (AccountID, Balance) VALUES (1, 1000);
    UPDATE Accounts SET Balance = Balance - 500 WHERE AccountID = 1;
    INSERT INTO Transactions (AccountID, Amount) VALUES (1, -500);
    COMMIT;
END TRY
BEGIN CATCH
    ROLLBACK;
    PRINT ERROR_MESSAGE();
END CATCH;
```

---

## **4. Zarządzanie dostępem i bezpieczeństwo**

### **1. Loginy i użytkownicy:**
- Tworzenie loginu:
  ```sql
  CREATE LOGIN JohnDoe WITH PASSWORD = 'StrongPassword123!';
  ```
- Tworzenie użytkownika:
  ```sql
  CREATE USER JohnDoeUser FOR LOGIN JohnDoe;
  ```

### **2. Uprawnienia:**
- Przyznawanie uprawnień:
  ```sql
  GRANT SELECT, INSERT ON Employees TO JohnDoeUser;
  DENY DELETE ON Employees TO JohnDoeUser;
  REVOKE INSERT ON Employees FROM JohnDoeUser;
  ```

### **3. Bezpieczeństwo na poziomie wiersza (Row-Level Security):**
```sql
CREATE FUNCTION SecurityFilter (@UserName NVARCHAR(50))
RETURNS TABLE
AS
RETURN SELECT 1 AS AllowAccess
WHERE @UserName = SYSTEM_USER;

CREATE SECURITY POLICY FilterPolicy
ADD FILTER PREDICATE dbo.SecurityFilter(SYSTEM_USER) ON Employees;
```

---

## **5. Optymalizacja zapytań i wydajność**

### **1. Indeksy:**
- Tworzenie indeksu klastrowanego:
  ```sql
  CREATE CLUSTERED INDEX IX_EmployeeID ON Employees(EmployeeID);
  ```
- Tworzenie indeksu filtrowanego:
  ```sql
  CREATE NONCLUSTERED INDEX IX_ActiveEmployees ON Employees(Status) WHERE Status = 'Active';
  ```

### **2. Plan wykonania zapytania:**
- Włączanie planu wykonania:
  ```sql
  SET SHOWPLAN_TEXT ON;
  ```

### **3. Statystyki:**
- Aktualizacja statystyk:
  ```sql
  UPDATE STATISTICS TableName WITH FULLSCAN;
  ```

---