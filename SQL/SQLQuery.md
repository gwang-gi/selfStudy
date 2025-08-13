# 1. 기본
## 1)	SELECT * FROM emp;
--> emp 테이블에서 모든 칼럼에 대해 데이터 가져오기
## 2)	SELECT ename, job, sal FROM emp;
--> Emp 테이블에서 ename, job, sal칼럼들에 있는 데이터만 가져오기
## 3)	SELECT * FROM emp WHERE job=’SALESMAN’;
-->모든칼럼 정보 가져오는데 job이 SALESMAN인것들만
## 4)	AND 사용
SELECT * 
FROM emp
WHERE job=’SALESMAN’
AND sal>1300;
--> emp테이블에서 job이 ‘SALESMAN’이고 sal가 1300초과인 데이터들만
## 5)	<LIKE 사용>
SELECT ename, job, sal
FROM emp
WHERE ename LIKE ‘%A’;

### LIKE와 %, _: 
1) %: 0개 이상아무문자
2) _: 정확히 한글자

### 예시:

%A%: A가 있는 문자열 o

_A%: 두번째글자가 A인 문자열

_A_: 두번째 글자가 A이고 3글자로 이루어진 문자열


## 6)	'IS NOT NULL' 사용
SELECT * 
FROM emp
WHERE ename LIKE ‘_A%’
AND comm IS NOT NUL;
--> comm이 NULL값이면 안됨

## 7)	'AS' 사용
SELECT ename AS ‘Employee Name’, job, sal, sal * 1.2 AS ‘New SAL’
FROM emp;

출력할 때 칼럼 이름을 바꿈

## 8)	|| 사용
SELECT ename || “ earns” || “ “ || sal AS earnings
FROM emp;
 

## 9)	DISTINCT 사용
SELECT DISTINCT job
FROM emp;
 

## 10)	ORDER BY 사용
SELECT * FROM Products
ORDER BY ProductName;

SELECT * FROM Products
ORDER BY ProductName DESC; 역순

SELECT * FROM Customers
ORDER BY Country ASC, CustomerName DESC;

# 2.	내장 함수
## 종류: AVG(), MIN(), MAX(), SUM(), COUNT()
## 예시:
SELECT AVG(SAL) AS “AVERAGE SAL”
FROM emp;

## 예시 2:
SELECT MAX(SAL) AS ‘AVERAGE SAL’
FROM emp;

## 심화: 내장 함수 + ‘GROUP BY’
예시:
SELECT DEPTNO, COUNT(*) AS EMP_COUNT
FROM emp
GROUP BY DEPTNO; 

## 심화 2: For each department, find the number of employees who earn more than £1000
SELECT DEPTNO, COUNT(SAL) AS ‘EARN MORE THAN 1K’
FROM emp
WHERE SAL> 1000
GROUP BY DEPTNO;

## 심화 3: 내장함수 + GROUP BY + HAVING
HAVING은 GROUP BY이후에 또 거르는 역할
### 예시:
-- 부서별 평균 급여가 2000 이상인 부서
SELECT deptno, AVG(sal) AS avg_sal
FROM emp
GROUP BY deptno
HAVING AVG(sal) >= 2000;

## 심화 4: 다중 GROUP BY
SELECT Deptno, Job, COUNT(Empno) AS "#e"
FROM Emp
GROUP BY Deptno, Job;





# 3.	JOIN 두개의 다른 테이블을 결합
## 1)	테이블 이름 줄이기 (재선언):
FROM emp e --> emp 테이블이름을 e로 변경

## 2)	Inner, Outer JOIN
의미: 두개의 다른 테이블을 합쳐서 출력

INNER = 교집합

LEFT = 왼쪽 + 교집합

RIGHT = 오른쪽 + 교집합

FULL = 합집합

## 3)	INNER JOIN: 부서 이름이 있는 직원 리스트만 뽑기

### Employee
  
#### Ename	Deptno

Alice	10

Bob	20

Charlie	30

Diana	40


### Department
  
#### Deptno	Dname

10	Sales

20	HR

50	IT

SELECT *

FROM Employee e

INNER JOIN Department d

ON e.Deptno = d.Deptno;


## 4)	OUTER JOIN: LEFT OUTER JOIN을 사용하여 모든 학생과, 해당하는 수업명을 함께 출력하세요.

--> 수업이 없으면 NULL로 표시


### Student

#### SID	SName	ClassID

