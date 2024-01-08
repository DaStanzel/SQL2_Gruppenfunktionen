# SQL2_Gruppenfunktionen

# Arithmetische Ausdrücke

Arithmetische Ausdrücke in den `Select` List Items oder / und in der `WHERE` Klausel:

- Addition
- Subtraktion
- Multiplikation
- Division

Zu jedem Verkäufer soll der gesamte Gehalt ausgegeben werden.

```sql
SELECT ENAME, SAL, COMM, SAL + COMM
FROM EMP
WHERE JOB = 'SALESMAN'
```

Arithmetische und logische Ausdrücke können gemeinsam in der `WHERE` Klausel verwendet werden. 

Ausgabe aller Mitarbeiter, deren Provision über 25% ihres Gehaltes liegt.

```sql
SELECT ENAME, SAL, COMM
FROM EMP
WHERE COMM > 0.25 * SAL;
```

## Beispiele

### Beispiel 1
Ausgabe aller Verkäufer in absteigender Reihenfolge bezüglich Verhältnis Provision-Gehalt.

```sql
SELECT ENAME, COMM / SAL, COMM, SAL
FROM EMP
WHERE JOB = 'SALESMAN'
ORDER BY COMM / SAL DESC;
```

### Beispiel 2
Es soll der tägliche Gehalt (22 Arbeitstage im Monat) der Mitarbeiter aus Abteilung 30 berechnet werden. Dazu soll der berechnete Wert, der Wert gerundet auf Dollar und der Wert gerundet auf Cent ausgegeben werden.

```sql
SELECT ENAME, SAL, SAL / 22, ROUND(SAL / 22, 0), ROUND(SAL / 22, 2)
FROM EMP
WHERE DEPTNO = 30;
```

# Funktionen (kurzer Ausschnitt)

| Funktion   | Beispiel            | Ergebnis                                            |
|------------|---------------------|-----------------------------------------------------|
| `ABS`      | `ABS(BALANCE)`      | Absolutwert von `BALANCE`                           |
| `GREATEST` | `GREATEST(SAL,COMM)`| Größerer Wert von `SAL` und `COMM`                  |
| `LEAST`    | `LEAST(SAL,COMM)`   | Kleinerer Wert von `SAL` und `COMM`                 |
| `ROUND`    | `ROUND(SAL,2)`      | Gehalt auf 2 Dezimalstellen gerundet                |
| `TRUNC`    | `TRUNC(SAL,2)`      | Gehalt nach 2 Dezimalstellen abgeschnitten          |
| `TO_NUMBER`| `TO_NUMBER(GRADEPT)`| Character-Wert, der eine Zahl enthält, wird in einen numerischen Wert umgewandelt |

# Gruppenfunktionen

Bearbeiten eine Menge von Tupeln und liefern stets 1 Resultat für die betrachtete Gruppe.

| DEPTNO | SAL   |   |   |
|--------|-------|---|---|
| 10     | 5000  |   | `MAX(SAL)` <br/> ---------- <br/> 5000 |
| 30     | 2850  |   |   |
| 10     | 2450  |   |   |
| 20     | 2975  |   |   |
| 30     | 1250  |   |   |
| 30     | 1600  |   |   |
| 30     | 1500  |   |   |
| 30     | 950   |   | `SUM(SAL)` <br/> ---------- <br/> 10875 |
| 30     | 1250  |   |   |
| 20     | 3000  |   |   |
| 20     | 800   |   |   |
| 20     | 3000  |   |   |
| 20     | 1100  |   |   |
| 10     | 1300  |   |   |

## Wichtige Gruppenfunktionen

| Funktion | Beispiel       | Ergebnis                                   |
|----------|----------------|--------------------------------------------|
| `AVG`    | `AVG(SAL)`     | Durchschnittlicher Wert von `SAL`          |
| `COUNT`  | `COUNT(COMM)`  | Anzahl der Werte in der Spalte `COMM`      |
| `MAX`    | `MAX(SAL)`     | Höchster Wert von `SAL`                    |
| `MIN`    | `MIN(SAL)`     | Kleinster Wert von `SAL`                   |
| `SUM`    | `SUM(COMM)`    | Summenwert von `COMM`                      |

