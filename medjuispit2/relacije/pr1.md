## Zadane relacije

### r(A, B, C)

| A    | B    | C    |
|------|------|------|
| 1    | X    | ab   |
| 2    | NULL | NULL |
| NULL | X    | ab   |
| 4    | Z    | NULL |
| 4    | Z    | ac   |
| 3    | Y    | aa   |
| 2    | NULL | bb   |

---

### s(A, B, C, D)

| A    | B    | C    | D    |
|------|------|------|------|
| 4    | Z    | ac   | 1.2  |
| NULL | Z    | NULL | 0.5  |
| 3    | NULL | aa   | NULL |
| 2    | NULL | bb   | 0.2  |
| NULL | X    | ab   | NULL |
| 1    | X    | ab   | 5.3  |
| 1    | X    | ac   | NULL |

---

### t(D, E)

| D    | E    |
|------|------|
| 5.3  | NULL |
| NULL | 4.6  |
| 4.6  | 1    |
| 5.3  | 4    |

---

## a) Izračun izraza  
\[
\pi_D(r ⋈ s) \cap \pi_D(t)
\]

---

### Mogući parovi r ⋈ s (spajanje po A, B, C)

#### r(1, X, ab)

- s(1, X, ab, 5.3)  
  → A, B, C potpuno jednaki → **spajaju se**

- s(1, X, ac, NULL)  
  → C se razlikuje (ab ≠ ac) → **ne spajaju se**

---

#### r(2, NULL, NULL)

- s(2, NULL, bb, 0.2)  
  → C: NULL ≠ bb  
  → poređenje s NULL daje **UNKNOWN** → **nema spajanja**

---

#### r(NULL, X, ab)

- s(NULL, X, ab, NULL)  
  → A = NULL u oba reda  
  → uslov `A = A` daje **UNKNOWN** → **ne spajaju se**

---

#### r(4, Z, NULL)

- s(4, Z, ac, 1.2)  
  → C: NULL ≠ ac → **nema spajanja**

---

#### r(4, Z, ac)

- s(4, Z, ac, 1.2)  
  → A, B, C se poklapaju → **spajaju se**

---

#### r(3, Y, aa)

- nijedan red u `s` nema istovremeno  
  A = 3, B = Y, C = aa  
  → **nema spajanja**

---

#### r(2, NULL, bb)

- s(2, NULL, bb, 0.2)  
  → A, B, C se poklapaju → **spajaju se**

---

### Rezultat r ⋈ s (posmatramo samo atribut D)

- (1, X, ab) ⋈ (1, X, ab, 5.3) → D = **5.3**
- (4, Z, ac) ⋈ (4, Z, ac, 1.2) → D = **1.2**
- (2, NULL, bb) ⋈ (2, NULL, bb, 0.2) → D = **0.2**

\[
\pi_D(r ⋈ s) = \{5.3,\;1.2,\;0.2\}
\]

---

### Projekcija πD(t)

Vrijednosti D u relaciji `t`:

- 5.3
- NULL
- 4.6
- 5.3

Skup bez duplikata:

\[
\pi_D(t) = \{5.3,\;4.6,\;NULL\}
\]

---

### Presjek

\[
\{5.3, 1.2, 0.2\} \cap \{5.3, 4.6, NULL\} = \{5.3\}
\]

---

### ✅ Odgovor (a)

\[
\pi_D(r ⋈ s) \cap \pi_D(t) = \{5.3\}
\]

---

## b) SQL upit

```sql
SELECT SUM(D), COUNT(DISTINCT A)
FROM s
WHERE C <> 'ac';
