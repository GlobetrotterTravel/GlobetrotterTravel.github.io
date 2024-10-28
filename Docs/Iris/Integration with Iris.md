# Integration with Iris

This section deals with set up, maintenance and best practises when connecting `subscriber` systems to Iris.

## In this section

* [REST payload return](#rest-payload-return)
* [Display order](#display-order)
## REST payload return

Iris returns all responses in JSON format.

Each document is prefixed by a result key that contains an array of objects representing the records returned:

````json
{
  "result":
            [
              {...
````

To access individual results, deserialise the JSON and use `result[n].key...`

## Display order

When display order is specified in a record this is the intended display order for client systems. The Iris interface respects this display order, and it's recommended that `subscribers` take care to respect the display order as well.

Iris will return objects according to the following ordering rules in ascending order of priority:

* **Display order**: Where a display order is specified on a record, the records will be returned in that order
* **Policy order**: Where an object return is subject to an ordering policy (e.g. `hybrid list` `item` inclusion policy), the ordering will be as specified in the policy
* **Alphabetical order**: Where neither is specified, the object will be returned in ascending alphabetical order: first characters then integer numbers.

Deserialisation of this information can be performed knowing that the deserialised order is likely to be the correct, intended order for that object array.


