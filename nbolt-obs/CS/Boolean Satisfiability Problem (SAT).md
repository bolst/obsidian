
# Definition

> [!definition|*] Boolean Satisfiability Problem
> The **Boolean satisfiability problem** (sometimes known as SAT) asks if a given boolean formula's variables can be consistently replaced by true/false in order to make the entire formula true.
> 
> For example, $a \land \bar{b}$ is satisfiable since we can assign $a=true$ and $b=false$. Additionally, something like $a \land \bar{a}$ is not satisfiable.


# 3-SAT

3-SAT is a special case of SAT where each clause is limited to at most three literals, for example:
$$
(u_{1} \lor u_{2} \lor u_{3}) \land (\neg u_{1} \lor \neg u_{3} \lor u_{4}) \land \cdots $$
