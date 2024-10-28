# System journal

The system journal automatically records events that happen to the records in Iris. As records are created, updated, or deleted, journal entries are created and associated with the records that are modified. Journal entries are immutable and once created cannot be modified or deleted.

## Triggers

In general, a journal entry is created any time a record is created, modified or deleted. Different modules have different event triggers.

A journal entry records the following information:

* The record on which the event happened
* The service account under which the change was made
* The User Principal Name of the user that made the change (if supplied)
* The type of event (one of RecordCreate, RecordUpdate, RecordDelete)
* What happened (the subject)
* A description of the event, including a description of any property changes