1	Alice	101

2	Bob	102

3	Charlie	105



### Class

#### ClassID	ClassName

101	Math

102	English

103	Science



SELECT s.SNAME, c.ClassName

FROM Student s

LEFT JOIN Class c

ON s.ClassID = c.ClassID;


## 5)	FULL JOIN: 

SELECT s.SName, c.ClassName

FROM Student s

FULL JOIN Class c

ON s.ClassID = c.ClassID;


## 6)	UNION: 합치기 기능

### SQLite는 LEFT JOIN만 지원

### FULL JOIN (지원 안 함)


SELECT *

FROM Dept FULL JOIN Emp

ON Emp.DeptNo = Dept.DeptNo;


### 대체 방법


SELECT *

FROM Emp LEFT JOIN Dept

ON Emp.DeptNo = Dept.DeptNo

UNION

SELECT *

FROM Dept LEFT JOIN Emp

ON Emp.DeptNo = Dept.DeptNo;

 



## 7)	Self JOIN: 하나의 테이블을 두 개로 복사한 것처럼 취급해서 조인하는 것


SELECT Slave.Ename AS Employee, Boss.Ename AS Manager

FROM Emp Slave

JOIN Emp Boss

ON Slave.Mgr = Boss.Empno;




















# 4.	Subquery 서브쿼리
## 1)	의미: 다른 SQL문 안에 포함된 SELECT문
## 2)	종류:
### --> 단일 값 반환:


SELECT name

FROM student

WHERE score > (SELECT AVG(score) FROM student); 값이 하나여서 비교연산자 사용


--> 다중 값 반환:

IN, ANY, ALL사용 (설명 나중에)

## 3)	위치: 보통 WHERE 이후에 많이 붙임


### 예시

SELECT ename,job 

FROM Emp

WHERE Deptno = (SELECT Deptno FROM Dept WHERE Dname='Sales');


## 4)	Nested SubQuery

--> 거미줄 처럼 이어질 수 있음

### 예시
SELECT Ename, Sal

FROM Emp

