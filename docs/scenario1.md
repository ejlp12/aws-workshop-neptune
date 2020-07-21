## Explore Graph Data

We have loaded data, now we will explore the data with Gremlin.

# Scenario 1: Credit Card Fraud Detection Using Amazon Neptune

Credit card fraud is a wide-ranging term for theft and fraud committed using or involving a payment card, such as a credit card or debit card, as a fraudulent source of funds in a transaction.The purpose may be to obtain goods without paying, or to obtain unauthorized funds from an account. Credit card fraud is also an adjunct to identity theft. 

The sample dataset that we are referring here is consists of users, merchants and their credit card transactions.

![Credit Card Fraud Graph](https://raw.githubusercontent.com/abhmish/Neptune/master/img/fraudring.png)


## Query data using Apache TinkerPop Gremlin

We are going to use Apache TinkerPop Gremlin to query data through Jupyter Notebook.


### List Persons

```
g.V().hasLabel("Person").limit(5)
```

Output:
```
==>v[John]
==>v[Zoey]
==>v[Ava]
==>v[Olivia]
==>v[Mia]
```

### List Merchants

```
g.V().hasLabel("Merchant").limit(5)
```

Output:
```
==>v[Justice]
==>v[Sears]
==>v[Sprint]
==>v[Abercrombie]
==>v[Wallmart]
```

### List Persons and merchants where they have done transactions

````
g.V().hasLabel("Person").project("person","merchants").by("name").by(out().fold())
```

Output:
```
==>[person:John,merchants:[v[Justice],v[Sprint],v[American_Apparel],v[Just_Brew_It],v[Soccer_for_the_City]]]
==>[person:Zoey,merchants:[v[Abercrombie],v[MacDonalds],v[Just_Brew_It],v[Subway],v[Amazon]]]
==>[person:Ava,merchants:[v[Sears],v[Wallmart],v[American_Apparel],v[American_Apparel],v[Just_Brew_It]]]
==>[person:Olivia,merchants:[v[Wallmart],v[Just_Brew_It],v[Soccer_for_the_City],v[Soccer_for_the_City],v[Apple_Store],v[Urban_Outfitters],v[RadioShack],v[Macys]]]
==>[person:Mia,merchants:[v[Sears],v[MacDonalds],v[Soccer_for_the_City],v[Soccer_for_the_City],v[Starbucks],v[Amazon]]]
==>[person:Madison,merchants:[v[Abercrombie],v[Wallmart],v[MacDonalds],v[Subway],v[Apple_Store],v[Urban_Outfitters],v[RadioShack],v[Amazon],v[Macys]]]
==>[person:Paul,merchants:[v[Sears],v[Wallmart],v[Just_Brew_It],v[Starbucks],v[Apple_Store],v[Urban_Outfitters],v[RadioShack],v[Macys]]]
==>[person:Jean,merchants:[v[Abercrombie],v[Wallmart],v[Soccer_for_the_City],v[Subway],v[Amazon]]]
==>[person:Dan,merchants:[v[MacDonalds],v[MacDonalds],v[Soccer_for_the_City],v[Amazon]]]
==>[person:Marc,merchants:[v[Sears],v[Wallmart],v[American_Apparel],v[Soccer_for_the_City],v[Apple_Store],v[Urban_Outfitters],v[RadioShack],v[Amazon],v[Macys]]]

```

### Find all Merchants where fraud Transactions were done, along with list of Victims

```
g.V().
hasLabel("Merchant").
filter(__.inE().has("status","Disputed")).
project("merchant","persons").by("name").by(inE().outV().fold())

```

Output:
```
==>[merchant:Apple_Store,persons:[v[Paul],v[Marc],v[Olivia],v[Madison]]]
==>[merchant:Urban_Outfitters,persons:[v[Paul],v[Marc],v[Olivia],v[Madison]]]
==>[merchant:RadioShack,persons:[v[Paul],v[Marc],v[Olivia],v[Madison]]]
==>[merchant:Macys,persons:[v[Paul],v[Marc],v[Olivia],v[Madison]]] 
```

### Find all Undisputed transactions done prior to Disputed transactions

```
g.E().
hasLabel('HAS_BOUGHT_AT').has('status', 'Disputed').as('disputed').
outV().as('victim').
outE('HAS_BOUGHT_AT').has('status','Undisputed').as('undisputed').
filter(__.select('undisputed').where('undisputed',lt('disputed')).by('time')).as("t").inV().as("merchants").select("merchants","t","victim").
project("Victim","Merchants", "Amount","Time").
  by(__.select('victim').by('name')).
  by(__.select('merchants').by('name')).
  by(__.select('undisputed').by('amount')).
  by(__.select('undisputed').by('time')).dedup()
```

Output:
```
==>[Victim:Paul,Merchants:Sears,Amount:475,Time:Fri Mar 28 00:00:00 UTC 2014]
==>[Victim:Paul,Merchants:Wallmart,Amount:654,Time:Thu Mar 20 00:00:00 UTC 2014]
==>[Victim:Paul,Merchants:Just_Brew_It,Amount:986,Time:Thu Apr 17 00:00:00 UTC 2014]
==>[Victim:Paul,Merchants:Starbucks,Amount:239,Time:Thu May 15 00:00:00 UTC 2014]
==>[Victim:Madison,Merchants:Abercrombie,Amount:19,Time:Tue Jul 29 00:00:00 UTC 2014]
==>[Victim:Madison,Merchants:Wallmart,Amount:91,Time:Sun Jun 29 00:00:00 UTC 2014]
==>[Victim:Madison,Merchants:MacDonalds,Amount:630,Time:Mon Oct 06 00:00:00 UTC 2014]
==>[Victim:Madison,Merchants:Subway,Amount:352,Time:Tue Dec 16 00:00:00 UTC 2014]
==>[Victim:Madison,Merchants:Amazon,Amount:147,Time:Sun Aug 03 00:00:00 UTC 2014]
==>[Victim:Olivia,Merchants:Wallmart,Amount:231,Time:Sat Jul 12 00:00:00 UTC 2014]
==>[Victim:Olivia,Merchants:Just_Brew_It,Amount:742,Time:Tue Aug 12 00:00:00 UTC 2014]
==>[Victim:Olivia,Merchants:Soccer_for_the_City,Amount:74,Time:Thu Sep 04 00:00:00 UTC 2014]
==>[Victim:Olivia,Merchants:Soccer_for_the_City,Amount:924,Time:Sat Oct 04 00:00:00 UTC 2014]
==>[Victim:Marc,Merchants:Sears,Amount:430,Time:Sun Aug 10 00:00:00 UTC 2014]
==>[Victim:Marc,Merchants:Wallmart,Amount:964,Time:Sat Mar 22 00:00:00 UTC 2014]
==>[Victim:Marc,Merchants:American_Apparel,Amount:336,Time:Thu Apr 03 00:00:00 UTC 2014]
==>[Victim:Marc,Merchants:Soccer_for_the_City,Amount:11,Time:Thu Sep 04 00:00:00 UTC 2014]
==>[Victim:Marc,Merchants:Amazon,Amount:134,Time:Mon Apr 14 00:00:00 UTC 2014]
```

### Identify point of origin of Fraud

```
g.E(). 
hasLabel('HAS_BOUGHT_AT').has('status', 'Disputed').as('disputed'). 
outV().as('victim'). 
outE('HAS_BOUGHT_AT').has('status', 'Undisputed').as('undisputed').
filter(__.select('undisputed').where('undisputed', lt('disputed')).
by('time')).as("t").inV().as("merchants").
select("merchants","t","victim").group().
    by(__.select('merchants').coalesce(__.values('name'))).
    by(__.fold().project('Suspicious_Store', 'Count', 'Victims').
        by(__.unfold().select('merchants').coalesce(__.values('name'))).
        by(__.unfold().select('t').dedup().count()).
        by(__.unfold().select('victim').coalesce(__.values('name')).dedup().fold())).
unfold().select(values).dedup()
```

Output:
```
==>[Suspicious_Store:Sears,Count:2,Victims:[Paul,Marc]]
==>[Suspicious_Store:Wallmart,Count:4,Victims:[Paul,Madison,Olivia,Marc]]
==>[Suspicious_Store:Abercrombie,Count:1,Victims:[Madison]]
==>[Suspicious_Store:MacDonalds,Count:1,Victims:[Madison]]
==>[Suspicious_Store:Soccer_for_the_City,Count:3,Victims:[Olivia,Marc]]
==>[Suspicious_Store:Just_Brew_It,Count:2,Victims:[Paul,Olivia]]
==>[Suspicious_Store:Amazon,Count:2,Victims:[Madison,Marc]]
==>[Suspicious_Store:Starbucks,Count:1,Victims:[Paul]]
==>[Suspicious_Store:Subway,Count:1,Victims:[Madison]]
==>[Suspicious_Store:American_Apparel,Count:1,Victims:[Marc]]
```
