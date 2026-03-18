# Pict Constraints Grammar

```
Constraints     ::=
  Constraint
| Constraint Constraints

Constraint      ::=
  IF Predicate THEN Predicate ELSE Predicate;
| IF Predicate THEN Predicate;
| Predicate;

Predicate       ::=
  Clause
| Clause LogicalOperator Predicate

Clause          ::=
  Term
| ( Predicate )
| NOT Predicate

Term            ::=
  ParameterName Relation Value
| ParameterName LIKE PatternString
| ParameterName NOT LIKE PatternString
| ParameterName IN { ValueSet }
| ParameterName NOT IN { ValueSet }
| ParameterName Relation ParameterName

ValueSet        ::=
  Value
| Value, ValueSet

LogicalOperator ::=
  AND
| OR

Relation        ::=
  =
| <>
| >
| >=
| <
| <=

ParameterName   ::= [String]

Value           ::=
  "String"
| Number

String          ::= whatever is typically regarded as a string of characters

Number          ::= whatever is typically regarded as a number

PatternString   ::= string with embedded special characters (wildcards):
                        * a series of characters of any length (can be zero)
                        ? any one character
```
