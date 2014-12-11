---
layout: post
title: Naming things
---

### userid+timestamp as key for user requests

The proper key for user requests is simply the user ID and timestamp.

In a recent application, the original key chosen reflected some internal data
such as dates, target accounts, etc because I wanted to prevent inadvertent
repeat requests. 

This turned out to be clunky and inflexible - what if you want to change some
of that data? Your key now needs to change. Oops. So, keep the two things
separate - having a reasonable key and business logic.

### insert (not add)

To insert means to place an object into some sort of container. So the typical
usage is to add an object to a generic container:

    aContainer.insert( anObject )            -- Javascript
    aContainer->insert( anObject )           -- Perl

Avoid using "add" which implies an increase and does not imply any relation to
a container.

Definitions of **insert**, and **add**  in Webster 1913 Edition:

    Insert \In*sert"\, v. t. [imp. & p. p. {Inserted}; p. pr. & vb.
       n. {Inserting}.] [L. insertus, p. p. of inserere to insert;
       pref. in- in + serere to join, connect. See {Series}.]
       To set within something; to put or thrust in; to introduce;
       to cause to enter, or be included, or contained; as, to
       insert a scion in a stock; to insert a letter, word, or
       passage in a composition; to insert an advertisement in a
       newspaper.

             These words were very weakly inserted where they will
             be so liable to misconstruction.         --Bp.
                                                      Stillingfleet.

    Add \Add\, v. i.
       1. To make an addition. To add to, to augment; to increase;
          as, it adds to our anxiety. ``I will add to your yoke.''
          --1 Kings xii. 14.

       2. To perform the arithmetical operation of addition; as, he
          adds rapidly.

    Add \Add\, v. t. [imp. & p. p. {Added}; p. pr. & vb. n.
       {Adding}.] [L. addere; ad + dare to give, put. Cf. {Date},
       {Do}.]
       1. To give by way of increased possession (to any one); to
          bestow (on).

                The Lord shall add to me another son. --Gen. xxx.
                                                      24.

### create (not new)

To "create" means to create an object. It says nothing about the container. So
the typical usage is to create an object from some kind of template:

    anObject = new objectTemplate()             -- Javascript
    anObject = objectTemplate->new()            -- Perl

It is rather impossible to avoid using "new" because that word is baked into
most languages. So to avoid confusing other readers of our programs, use "new"

Definition of **create**, **new**  in Webster 1913 Edition:

    Create \Cre*ate"\, v. t. [imp. & p. p. {Created}; p. pr. & vb.
       n. {Creating}.]
       1. To bring into being; to form out of nothing; to cause to
          exist.

                In the beginning, God created the heaven and the
                earth.                                --Gen. i. 1.

### anObject (not someObject)

To name a single generic object, use anObject:

    anObject = ....
    aRequest = ...

Avoid someObject because it could also mean several objects.

### anObjectCollection (not aCollectionOfObjects)

Using anObjectCollection less wordy and the close association of the
collection followed by the verb requires less mental work e.g. insert into a
set, append to a list, remove from a String, pick from a :

    aRequestSet.insert( aRequest ) 
    aRequestList.append( aRequest )
    aHouseRow.extendBy( aHouse )
    aPearlString.remove( aPearl )
    aWordDictionary.pick( aWord )

Calling something aCollectionOfObject is more wordy and awkward and if of is
capitalized, it's even more awkward.

    aSetofRequests.insert( aRequest )
    aListofRequests.append( aRequest )
    aRowofHouses.extendBy( aHouse )
    aStringofPearls.remove( aPearl )
    aDictionaryofWords.pick( aWord )