### Beispiel 1
```sql
SELECT

 AVG(SAL)
FROM EMP
WHERE JOB = 'CLERK';
```

### Beispiel 2
```sql
SELECT SUM(SAL), SUM(COMM)
FROM EMP
WHERE JOB = 'SALESMAN';
```

### Beispiel 3
```sql
SELECT 12 * AVG(SAL + COMM)
FROM EMP
WHERE JOB = 'SALESMAN';
```

## COUNT

Die `COUNT`-Funktion zählt entweder die Anzahl von Nicht-Null-Werten oder die Anzahl unterschiedlicher Werte oder die Anzahl der Zeilen der Query.

Die Zahl der Mitarbeiter, die eine Provision erhalten dürfen, wird mit folgendem `SELECT` Kommando ermittelt:

```sql
SELECT COUNT(COMM)
FROM EMP;
```

### Beispiel 1
Zahl der verschiedenen Jobs in Abteilung 30.

```sql
SELECT COUNT(DISTINCT JOB)
FROM EMP
WHERE DEPTNO = 30;
```

### Beispiel 2
Anzahl der Mitarbeiter in Abteilung 30.

```sql
SELECT COUNT(*)
FROM EMP
WHERE DEPTNO = 30;
```

## GROUP BY

- Ein Kommando, das mit `SELECT DEPTNO, AVG(SAL) FROM EMP…` beginnt, ist normalerweise ungültig. `DEPTNO` hat einen Wert für jede ausgewählte Zeile, `AVG(SAL)` hat einen einzigen Wert für das gesamte Query.
- Will man nun den durchschnittlichen Gehalt für jede Abteilung ermitteln, so könnte man für jede Abteilung je ein Query durchführen, um den durchschnittlichen Gehalt zu ermitteln.
- Diese Information kann aber auch mit einem Query ermittelt werden, indem die `GROUP BY` Klausel verwendet wird. Die `GROUP BY` Klausel unterteilt eine Tabelle in Gruppen mit gleichen Werten in den entsprechenden Spalten.

### Beispiel 1
Ausgabe der Durchschnittsgehälter in den Abteilungen.

```sql
SELECT DEPTNO, AVG(SAL)
FROM EMP
GROUP BY DEPTNO;
```

### Beispiel 2
Finde das jährliche Durchschnittsgehalt der Nicht-Führungskräfte in jeder Abteilung.

```sql
SELECT DEPTNO, 12 * AVG(SAL)
FROM EMP
WHERE JOB NOT IN ('MANAGER', 'PRESIDENT')
GROUP BY DEPTNO;
```

### Beispiel 3
Bestimme die Mitarbeiteranzahl und das jährliche Durchschnittsgehalts pro Abteilung und pro Job.

```sql
SELECT DEPTNO, JOB, COUNT(*), 12 * AVG(SAL)
FROM EMP
GROUP BY DEPTNO, JOB;
```

### Beispiel 4
Ausgabe des maximalen Durchschnittsgehalts der Beschäftigten über alle Abteilungen.

```sql
SELECT MAX(AVG(SAL))
FROM EMP
GROUP BY DEPTNO;
```

## HAVING Klausel

Genau wie mit der `WHERE` Klausel spezielle Zeilen ausgewählt werden können, können mit Hilfe der `HAVING` Klausel spezielle Gruppen ausgewählt werden.

### Beispiel 1
Durchschnittliche Jahresgehälter derjenigen Jobs, die von mehr als zwei Mitarbeitern ausgeführt werden.

```sql
SELECT JOB, COUNT(*), 12 * AVG(SAL)
FROM EMP
GROUP BY JOB
HAVING COUNT(*) > 2;
```

### Beispiel 2
Ausgabe aller Abteilungen mit wenigstens 2 Büroangestellten.

```sql
SELECT DEPTNO
FROM EMP
WHERE JOB = 'CLERK'
GROUP BY DEPTNO
HAVING COUNT(*) >= 2;
```
