# LP Duality, Lagrange Multipliers, and KKT Conditions

<details>
<summary>🇹🇷 Türkçe özet için tıklayın</summary>

Konveks optimizasyonun dört temel aracını (LP formülasyonu, dual problem türetimi, Lagrange çarpanları, KKT koşulları) kapsayan bir çalışma. Her soru hem elle yapılmış analitik çözüm hem de bir solver (CVXPY veya scipy) ile sayısal doğrulama içeriyor.

**Soru 1:** bir su dağıtım taşıma probleminin LP formülasyonu, standart forma dönüştürülmesi, analitik çözümü ve CVXPY ile doğrulanması, bir parametrenin çözüm üzerindeki etkisinin incelenmesi.
**Soru 2:** bir LP probleminin dual (eşlenik) formunun türetilmesi, CVXPY ile çözülmesi, bir kısıtın kaldırılmasının etkisi.
**Soru 3:** Lagrange çarpanlarıyla kısıtlı optimizasyon, SONC/SOSC (ikinci mertebe koşullar) ile yerel minimum doğrulaması, scipy ile çapraz kontrol.
**Soru 4:** KKT koşullarının çıkarımı, analitik çözüm, kısıtların aktifliğinin tartışılması, CVXPY ile doğrulama.

</details>

---

Four core tools from convex optimization, each solved by hand and then checked against a solver: an LP formulation, a dual problem derivation, Lagrange multipliers, and the KKT conditions.

## Question 1: water distribution transportation problem

A transportation problem set up as an LP, converted to standard form, solved analytically, and cross-checked with CVXPY. Also looks at how the solution shifts as one of the problem's parameters changes.

## Question 2: LP duality

Derives the dual of a given LP, solves it with CVXPY, and checks what happens to the solution when one of the original constraints is removed.

## Question 3: Lagrange multipliers

A constrained optimization problem solved with Lagrange multipliers, verified as a genuine local minimum using the second-order necessary and sufficient conditions (SONC/SOSC), then cross-checked numerically with scipy. Also compares the maximization and minimization versions of the same problem.

## Question 4: KKT conditions

Derives the KKT conditions for a constrained problem, solves it analytically, discusses which constraints are active at the solution, and verifies the result with CVXPY.

## Files

| File | What it is |
|---|---|
| `hw3_solution.ipynb` | Clean solution notebook, no run outputs |
| `hw3_solution_executed.ipynb` | Same notebook, executed, with all plots and solver outputs included |
| `report_final.md` / `report_final.pdf` | Written report with the full derivations |
| `q1_a_analysis.png`, `q3_contour.png`, `q4_contour.png` | Generated analysis plots |
| `YZV202E-HW3.pdf` | The original problem statement |

## Tools

Python, NumPy and Matplotlib for the numerical parts, CVXPY and scipy for solver-based verification. The derivations themselves are worked by hand.
