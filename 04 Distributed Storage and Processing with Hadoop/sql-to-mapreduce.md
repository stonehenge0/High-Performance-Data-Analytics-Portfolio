Task: turn these SQL queries into a map-reduce paradigm (pseudocode)


1. A simple selection:
```SQL
SELECT fieldA FROM input.csv WHERE fieldB == something
```

```
map(ID,line):
    valA, valB = parser(line)

    if valB == something: 
        Emit(valA, None)

reduce(valA, values): # Reduce receives: A → [None, None, None, ...] 
    Emit(valA, None)
```

2. A summation:
```SQL
SELECT groupfield, SUM(fieldA) AS mysum FROM input.csv GROUP BY groupfield
```

```
map(ID,line): # key = ID, value = one row of that csv

    group, fieldA = extract(line)
    
    Emit(group, fieldA)


reduce(group, values):   # key = groupvalue, values = list of values. 

    total = 0
    for value in values:
        total+= value

    Emit(group, total)
```


3. A join:
```SQL
SELECT a.f1, b.f2 FROM tbl1.csv AS a JOIN tbl2.csv AS b ON a.id = b.id
```

```pseudocode

map(filename, line):

    if filename == "tbl1.csv":
        id, f1 = parse(line)
        Emit(id, ("A", f1))   # Tag as coming from file A

    else if filename == "tbl2.csv":
        id, f2 = parse(line)
        Emit(id, ("B", f2))   # Tag as coming from file B


reduce(id, tagged_values): # gets: id → [ ("A", 1_value), ... ]

    listA = []  
    listB = [] 

    for (tag, value) in tagged_values:
        if tag == "A":
            listA.append(value)
        else if tag == "B":
            listB.append(value)

    for a in listA:
        for b in listB:
            Emit(a, b)
```


## Notes:

**MAP**
1. Input almost always form: Map(ID, line)
2. Unpack values
3. return correct ones so that reduce can take them 

**REDUCE**
