$E \rightarrow E+T|T$
$T \rightarrow T*F|F$
$F \rightarrow (E)|x$



Схема трансляции для вычисления максимальной вложенности скобок

$S \rightarrow [E.ic = 0]E[S.max = E.max]$
$E \rightarrow [E_1.ic = E.ic]E_1+[T.ic = E.ic]T[E.max = MAX(E_1.max, T.max)]$ 
$E \rightarrow [T.ic = E.ic]T[E.val = T.val, E.max = T.max]$      
$T \rightarrow [T_1.ic = T.ic]T_1*[D.ic = T.ic]F[T.max = MAX(T_1.max, F.max)]$  
$T \rightarrow [F.ic = T.ic]F[T.val = F.val, T.max = F.max]$     
$F \rightarrow ([E.ic = F.ic + 1]E[F.max = E.max, F.val = E.val])$  
$F \rightarrow x[F.val = x.lexval, F.max = F.ic, x.nest = F.ic]$     

Устраним левую рекурсию:

$E \rightarrow TA$     
$A \rightarrow +TA$ 
$A \rightarrow \lambda $

$T \rightarrow FB$
$B \rightarrow *FB$
$B \rightarrow \lambda$

$F \rightarrow (E)|x$



Что с правилами:

$S \rightarrow [E.ic = 0]E[S.max = E.max]$

$E \rightarrow [T.ic = E.ic]T[A.ic = E.ic, A.iv = E.val]A[E.val = A.sv, E.max = MAX(T.max, A.max)]$     $A \rightarrow +[T.ic = A.ic]T[A_1.iv = A.iv + T.val]A_1[A.sv =A_1.sv, A.max = MAX(T.max, A_1.max)]$ 
$A \rightarrow \lambda [A.sv = A.iv, A.max = A.ic]$

$T \rightarrow [F.ic = T.ic]F[B.iv = F.val]B[T.val = B.sv, T.max = MAX(F.max, B.max)]$
$B \rightarrow *[F.ic = B.ic]F[B_1.iv = B.i * F.val]B_1[B.sv = B_1.sv, B.max = MAX(F.max, B_1.max)]$
$B \rightarrow \lambda[B.sv = B.iv, B.max B.ic]$



$F \rightarrow ([E.ic = F.ic + 1]E[F.max = E.max, F.val = E.val])$ 
$F \rightarrow x[F.val = x.lexval, F.max = F.ic, x.nest = F.ic]$     

Добавим маркеры:

$S \rightarrow [E.ic = 0]E[S.max = E.max]$

$E \rightarrow N[T.ic = E.ic]T[A.ic = E.ic, A.iv = E.val]A[E.val = A.sv, E.max = MAX(T.max, A.max)]$     

$N \rightarrow \lambda[N.sc = N.ic + 1]$