WHERE Sal > (SELECT Sal
             FROM Emp
             WHERE Job = 'Manager'
             AND Deptno = (SELECT Deptno
                           FROM Dept 
                           WHERE Dname='Sales’));
                           



## 5)	IN, NOT IN사용

의미: WHERE Deptno IN (10, 20, 30) --> OR 조건을 여러 개
  
## 6)	ANY 조건 만족하는 아무나
 
--> 매니저 중 월급 가장 낮은 사람보다 높으면 만족
## 7)	ALL 모든 조건 만족
 
--> 모든 매니져보다 월급이 높아야함




# 5.	UNION
## 1)	그냥 같은 칼럼에 합치는 기능
## 2)	조건: 같은 칼럼갯수 선택, 각각의 칼럼이 상대 테이블의 칼럼의 데이터타입과 같아야함
## 3)	예시:
 
### emp

#### Employee	No

SMITH	7369

ALLEN	7499


### dept

#### dname	deptno

ACCOUNTING	10

RESEARCH	20

SALES	30


UNION 

#### Employee	No

ACCOUNTING	10

ADAMS	7876

ALLEN	7499



# 6.	INTERSECT, EXCEPT

## 1)	INTERSECT:

--> 두 테이블의 공통 부분만 출력

t1의 C1: 1, 2, 3, 4

t2의 C1: 3, 4, 5, 6

  결과: 3, 4
  
## 2)	EXCEPT:

-->	공통 부분만 제외



# 7.	DATABASE의 시작, 작업
## 1)	SQL의 종류:
--> DML: Data Manipulation Language 건물 안에서 살림하기 (물건 넣기·빼기·바꾸기)
Ex: SELECT, INSERT, UPDATE, DELETE
--> DDL: Data Definition Language 테이블만들기
Ex: CREATE, ALTER, DROP

## 2)	DDL
### CREATE

#### <기본 형태>

CREATE TABLE users(

    user_id INTEGER,
    
    user_name VARCHAR(20),
    
    role VARCHAR(10)
    
)


#### <PK, FK 지정>

CREATE TABLE users(

    user_id INTEGER PRIMARY KEY,
    
    user_name VARCHAR(20),
    
    role VARCHAR(10),
    
    FOREIGN KEY (deptno) REFERENCES dept(deptno)
    
)


### DROP 테이블 삭제

    DROP TABLE users;


### ALTER

#### 1)	칼럼 추가

    ALTER TABLE person

    ADD First_Name VARCHAR(10);



#### 2)	칼럼 삭제

    ALTER TABLE Person

    DROP First_Name;



#### 3)	칼럼 데이터 타입 수정

    ALTER TABLE Person

    MODIFY EName CHAR(12);



#### 4)	칼럼 이름 수정

    ALTER TABLE Person

    RENAME COLUMN EName TO EmolyeeName;




### 3)	DML

#### SELECT (이미 배움)


#### INSERT:

##### 1)	행 추가

    INSERT INTO (테이블 이름)

    (칼럼이름) VALUES (해당 칼럼들 값);

##### 2)	테이블 복사

    INSERT INTO new_table (Name, ID)

    SELECT ENAME, EID

    FROM emp;

#### UPDATE:

##### 1)	구조:

    UPDATE 테이블명

    SET 컬럼명1 = 새로운값1, 컬럼명2 = 새로운값2, ...

    WHERE 조건;

##### 2)	예시:

    UPDATE Emp

    SET Job = 'MANAGER',

    Sal = Sal * 1.1,

    Comm = NULL,

    Deptno = 40

    WHERE ename = 'FORD';




##### DELETE: 행 삭제

###### 1)	구조:

    DELETE FROM (테이블 이름)

    WHERE (조건);)


###### 2)	심화: 모든 데이터 삭제(테이블은 유지)

    DELETE FROM (테이블 이름);







# 8.	COMMIT

## 1)	커밋이란?:

INSERT, UPDATE, DELETE 같은 변경 작업을 실행하면,

실제 데이터베이스 디스크에 바로 저장되지 않고, **메모리(Cache)**에 임시 저장됨.

## 2)	MySQL: MySQL 서버는 기본적으로 autocommit 모드가 켜져 있어서

INSERT, UPDATE, DELETE를 실행하면 자동으로 COMMIT 

## 3)	Autocommit 끄는 이유:

-> SQL문이 실행될 때마다 바로 저장

--> 중간에 한 쿼리라도 실패해도 이전 변경 내용은 이미 저장됨

--> 데이터 불일치, 잘못된 상태 발생 가능

### Ex: 주문과 결제 시스템

    START TRANSACTION;
    INSERT INTO 주문 ...
    INSERT INTO 결제정보 ...
    COMMIT;   
  --> 둘 다 성공해야 저장 (아니면 롤백)
  

## 4)	CREATE, ALTER, DROP같은 테이블 구조를 바꾸는건 자동 커밋

## 5)	트렌젝션
--> 자동으로 커밋 안할 때 쓰는 쿼리
--> 구조:

    BEGIN TRANSACTION;
    (원하는 쿼리)
    COMMIT; (OR ROLLEBACK;)
### 예시 (php file):

    <?php
    try {
        $pdo->exec("BEGIN TRANSACTION");
        $pdo->exec("INSERT INTO dept VALUES (50, 'Sales', 'Leicester')");
        $pdo->exec("UPDATE dept SET loc = 'London' WHERE deptno = 20");
        $pdo->exec("COMMIT");
    } catch (Exception $e) {
        $pdo->exec("ROLLBACK");
        echo "에러 발생: " . $e->getMessage();
    }
    ?>






















# 9.	VIEW
## 1)	VIEW란:
가상의 테이블을 만들어서 임시 저장하고 보여주는 기능
## 2)	사용하는 이유:
--> 편의성: 복잡한 쿼리 매번 사용X, VIEW로 저장된거 불러오면 됨
--> 보안: php파일에서 민감한 테이블의 데이터를 전부 노출될 위험이 적음
## 3)	특징: 실제 DB에서 테이블의 데이터가 변경되면 같이 변경됨
## 4)	구조: 

    CREATE VIEW “employee_info” AS
    SELECT e.user_id, e.user_name AS ‘name’, e.role, d.DNAME
    FROM emp e
    LEFT JOIN dept d
    ON e.deptno= d.deptno;
--> 이후에 php 파일에서도 재사용 가능

## 5)	원본 테이블 수정:

JOIN, GROUP BY, 집계(COUNT() 등)를 사용한 뷰테이블은 원본 테이블 수정 불가능

## 6)	심화:

--> 뷰를 이용한 뷰테이블을 만들 수 있음

## 7)	뷰 테이블 삭제:

    DROP VIEW users;
