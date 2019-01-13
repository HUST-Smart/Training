<p style="text-align: center; font-size: 32px; font-weight:bold;">
  Inventory Routing Problem
</p>



[TOC]



# Inventory Problem

## Known

### Set

| Set | Description                   | Size        | Element         | Remark                                                         |
| ---- | ---------------------- | ----------- | ------------ | ------------------------------------------------------------ |
| $V$ | **v**ehicle set | $[1, 3]$ | $v$ |               |
| $T$ | **t**imepoint set | $[4, 7]$ | $t, p$ | timepoint $t$ is the beginning of period $t + 1$ |
| $P$ | **p**eriod set | $T - \{0\}$ | $t, p$ | timepoint $p$ is the end of period $p$ |
| $N$ | **n**ode set | $[5, 200]$ | $n, m, s$ | $s$ is the supplier |
| $N'$ | customer set | $N - \{s\}$ | $n, m$ |  |

### Constant

| Constant | Description                    | Type | Range       | Remark |
| -------- | ------------------------------ | ---- | ----------- | ------ |
| $H_{n}$ | unit inventory **h**olding cost for node $n$ | real | $[0, 0.1]$ |  |
| $D_{nm}$ | **d**istance between node $n$ and $m$ | real |  | a.k.a. routing cost |
| $C_{n}$ | **c**apacity of node $n$ | real |  |  |
| $C_{v}$ | **c**apacity of vehicle $v$ | real |  |  |
| $I_{tn}$ | **i**nventory quantity held in node $n$ at timepoint $t$ if no delivery | real |  | also denoted by $ I_{pn}$ |


## Decision

| Variable     | Description                                                | Type | Domain     | Remark                                                     |
| -------------- | ------------------------------------------------------------ | ---- | ------------- | ------------------------------------------------------------ |
| $y_{vpn}$ | deliver**y** quantit**y** for customer $n$ at period $p$ by vehicle $v$ | real | $[0, \textrm{C}(v, n)]$ | $n \in N'$ |
| $y_{vps}$ | deliver**y** quantit**y** for supplier $s$ at period $p$ by vehicle $v$ | real | $[-\textrm{C}(v, s), 0]$ | |

### Convention and Function

- define function $\textrm{C}(v, n) = \min\{C_{n}, C_{v}\}$ to indicate the min capacity.
- define function $\textrm{y}(t, n) = \sum\limits_{v \in V} \sum\limits_{p = 1}^{t} y_{vpn}$ to indicate the cumulative delivery quantity to node $n$ until timepoint $t$.


## Objective

### minimize the holding cost **OHC (holding cost)**

the classical objective of the sub-problem.

$$
\min \sum_{n \in N} \sum_{p \in P} H_{n} \cdot (I_{pn} + \textrm{y}(p, n))
$$

### minimize the approximate routing cost **ORC (routing cost)**

the part of the objective of the original problem.

$$
?
$$


## Constraint

all of the following constraints must be satisfied.

- **HNC (node capacity)** the held inventory quantity should be none-negative at the end of each period and not exceed the node capacity at the beginning of each period.
  $$
  I_{tn} - I_{pn} \le I_{tn} + \textrm{y}(p, n) \le C_{n}, \quad \forall n \in N, \forall p \in P, \forall t \in T, t = p - 1
  $$

- **HQC (quantity consistency)** the loaded and total delivered quantity of the same vehicle should be equal in each period.
  $$
  \sum_{n \in N} y_{vpn} = 0, \quad \forall v \in V, \forall p \in P
  $$

- **HMD.O (max delivery)** the delivered quantity should not exceed the node capacity in each period.
  $$
  \sum_{v \in V} y_{vpn} \le C_{n}, \quad \forall n \in N, \forall p \in P
  $$

- **SEQ.OL (economic quantity)** the delivered quantity in each delivery should not be too small to reduce the number of delivery to reduce the routing cost.

- **SMV1.OL (max visit)** the number of visit to each customer should not exceed given number (e.g. $(I_{0n} - I_{|P|n} + C_{n}) / \min\{0.8 \cdot C_{n}, 0.2 \cdot C_{v}\}$).
- **SMV2.OL (max visit)** the number of visited customer (per vehicle) in each period should not exceed given number.

- **HVC (vehicle capacity)** ~~the carried inventory quantity should not exceed the vehicle capacity in any period.~~ (duplicated with the domain of $y_{vps}$)


## Note

time point and period notation.

```
P: |  1  |  2  |  3  |
T: 0     1     2     3
```



# Notation

there are several notations about the constraints.

- hard constraints
  - prefixed with **H** in the tag
  - constraints in the original problem that must be satisfied
- soft constraints
  - prefixed with **S** in the tag
  - objectives in the original problem
  - cut-off scores to the low-priority objectives in case the high-priority objectives are over dominant
- auxiliary constraints
  - prefixed with **A** in the tag
  - additional constraints for converting non-linear constraints into linear ones
- optional constraints
  - prefixed with **.O** in the tag
  - user cuts or constraints that are very likely to be ignored when changing requirement or implementation
- lazy constraints
  - suffixed with **.L** in the tag
  - constraints which are recommended to be added lazily
