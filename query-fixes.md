# Adding a schema

```
WOQL.doctype("Person")            
  .label("Person Name")            
  .description("A Person Document")
	.property("name", "string").label("Name").cardinality(1)
	.property("date_of_birth", "date").label("Date Of Birth").cardinality(1)
	.property("knows", "Person").label("Knows")
```

# Insert Data

```
and(
  idgen("doc:Person",["Maria","1978-12-03"],"v:Maria"),
  idgen("doc:Person",["Anna","1974-02-10"],"v:Anna"),
  idgen("doc:Person",["Tom","1975-06-23"],"v:Tom"),
  idgen("doc:Person",["Jim","1974-07-20"],"v:Jim"),
 	
  insert("v:Maria", "Person").label("Maria")
  .property("date_of_birth", literal("1978-12-03",'date'))
  .property("name", "Maria")
  .property("knows", "v:Anna"),

  insert("v:Anna", "Person").label("Anna")
  .property("date_of_birth", literal("1974-02-10",'date'))
  .property("name", "Anna")
  .property("knows", "v:Tom"),

  insert("v:Tom", "Person").label("Tom")
  .property("date_of_birth", literal("1975-06-23",'date'))
  .property("name", "Tom")
  .property("knows", "v:Maria"),

  insert("v:Jim", "Person").label("Jim")
  .property("date_of_birth", literal("1974-07-20",'date'))
  .property("name", "Jim")
  .property("knows", "v:Tom")
)
```

# Add a new node type and relationship

In TerminusDB we create a new Document Object City

```
WOQL.doctype("City").label("City Name").description("A City name")
```

We add a new property `city_of_birth` in the Document Person with range type City. Here is our relationship between Person->City

```
add_property("city_of_birth","City").domain("Person")
```

Now update the data!!

```
and(
  idgen("doc:City",["Dublin"],"v:City_id"),
  insert("v:City_id", "City").label("Dublin"),
  triple('v:Person','type','scm:Person'),
  add_triple("v:Person","city_of_birth","v:City_id")
)
```

# Creating hierarchies

```
WOQL.doctype("Doctor")
  .parent("Person")
  .label("Doctor")
  .description("A Doctor is a person with patients")
  .property("patient", "Person")

WOQL.and(
  idgen("doc:Doctor",["Freud","1976-08-25"],"v:Freud"),
  insert("v:Freud", "Doctor").label("Freud")
  .property("date_of_birth", literal("1976-08-25",'date'})
  .property("name", "Freud")
  .property("patient","doc:Person_Maria_1978-12-03")
  .property("patient","doc:Person_Tom_1975-06-23")
       	
)
```

# Query the data
Understand how a person knows another

```
or(triple('v:Person', 'knows', 'v:OtherPerson'),
   triple('v:OtherPerson','knows','v:Person')
)
```

How do we get only people who know each other and are patients of doctor Freud?

```
and(
  triple('v:Person', 'knows', 'v:OtherPerson'),
  triple("v:Doc",'patient','v:Person'),
  triple("v:Doc",'patient','v:OtherPerson'),
  triple("v:Doc",'name','v:Freud')
)
```
