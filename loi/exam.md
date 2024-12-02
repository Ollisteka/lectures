##### Синтаксис

$S \rightarrow \backslash begin\{array\}\{P\}A\backslash end \{array\}$



###### Правила для преамбулы:

$\begin{array}{ll}P &\rightarrow XP'\\ P' &\rightarrow XP'|\lambda \\X&\rightarrow l|c|r \end{array}$



###### Правила для блока с содержимым:

$\begin{array}{ll}A &\rightarrow R|\lambda \\ R &\rightarrow CR' \\R'&\rightarrow \backslash\backslash R | \lambda \\ C &\rightarrow TC' \\ C' &\rightarrow \&C|\lambda \\ T &\rightarrow text \end{array}$



LL(1)-грамматика, потому что множества SELECT не пересекаются:

$Ann = \{A, P’, R’, C’\}$

| Нетерминал | FIRST                       | FOLLOW           |
| ---------- | --------------------------- | ---------------- |
| S          | \begin{array}               | $\dashv$         |
| P          | l, c, r                     | }                |
| P’         | l, c, r, $$\lambda$$        | }                |
| X          | l, c, r                     | l, c, r, }       |
| A          | text, $\lambda$             | \end{array}      |
| R          | text                        | \end{array}      |
| R’         | \\\, \end{array}, $\lambda$ | \end{array}      |
| C          | text                        | \\\, \end{array} |
| C’         | &, \end{array}, $\lambda$   | \\\, \end{array} |
| T          | text                        | \\\, \end{array} |




| Нетерминал | Правило   | SELECT           |
| ---------- | --------- | ---------------- |
| S          |           | \begin{array}    |
| P          | XP’       | l, c, r          |
| P’         | XP’       | l, c, r          |
| P’         | $\lambda$ | }                |
| X          | l         | l                |
| X          | c         | c                |
| X          | r         | r                |
| A          | R         | text             |
| A          | $\lambda$ | \end{array}      |
| R          | CR’       | text             |
| R’         | \\\R      | text             |
| R’         | $\lambda$ | \end{array}      |
| C          | TC’       | text             |
| C’         | &C        | &                |
| C’         | $\lambda$ | \\\, \end{array} |
| T          | text      | text             |

##### Семантика

В преамбуле хочу заполнять табличку (массив) $ALIGNMENT$ вида “номер колонки” — центрирование. Тогда в блоке с содержимым я смогу для каждого блока $text_{ij}$ сказать наверняка, что у этого блока должно быть центрирование $ALIGNMENT[j]$. А если такой записи в табличке нет, то колонок слишком много, и это ошибка. 

Также я хочу отслеживать число колонок в строчке, и если оно вдруг окажется меньше, чем длина $ALIGNMENT$, то это тоже ошибка

А все-все выравнивания можно хранить в таблице $TABLE[i,j]$;

| Синтаксис                                                    | Семантика                                                    | Стек                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------------------------------- |
| $S \rightarrow \backslash begin\{array\}\{P\}A\backslash end \{array\}$ | $\begin{array}{ll} P.colCount &=1 \end{array}$               |                                          |
| $P \rightarrow XP'$                                          | $\begin{array}{ll}X.i &= P.colCount \\P'.colCount &= P.colCount + 1 \end{array}$ |                                          |
| $P' \rightarrow XP_1'$                                       | $\begin{array}{ll}X.i &= P'.colCount \\P'_1.colCount &= P'.colCount + 1 \end{array}$ |                                          |
| $P' \rightarrow \lambda$                                     |                                                              |                                          |
| $X \rightarrow l|c|r$                                        | $ALIGNMENT[X.i] = l|c|r$                                     |                                          |
| $A \rightarrow R$                                            | $R.rowCount = 1$                                             | $val[ntop].row = 1$                      |
| $A \rightarrow \lambda$                                      |                                                              |                                          |
| $R \rightarrow CR'$                                          | $\begin{array}{ll}C.colCount &= 1 \\R'.rowCount = R.rowCount \end{array}$ | $val[ntop].col = 1$<br>rowCount остаётся |
| $R' \rightarrow \backslash\backslash R$                      | $R.rowCount = R'.rowCount + 1$                               |                                          |
| $R' \rightarrow \lambda$                                     |                                                              |                                          |
| $C \rightarrow TC'$                                          | $\begin{array}{ll}T.colCount &= C.colCount \\ C'.colCount &= C.colCount\end{array}$ | $val[ntop].col = val[top]$               |
| $C' \rightarrow \&C$                                         | $C.colCount = C'.colCount + 1$                               |                                          |
| $C' \rightarrow \lambda$                                     | Если $C'.colCount < len(ALIGNMENT)$ — ошибка, колонок меньше, чем объявлено в преамбуле. |                                          |
| $T \rightarrow text$                                         | Если $T.colCount > len(ALIGNMENT)$ — ошибка, колонок больше, чем объявлено в преамбуле.</br>Иначе — $T.align = ALIGNMENT[T.colCount]$ |                                          |

  

##### Анализатор

Так как это LL-1, L-атрибутная грамматика, то её можно анализировать методом рекурсивного спуска. А можно использовать стек.